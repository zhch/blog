https://checkoway.net/musings/linkers/

Basic Linker Operation

At this point we already know enough to understand the basic steps used by every linker.

- Read the input object files. Determine the length and type of the contents. Read the symbols.
- Build a symbol table containing all the symbols, linking undefined symbols to their definitions.
- Decide where all the contents should go in the output executable file, which means deciding where they should go in memory when the program runs.
- Read the contents data and the relocations. Apply the relocations to the contents. Write the result to the output file.
- Optionally write out the complete symbol table with the final values of the symbols.



An address space is simply a view of memory, in which each byte has an address. The linker deals with three distinct types of address space.

Every input object file is a small address space: the contents have addresses, and the symbols and relocations refer to the contents by addresses.

The output program will be placed at some location in memory when it runs. This is the output address space, which I generally refer to as using *virtual memory addresses*.





The output program will be loaded at some location in memory. This is the *load memory address*. On typical Unix systems virtual memory addresses and load memory addresses are the same.







Shared libraries can normally be run at different virtual memory address in different processes. A shared library has a *base address* when it is created; this is often simply zero. When the dynamic linker copies the shared library into the virtual memory space of a process, it must apply relocations to adjust the shared library to run at its virtual memory address. Shared library systems minimize the number of relocations which must be applied, since they take time when starting the program.





There is no logical requirement that the object file format resemble the executable file format. However, in practice they are normally very similar.





ELF:





Each ELF file is made up of one ELF header, followed by file data. The data can include:

- Program header table, describing zero or more [memory segments](https://en.wikipedia.org/wiki/Memory_segmentation)
- Section header table, describing zero or more sections
- Data referred to by entries in the program header table or section header table

The segments contain information that is needed for [run time](https://en.wikipedia.org/wiki/Run_time_(program_lifecycle_phase)) execution of the file, while sections contain important data for linking and relocation. Any [byte](https://en.wikipedia.org/wiki/Byte) in the entire file can be owned by one section at most, and orphan bytes can occur which are unowned by any section.



An object file segment contains one or more sections.