
## 数据类型

### Declarations的结构总结

CPP的Declarations按语法顺序可以总结为5部分结构：

1. Optional prefix specifiers (e.g., static  or virtual )
2. A base type (e.g., vector<double>  or const int )
3. A declarator optionally including a name (e.g., p[7] , n , or ∗(∗)[] )
4. Optional suffix function specifiers (e.g., const  or noexcept )
5. An optional initializer or function body (e.g., ={7,5,3}  or {return x;} )


例子：

```C++
auto count = 1;
const double pi {3.1415926535897};
vector<string> people { name, "Skarphedin", "Gunnar" };
struct Date { int d, m, y; };
int day(Date∗ p) { return p−>d; }
template<class T> T abs(T a) { return a<0 ? −a : a; }
using Cmplx = std::complex<double>; // type alias
struct User; // type name
namespace NS { int a; }
```

### auto和decltype
* auto for deducing a type of an object from its initializer; the type can be the type of a variable,
  a const, or a constexpr.
* decltype(expr) for deducing the type of something that is not a simple initializer, such as the
  return type for a function or the type of a class member.

### Type Aliases
例子：

```c++
using Pchar = char∗; // pointer to character
using PF = int(∗)(double); // pointer to function taking a double and returning an int
template<class T>
class vector {
	using value_type = T; // every container has a value_type
	// ...
};
```

### Object的生命周期
分为以下5类：

1. Automatic
2. Static
3. Freestore
4. Temporary objects
5. Thread-local objects

### nullptr

### const和constexpr
• constexpr: Evaluate at compile time 
• const: Do not modify in this scope  

### enum class
对Plain Enum的改进，例子：

```c++
enum class Traffic_light { red, yellow, green };
```



### Statement

![](https://user-images.githubusercontent.com/1244560/50586161-4faa3380-0eb3-11e9-897d-d085f7a4395c.png)

### new和delete
It is often useful to create an object that exists independently of the scope in which it was created. For example, it is common to create objects that can be used after returning from the function in which they were created. The operator new creates such objects, and the operator delete can be used to destroy them. Objects allocated by new are said to be ‘‘on the free store’’ (also, ‘‘on the heap’’ or ‘‘in dynamic memory’’).

Operator new[]和delete[]分别用于动态分配和释放数组内存空间，例子：

```c++
MyClass * p1 = new MyClass[5]; // allocates and constructs five objects
MyClass * p2 = new (std::nothrow) MyClass[5]; 
...
delete[] p2;
delete[] p1;
```


#### Overloading new



### {}-lists

In addition to their use for initializing named variables (§6.3.5.2), {}-lists can be used as expressions in many (but not all) places. They can appear in two forms:

1. Qualified by a type, T{...}, meaning ‘‘create an object of type T initialized by T{...}’’;
2. Unqualified {...}, for which the the type must be determined from the context of use;

例子：

```c++
struct S { int a, b; };
struct SS { double a, b; };
void f(S); // f() takes an S
void g(S);
void g(SS); // g() is overloaded
void h()
{
	f({1,2}); // OK: call f(S{1,2})
	g({1,2}); // error : ambiguous
	g(S{1,2}); // OK: call g(S)
	g(SS{1,2}); // OK: call g(SS)
}
vector<double> v = {1, 2, 3.14};
```



#### Qulified/Unqualified Lists



### Named casts

- static_cast converts between related types such as one pointer type to another in the same class hierarchy, an integral type to an enumeration, or a floating-point type to an integral type. It also does conversions defined by constructors and conversion operators.

- reinterpret_cast handles conversions between unrelated types such as an integer to a pointer or a pointer to an unrelated pointer type
- const_cast converts between types that differ only in const and volatile qualifiers
- dynamic_cast does run-time checked conversion of pointers and references into a class hierarchy


These distinctions among the named casts allow the compiler to apply some minimal type checking and make it easier for a programmer to find the more dangerous conversions represented as reinterpret_casts. Some static_casts are portable, but few reinterpret_casts are. Hardly any guarantees are made for reinterpret_cast, but generally it produces a value of a new type that has the same bit pattern as its argument. If the target has at least as many bits as the original value, we can reinterpret_cast the result back to its original type and use it. The result of a reinterpret_cast is guaranteed to be usable only if its result is converted back to the exact original type. Note that reinterpret_cast is the kind of conversion that must be used for pointers to functions (§12.5). 

例子：

```c++
char x = 'a';
int∗ p1 = &x; // error : no implicit char* to int* conversion
int∗ p2 = static_cast<int∗>(&x); // error : no implicit char* to int* conversion
int∗ p3 = reinterpret_cast<int∗>(&x); // OK: on your head be it
struct B { /* ... */ };
struct D : B { /* ... */ }; // see §3.2.2 and §20.5.2
B∗ pb = new D; // OK: implicit conversion from D* to B*
D∗ pd = pb; // error : no implicit conversion from B* to D*
D∗ pd = static_cast<D∗>(pb); //OK
```



#### C-Style Cast

From C, C++ inherited the notation (T)e，建议不要使用。



#### Function-Style Cast

 The T(e)  construct is sometimes referred to as a function-style cast . Unfortunately, for a built-in type T , T(e)  is equivalent to (T)e  (§11.5.3). This implies that for many built-in types T(e)  is not safe.

例子：

```c++
void f(double d, char∗ p)
{
	int a = int(d); // truncates
	int b = int(p); // not portable
	// ...
}
```



~