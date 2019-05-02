### contexpr

用于优化性能，被contexpr修饰的value of an object或者是函数可以被编译器在编译时求值。当然被声明为contexpr的value of an object或者是函数有一些列限制：https://en.cppreference.com/w/cpp/language/constexpr

constexpr函数例子：

```C++
// constexpr function for product of two numbers. 
// By specifying constexpr, we suggest compiler to 
// to evaluate value at compiler time 
constexpr int product(int x, int y) 
{ 
	return (x * y); 
} 

int main() 
{ 
	const int x = product(10, 20); 
	cout << x; 
	return 0; 
} 
```



contexpr构造器例子：

```C++
//  C++ program to demonstrate uses of constexpr in constructor 
#include <bits/stdc++.h> 
using namespace std; 
  
//  A class with constexpr constructor and function 
class Rectangle 
{ 
    int _h, _w; 
public: 
    // A constexpr constructor 
    constexpr Rectangle (int h, int w) : _h(h), _w(w) {} 
      
    constexpr int getArea ()  {   return _h * _w; } 
}; 
  
//  driver program to test function 
int main() 
{ 
    // Below object is initialized at compile time 
    constexpr Rectangle obj(10, 20); 
    cout << obj.getArea(); 
    return 0; 
} 
```



### explicit

只能出现在 class definition中的constructor或者是conversion function声明：Specifies that a constructor or conversion function  is explicit, that is, it cannot be used for [implicit conversions](https://en.cppreference.com/w/cpp/language/implicit_cast) and [copy-initialization](https://en.cppreference.com/w/cpp/language/copy_initialization).

conversion function也称重载类型转换，例子：

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