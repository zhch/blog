**The Video Graphics Array (VGA)** is an anolog computer display standard marketed in 1987 by IBM. It is called an "Array" because it was originally developed as a single chip, replacing dozens of logic chips in a Industry Standard Architecture (ISA) board that the MDA, CGA, and EGA used. Because this was all on a single ISA board, it was very easy to connect it to the motherboard.

The VGA consists of the video buffer, video DAC, CRT Controller, Sequencer unit, Graphics Controller, and an Attribute Controller.

Memory Mapped I/O (MMIO)

The processor can work with reading from RAM and ROM devices. In applications programming, this is something you never see. This is made possible with MMIO devices. Memory Mapped I/O allows a hardware device to map its own RAM or ROM into your processors physical address space. This allows the processor to be able to access hardware RAM or ROM in different ways by just using a pointer to that location in the address space. This is made possible because MMIO devices uses the same physical address and data bus that the processor and system memory uses.

It is important to remember, however, that Memory Mapped I/O is a mapping to the physical address space of the processor, not actual computer memory. In some architectures, it is possible to bank switch, or provide a method to switch between either using the MMIO device mapping or the system memory "hidden" behind it, while on others it is not. What this means for us is that we cannot access the actual system memory addresses that are "hidden" by the MMIO device. For example, CMOS RAM memory is mapped into the physical address space at address 0x400. This is different then main system memory; accessing 0x400 with a pointer will access the CMOS RAM memory always do to MMIO. It is not possible to access this location in system memory in the i86 architecture.

MMIO devices allows us to have more control over the hardware - it allows high resolution video displays with limited system memory, it allows us to obtain information from a device that is kept current by a battery (CMOS RAM) that would have altherwise been lost if in system memory. Another example of an MMIO device is the system BIOS ROM itself. MMIO is what allows the processor to execute the BIOS from ROM as it is mapped to the systems physical address space. Cool, huh?

Standard VGA
Video memory is stored inside of the video device; usually a video card or onboard video adapter. Standard VGA cards have 256 KB of VRAM. It is not uncommon however to see SVGA+ cards to have much more video memory however. 

We can see the Standard VGA memory resides in 0x000A0000 - 0x000BFFFF. 0xBFFFF - 0xA0000 = 0xA0000, which is 655360 bytes, or 640 KB.

When accessing video memory, you typically access it using a "window" into the real video RAM. This is typically:

0xA0000 - EGA/VGA graphics modes (64 KB)
0xB0000 - Monochrome text mode (32 KB)
0xB8000 - Color text mode and CGA (32 KB)

Super VGA
Super VGA and other display adapters typically do things differently. It is not uncommon to see a Super VGA or higher resolution display adapter to have VRAM mapped to a high address range.