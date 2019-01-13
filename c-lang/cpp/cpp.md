# CPP Summary

## 1 Declarations的结构总结
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

### 1.2  auto和decltype

* auto for deducing a type of an object from its initializer; the type can be the type of a variable,
  a const, or a constexpr.
* decltype(expr) for deducing the type of something that is not a simple initializer, such as the
  return type for a function or the type of a class member.


## 2 new和delete
It is often useful to create an object that exists independently of the scope in which it was created. For example, it is common to create objects that can be used after returning from the function in which they were created. The operator new creates such objects, and the operator delete can be used to destroy them. Objects allocated by new are said to be ‘‘on the free store’’ (also, ‘‘on the heap’’ or ‘‘in dynamic memory’’).

Operator new[]和delete[]分别用于动态分配和释放数组内存空间，例子：

```c++
MyClass * p1 = new MyClass[5]; // allocates and constructs five objects
MyClass * p2 = new (std::nothrow) MyClass[5]; 
...
delete[] p2;
delete[] p1;
```

### Overloading new

### 3 {}-lists

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



## 4 函数修饰符

- inline:indicating a desire to have function calls implemented by inlining the function body constexpr:indicating that it should be possible to evaluate the function at compile time if given constant expressions as arguments

- noexcept:indicating that the function may not throw an exception

- [[noreturn]]: indicating that the function will not return using the normal call/return mechanism(What happens if the function returns despite a [[noreturn]] attribute is undefined.)

- virtual: indicating that it can be overridden in a derived class

- override:indicating that it must be overriding a virtual function from a base class

- final: indicating that it cannot be overriden in a derived class 

- static: indicating that it is not associated with a particular object

- const: indicating that it may not modify its object 

### 4.1  [[...]] Attributes
A construct [[...]] is called an attribute and can be placed just about anywhere in the C++ syntax.
In general, an attribute specifies some implementation-dependent property about the syntactic
entity that precedes it. In addition, an attribute can be placed in front of a declaration. There are
only two standard attributes, and [[noreturn]] is one of them. The other is [[carries_dependency]]。

### 4.2 Member initializer list

例子：

```
Club::Club(const string& n, Date fd)
: name{n}, members{}, officers{}, founded{fd}
{
	// ...
}
```

### 4.3 deleted Functions

We can ‘‘delete’’ a function; that is, we can state that a function does not exist so that it is an error to try to use it (implicitly or explicitly). The most obvious use is to eliminate otherwise defaulted functions. 例子：

```c++
class Base {
	// ...
	Base& operator=(const Base&) = delete;// disallow copying
	Base(const Base&) = delete;
	Base& operator=(Base&&) = delete; // disallow moving
	Base(Base&&) = delete;
};
```





### 5 Exception Handling

C++ Exception Handling支持throw、try和catch，不支持finally，基本模式如下：

```c++
try {
   // protected code
} catch( ExceptionName e1 ) {
   // catch block
} catch( ExceptionName e2 ) {
   // catch block
} catch( ExceptionName eN ) {
   // catch block
}
```

try/catch的可以是一个任意类，实际例子如下：

```c++
#include <iostream>
using namespace std;

double division(int a, int b) {
   if( b == 0 ) {
      throw "Division by zero condition!";
   }
   return (a/b);
}

int main () {
   int x = 50;
   int y = 0;
   double z = 0;
 
   try {
      z = division(x, y);
      cout << z << endl;
   } catch (const char* msg) {
     cerr << msg << endl;
   }

   return 0;
}
```

C++定义了以std::exception为父类的一些列标准异常类，也可以继承这些类，实现自定义异常类。



## 6 RAII（Resource Acquisition Is Initialization）

RAII can be summarized as follows:

encapsulate each resource into a class, where

- the constructor acquires the resource and establishes all class invariants or throws an exception if that cannot be done,
- the destructor releases the resource and never throws exceptions;

always use the resource via an instance of a RAII-class that either

- has automatic storage duration or temporary lifetime itself, or
- has lifetime that is bounded by the lifetime of an automatic or temporary object

Move semantics make it possible to safely transfer resource ownership between objects, across scopes, and in and out of threads, while maintaining resource safety. Classes with open()/close(), lock()/unlock(), or init()/copyFrom()/destroy() member functions are typical examples of non-RAII classes。


## 7 Class Constructor和Deconstructor

```c++
class X {
	X(Sometype); // ‘‘ordinar y constructor’’: create an object
	X(); //default constructor
	X(const X&); // copy constr uctor
	X(X&&); //move constr uctor
	X& operator=(const X&); // copy assignment: clean up target and copy
	X& operator=(X&&); // move assignment: clean up target and move
	˜X(); //destructor: clean up
	// ...
};
```



### 7.1 Destructor

