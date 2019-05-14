## 内存对齐

### 举例

```C
struct S { short f[3]; } __attribute__ ((aligned (8)));
typedef int more_aligned_int __attribute__ ((aligned (8)));
```

Force the compiler to insure (as far as it can) that each variable whose type is struct S or more_aligned_int will be allocated and aligned at least on a 8-byte boundary（地址能被8整除）.

```C
struct S { short f[3]; } __attribute__ ((aligned));
```

Whenever you leave out the alignment factor in an aligned attribute specification, the compiler automatically sets the alignment for the type to the largest alignment that is ever used for any data type on the target machine you are compiling for. In the example above, if the size of each short is 2 bytes, then the size of the entire struct S type is 6 bytes. The smallest power of two that is greater than or equal to that is 8, so the compiler sets the alignment for the entire struct S type to 8 bytes.

### 详细文档

https://gcc.gnu.org/onlinedocs/gcc-8.2.0/gcc/Common-Type-Attributes.html#Common-Type-Attributes