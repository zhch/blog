




## highmem

| Category        | Type/Field/Function          | Summary                    |
| --------------- | ---------------------------- | -------------------------- |
|                 |                              |                            |
|                 |                              |                            |
|                 |                              |                            |
| PUBLIC FUNCTION | void page_address_init(void) | 初始化 page_address_htable |
|                 |                              |                            |
|                 |                              |                            |
|                 |                              |                            |



| Init/main.c: start_kernel | mm/highmem.c: page_address_init       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
| ------------------------- | ------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|                           | arch/x86/kernel/setup.c: setup_arch() | mm/memblock.c: memblock_reserve                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       | arch/x86/kernel/e820.c: e820__memory_setup               | struct x86_init_ops x86_init在arch/x86/kernel/x86_init.c中初始化 |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          | arch/x86/kernel/e820.c: e820__memory_setup_default           |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          | pr_info("BIOS-provided physical RAM map:\n");       e820__print_table(who); |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       | arch/x86/kernel/setup.c: e820_add_kernel_range           |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       | unsigned long max_pfn 在 include/linux/memblock.h 中声明 |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           | READ HERE: console_init();            |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |
|                           |                                       |                                                          |                                                              |      |      |      |      |      |      |      |      |      |      |      |      |