- The name of a destructor is ˜ followed by the class name
- A destructor does not take an argument, and a class can have only one destructor.
- Destructors are called implicitly when an automatic variable goes out of scope, an object
- on the free store is deleted, etc.
- Only in very rare circumstances does the user need to call a destructor explicitly
- A type that has no destructor declared, such as a built-in type, is considered to have a destructor that does nothing.(如果希望调用父类的Destructor(包括隐式或显式)能正确的调用到子类的Destructor，需要将父类的Destructor声明为virtual)

### 7.2 默认Constructor和Deconstructor

编译器会生成下列默认Constructor/Deconstructor：

- A default constructor: X()
- A copy constructor: X(const X&)
- A copy assignment: X& operator=(const X&)
- A move constructor: X(X&&)
- A move assignment: X& operator=(X&&)
- A destructor: ˜X()

如果用户声明了就不会自动生成。

### 7.3 Placement New

Placement new allows you to construct an object in memory that's already allocated。使用场景例如一次内存分配完成多个Object所需内存，然后在已分配的内存上完成Object初始化，而不是每次初始化一个Object都需要单独进行一次内存分配。

例子：

```C++
char *buf  = new char[sizeof(string)]; // pre-allocated buffer
string *p = new (buf) string("hi");    // placement new
string *q = new string("hi");          // ordinary heap allocation

void C::push_back(const X& a)
{
	// ...
	new(p) X{a}; // copy constr uct an X with the value a in address p
	// ...
}
```

### 7.4 Deallocation in placement new

You should not deallocate every object that is using the memory buffer. Instead you should delete[] only the original buffer. You would have to then call the destructors of your classes manually. 例子：

```C++
void C::pop_back()
{
	// ...
	p−>˜X(); // destroy the X in address p
}
```



































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











### In-Class Function Definitions
A member function defined within the class definition – rather than simply declared there – is taken to be an inline member function. That is, in-class definition of member functions is for small, rarely modified, frequently used functions.


### mutable member
We can define a member of a class to be mutable, meaning that it can be modified even in a const
object，例子：

```c++
class Date {
	public:
		// ...
		string string_rep() const; // string representation
	private:
		mutable bool cache_valid;
		mutable string cache;
		void compute_cache_value() const; // fill (mutable) cache
		// ...
};
```



### Type Aliases
下面的例子中value_type就是一个Type Aliases：

```C++
template<typename T>
	class Tree {
		using value_type = T; // member alias
		enum Policy { rb, splay, treeps }; // member enum
			class Node { // member class
				Node∗ right;
				Node∗ left;
				value_type value;
				public:
					void f(Tree∗);
			};
		Node∗ top;
		public:
			void g(const T&);
			// ...
	};
```

### Overloaded Operators
例子：

```c++
inline bool operator==(Date a, Date b) // equality
{
	return a.day()==b.day() && a.month()==b.month() && a.year()==b.year();
}
```





### friend
Friend functions: A non-member function can access the private and protected members of a class if it is declared a friend of that class. 例子：

```
class Rectangle {
    int width, height;
  public:
    Rectangle() {}
    Rectangle (int x, int y) : width(x), height(y) {}
    int area() {return width * height;}
    friend Rectangle duplicate (const Rectangle&);
};
```

Similar to friend functions, a friend class is a class whose members have access to the private or protected members of another class.



### 重载类型转换
例子：

```c++
template <typename T, typename D = default_delete<T>>
class unique_ptr {
	public:
	// ...
	// does *this hold a pointer (that is not nullptr)?
	explicit operator bool() const noexcept; 
	// ...
};
```



### typeid
运行时获知变量类型名称，可以使用 typeid(变量).name()。需要注意不是所有编译器都输出”int”、”float”等之类的名称。


http://www.cplusplus.com/doc/tutorial/


### template
#### Function templates
The format for declaring function templates with type parameters is:

```c++
template <class identifier> function_declaration;
template <typename identifier> function_declaration;
```



To use this function template we use the following format for the function call:

```c++
function_name <type> (parameters);
```

例子：

```c++
template <class myType>
myType GetMax (myType a, myType b) {
   return (a>b?a:b);
}

GetMax <int> (x,y);
```

#### Class templates
例子：
```c++
template <class T>
class mypair {
	T values [2];
    public:
		mypair (T first, T second) {
			values[0]=first; values[1]=second;
		}
};

mypair<int> myobject (115, 36); 
```

#### Template specialization
If we want to define a different implementation for a template when a specific type is passed as template parameter, we can declare a specialization of that template.  例子：

```c++
// class template:
template <class T>
class mycontainer {
    T element;
  public:
    mycontainer (T arg) {element=arg;}
    T increase () {return ++element;}
};

// class template specialization:
template <>
class mycontainer <char> {
    char element;
  public:
    mycontainer (char arg) {element=arg;}
    char uppercase ()
    {
      if ((element>='a')&&(element<='z'))
      element+='A'-'a';
      return element;
    }
};
```



