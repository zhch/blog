When a PC powers on, it initializes itself and then loads a boot loader from disk
into memory and executes it. The x86 paging hardware
is not enabled when the kernel starts; virtual addresses map directly to physical
addresses.
The boot loader loads the xv6 kernel into memory at physical address 0x100000.
The reason it doesn’t load the kernel at 0x80100000, where the kernel expects to find
its instructions and data, is that there may not be any physical memory at such a high
address on a small machine. The reason it places the kernel at 0x100000 rather than
0x0 is because the address range 0xa0000:0x100000 contains I/O devices.




最终内核线性地址空间和物理地址空间的 layout:

![](https://user-images.githubusercontent.com/1244560/49500113-dff05b00-f8aa-11e8-8464-70fe59645905.png)



说明：

- KERNBASE是内核虚拟地址的起始，为2G
- end位于main.c中
- DEVSPACE=0xFE000000




用户虚拟地址空间的layout：



![](![img](https://user-images.githubusercontent.com/1244560/49553580-5c2e8100-f933-11e8-80ae-dcb9a4644f97.jpg))









