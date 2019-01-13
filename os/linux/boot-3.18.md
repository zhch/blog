## 1 自检
系统加电后，进行自检。


## 2 实模式运行
系统首先进入实模式运行。实模式有20位地址线，可寻址地址空间为0 ~ 1MB（0xFFFFF）。但是寄存器只有16位，只能寻址64K地址空间，于是实模式也支持内存分段，寻址方式如下：

```
PhysicalAddress = Segment Selector * 16 + Offset
```

实模式逻辑地址所能寻址的最大物理地址为：

```
0xffff:0xffff = 0xffff * 16 + 0xffff = 0x10ffef = 1MB + 64KB - 16b
```

但是因为物理地址20位，所以实际能寻址的最大地址为 1MB。


## 3 BIOS启动
BIOS启动。

BIOS遍历所有可Boot设备，寻找Boot Sector。第一个Sector如果最后两字节为 0x55 和 0xaa ，则该Sector为Boot Sector。Boot Sector被载入到 0x7c00 ,并跳往该地址开始执行。此时内存Layout如下：

```
0x00000000 - 0x000003FF - Real Mode Interrupt Vector Table
0x00000400 - 0x000004FF - BIOS Data Area
0x00000500 - 0x00007BFF - Unused
0x00007C00 - 0x00007DFF - Our Bootloader
0x00007E00 - 0x0009FFFF - Unused
0x000A0000 - 0x000BFFFF - Video RAM (VRAM) Memory
0x000B0000 - 0x000B7777 - Monochrome Video Memory
0x000B8000 - 0x000BFFFF - Color Video Memory
0x000C0000 - 0x000C7FFF - Video ROM BIOS
0x000C8000 - 0x000EFFFF - BIOS Shadow Area
0x000F0000 - 0x000FFFFF - System BIOS
```

至此BIOS引导结束，开始Bootloader引导。


## 4 Bootloader(以GRUB2为例)引导
Linux使用专门的Bootloader管理启动后的最初阶段引导。常用Bootloader有GRUB2、syslinux、LILO等，下面以GRUB2为例。

Boot Sector由GRUB2生成，负责载入GRUB2 Core Image（包含文件系统支持），并执行grub_main()函数。grub_main函数负责：

- 初始化Console
- gets the base address for modules
- gets the base address for modules, sets the root device
- sets the root device
- loads/parses the grub configuration file
- loads/parses the grub configuration file, loads modules
- loads modules

### 载入Kernel
完成后开始执行grub-core/normal/main.c#grub_normal_execute()函数，显示菜单供选择需要引导的操作系统，用户选中菜单项后，开始执行 grub_menu_execute_entry() 函数，执行GRUB的boot命令，载入选中的操作系统。

根据Linux Boot Protocol要求，Linux Image载入到内存后，Bootloader需要负责初始化一个Kernel Setup Header，位于Linux Image的 0x01f1 offset 处，Header的内容定义：https://github.com/torvalds/linux/blob/v4.16/Documentation/x86/boot.txt#L156 。

根据Linux Boot Protocol要求，Bootloader载入Linux Image后，内存Layout如下：

```
For a modern bzImage kernel with boot protocol version >= 2.02, a memory layout like the following is suggested:

         | Protected-mode kernel  |
100000   +------------------------+
         | I/O memory hole        |
0A0000   +------------------------+
         | Reserved for BIOS      | Leave as much as possible unused
         ~                        ~
         | Command line           | (Can also be below the X+10000 mark)
X+10000  +------------------------+
         | Stack/heap             | For use by the kernel real-mode code.
X+08000  +------------------------+
         | Kernel setup           | The kernel real-mode code.
         | Kernel boot sector     | The kernel legacy boot sector.
       X +------------------------+
         | Boot loader            | <- Boot sector entry point 0x7C00
001000   +------------------------+
         | Reserved for MBR/BIOS  |
000800   +------------------------+
         | Typically used by MBR  |
000600   +------------------------+
         | BIOS use only          |
000000   +------------------------+

... where the address X is as low as the design of the boot loader
permits.
```

至此，Bootloader使命结束， 由Kernel负责继续引导，跳转到地址 X + sizeof(KernelBootSector) + 1 开始执行。


## 5 Kernel Setup
### 5.1 最初操作
Kernel Setup的最初几步操作：

- 设置Decompressor
- some memory management related things
- decompress the actual kernel and jump to it

开始执行start_of_setup：

- 设置Stack
- 设置Kernel BSS段
- 跳转到 arch/x86/boot/main.c#main()开始执行

### 5.2 复制boot parameters(copy_boot_params()函数)
复制Kernel Setup Header到arch/x86/include/uapi/asm/bootparam.h#boot_params数据结构 

### 5.3 初始化 Console(console_init()函数)

### 5.4 初始化Heap(init_heap()函数)
checks the CAN_USE_HEAP flag from the loadflags structure in the kernel setup header and calculates the end of the stack if this flag was set， or in other words stack_end = esp - STACK_SIZE.

Then there is the heap_end calculation:
```
heap_end = (char *)((size_t)boot_params.hdr.heap_end_ptr + 0x200);
```

Now the heap is initialized and we can use it using the GET_HEAP method. 

### 5.5 CPU检测(check_cpu()函数)

### 5.6 内存检测(detect_memory()函数)
Basically provides a map of available RAM to the CPU。BIOS 提供多个programming interfaces for memory detection like 0xe820, 0xe801 and 0x88,以0xE820为例，执行 arch/x86/boot/memory.c#detect_memory_e820():

1 First of all, initializes the biosregs structure as we saw above and fills registers with special values for the 0xe820 call:
```
initregs(&ireg);
ireg.ax  = 0xe820;
ireg.cx  = sizeof buf;
ireg.edx = SMAP;
ireg.di  = (size_t)&buf;
```
- ax contains the number of the function (0xe820 in our case)

- cx contains the size of the buffer which will contain data about the memory
- edx must contain the SMAP magic number
- es:di must contain the address of the buffer which will contain memory data
- ebx has to be zero.

2 Next is a loop where data about the memory will be collected. It starts with a call to the 0x15 BIOS interrupt, which writes one line from the address allocation table. For getting the next line we need to call this interrupt again (which we do in the loop). Before the next call ebx must contain the value returned previously:

    intcall(0x15, &ireg, &oreg);
    ireg.ebx = oreg.ebx;
3 Ultimately, this function collects data from the address allocation table and writes this data into the e820_entry array:

- start of memory segment

- size of memory segment
- type of memory segment (是usable 或者是 reserved)

You can see the result of this in the dmesg output, something like:
```
[0.000000] e820: BIOS-provided physical RAM map:
[0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[0.000000] BIOS-e820: [mem 0x0000000000100000-0x000000003ffdffff] usable
[0.000000] BIOS-e820: [mem 0x000000003ffe0000-0x000000003fffffff] reserved
[0.000000] BIOS-e820: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved

```

### 5.7 初始化键盘(keyboard_init()函数)

### 5.8 其他初始化操作
The next couple of steps are queries for different parameters.



## 6 切换到保护模式

保护模式摘要：

- 地址线由20位增加到32位
- 内存管理采用分段+分页模式

切换到保护模式的过程摘要如下：

- Set up the Interrupt Descriptor Table
- Disable interrupts
- Describe and load the GDT with the `lgdt` instruction
- Set the PE (Protection Enable) bit in CR0 (Control Register 0)
- Jump to protected mode code




READ TO：https://github.com/0xAX/linux-insides/blob/master/Booting/linux-bootstrap-4.md


~