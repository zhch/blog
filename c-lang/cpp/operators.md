### Overloaded Operators
例子：

```c++
inline bool operator==(Date a, Date b) // equality
{
	return a.day()==b.day() && a.month()==b.month() && a.year()==b.year();
}
```



### Assignment

```C++
X& operator=(const X&); // copy assignment: clean up target and copy
X& operator=(X&&); // move assignment: clean up target and move
```

Copy assignment从src中拷贝数据到destination，Move assignment从src中“偷取”资源到destination。Copy assignment保证完成后两个Object都是Complelte，Move assignment只保证destination complete，src可以是complete，可以是empty或者可以是invalid状态。

#### 为什么copy assignment和move assignment返回reference而不是value？

If you return by value for operator=, you will call a constructor AND destructor EACH time that the assignment operator is called!! 例子：

```C++
//Given: 
A& operator=(const A& rhs) { /* ... */ };

//Then calls assignment operator above twice. Nice and simple.
a = b = c;
```

```c++
//But, 
A operator=(const A& rhs) { /* ... */ };

// calls assignment operator twice, calls copy constructor twice, calls destructor type to delete the temporary values!
a = b = c; 
```

