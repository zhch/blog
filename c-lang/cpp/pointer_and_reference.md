###  什么是Reference
A reference variable is an alias, that is, another name for an already existing variable. A reference, like a pointer is also implemented by storing the address of an object.

Reference可以被理解为：a constant pointer (not to be confused with a pointer to a constant value!) with automatic indirection, i.e the compiler will apply the * operator for you.

### Pointer和Reference的差别比较

|             | 指针                           | 引用                                                         |
| ----------- | ------------------------------ | ------------------------------------------------------------ |
| 初始化      | 声明与初始化可以分开为多条语句 | 引用必须在声明时初始化                                       |
| 赋值        | 指针可以反复赋值               | 引用在初始化之后不能再次赋值，再次赋值修改的不是把引用指向另一个变量，而是修改了被引用变量的值 |
| nullptr     | 可以被赋值为nullptr            | 不允许赋值为nullptr                                          |
| Indirection | 可以实现多级Indirection        | 只能有一级Indirection，并且由编译器自动完成                  |
| 算术操作    | 可以完成各种指针算术操作       | there is no such thing called Reference Arithmetic.(but you can take the address of an object pointed by a reference and do pointer arithmetics on it as in &obj + 5) |