所用语法提要（precede the class template name with an empty `template<>` parameter list. This is to explicitly declare it as a template specialization. But more important than this prefix, is the `<char>` specialization parameter after the class template name. ）：

```C++
template <> class mycontainer <char> { ... };
```

#### 非类型template
除了类型可以作为template参数之外，还可以和普通函数一样，有普通类型参数，例子：

```c++
// sequence template
#include <iostream>
using namespace std;

template <class T, int N>
class mysequence {
	T memblock [N];
	public:
		void setmember (int x, T value);
		T getmember (int x);
};

template <class T, int N>
void mysequence<T,N>::setmember (int x, T value) {
	memblock[x]=value;
}

template <class T, int N>
T mysequence<T,N>::getmember (int x) {
	return memblock[x];
}

int main () {
  mysequence <int,5> myints;
  mysequence <double,5> myfloats;
  myints.setmember (0,100);
  myfloats.setmember (3,3.1416);
  cout << myints.getmember(0) << '\n';
  cout << myfloats.getmember(3) << '\n';
  return 0;
}
```



#### template参数默认值
It is also possible to set default values or types for class template parameters. For example, if the previous class template definition had been:

```c++
template <class T=char, int N=10> class mysequence {..};

//using the default template parameters by declaring:
mysequence<> myseq;

//equivalent to:
mysequence<char,10> myseq;
```


#### 编译器的处理与限制
From the point of view of the compiler, templates are not normal functions or classes. They are compiled on demand, meaning that the code of a template function is not compiled until an instantiation with specific template arguments is required. At that moment, when an instantiation is required, the compiler generates a function specifically for those arguments from the template.

#####限制
Because templates are compiled when required, this forces a restriction for multi-file projects: the implementation (definition) of a template class or function must be in the same file as its declaration. That means that we cannot separate the interface in a separate header file, and that we must include both interface and implementation in any file that uses the templates.

Since no code is generated until a template is instantiated when required, compilers are prepared to allow the inclusion more than once of the same template file with both declarations and definitions in a project without generating linkage errors.





### Smart Pointers

- unique_ptr:  represent exclusive ownership, When a unique_ptr  is destroyed, its deleter  is called to destroy the owned object.

- shared_ptr:  represents shared ownership,  is a kind of counted pointer where the object pointed to is deleted when the use count goes to zero.

- weak_ptr: is a smart pointer that holds a non-owning ("weak") reference to an object that is managed by std::shared_ptr. It must be converted to std::shared_ptr in order to access the referenced object. std::weak_ptr models temporary ownership: when an object needs to be accessed only if it exists, and it may be deleted at any time by someone else, std::weak_ptr is used to track the object, and it is converted to std::shared_ptr to assume temporary ownership. If the original std::shared_ptr is destroyed at this time, the object's lifetime is extended until the temporary std::shared_ptr is destroyed as well.







## Advices

1. Don’t try to catch every exception in every function; Don’t try to catch every exception in every function;
2. Let a constructor establish an invariant, and throw if it cannot; Be sure that every resource acquired in a constructor is released when throwing an exception in that constructor;
3. Minimize the use of try-blocks; 
4. Prefer proper resource handles to the less structured finally;
5. Design your error-handling strategy to allow for different levels of checking/enforcement;
6. If your function may not throw, declare it noexcept;
7. Have main() catch and report all exceptions;
8. Never let an exception escape from a destructor;
9. Place ev ery nonlocal name, except main(), in some namespace;
10. Don’t put a using-directive in a header file;
11. Avoid non-inline function definitions in headers;
12. Distinguish between average users’ interfaces and expert users’ interfaces
13. By default declare single-argument constructors explicit;
14. Declare a member function that does not modify the state of its object const ;
15. Make a function a member only if it needs direct access to the representation of a class;
16. Make a member function that doesn’t modify the value of its object a const  member function;
17. If a class has a virtual function, it needs a virtual destructor;
18. Initialize members and bases in their order of declaration;
19. If a class has a reference member, it probably needs copy operations (copy constructor and copy assignment);
20. A copy operations should provide equivalence and independence;
21. Use static_assert() and assert() extensively;
22. Do not assume that assert() is always evaluated;
23. Insertion operators, such as insert() and push_back() are often more efficient on a vector than
    on a list;
24. Use forward_list for sequences that are usually empty;
25. To be an element type for a STL container, a type must provide copy or move operations;
26. Pass a container by reference and return a container by value;
27. For a container, use the ()-initializer syntax for sizes and the {}-initializer syntax for lists of
    elements;
28. Use const iterators where you don’t need to modify the elements of a container;
29. Don’t use iterators into a resized vector or deque;
30. 0.7 is often a reasonable load factor of containers; 
31.  Use unique_ptr  to represent exclusive ownership;
32.  Use shared_ptr  to represent shared ownership;
33. Minimize the use of weak_ptrs;



~

