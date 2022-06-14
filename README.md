# EM-CPP-
Effective Modern C++ Summary

## 术语和惯例
术语　　　　　　意指语言版本  
C++ 　　　　　　所有版本  
C++98　　　　　C++98 & C++03  
C++11　　　　　C++11 & C++14  
C++14　　　　　C++14　　　　　　　　　　　　　　　　　　　　　　　　　　　
                              

任意用户自定义型别　Widget类
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　
                              

声明：引入名字和型别 不给出细节

```
extern int x;　　　　　　　　　　　//对象声明

class Widget;　　　　　　　　　　　//类声明

bool func(const Widget& w); 　　　//函数声明

enum class Color;　　　　　　　　　//限定作用域的枚举声明
```
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　    
定义：给出存储位置和具体实现
```
int x;　　　　　　　　　　     　　   //对象定义

class Widget{  
  ...  
}; 　　　　　　　　　　　　　　　　　  //类定义

bool func(const Widget& w)  
{ return w.size() < 10 };　　　　　　//函数定义

enum class Color  
{ Yellow, Red, Blue };　　　　 　　　//限定作用域的枚举定义
```

## 条款1：理解模板型别推导
函数模板：
```cpp
template<typename T>
void f(ParamType param);
```
调用：
```cpp
f(expr);  // 从expr推导T和ParamType的型别
```

### 情况1：ParamType是个指针或引用，但不是个万能引用
```cpp
template<typename T>
void f(T& param);   // param是个引用
```
声明变量：
```cpp
int x = 27;
const int cx = x;
const int& rx = x;
```
调用结果：
```cpp
f(x);    // T: int , param: int&
f(cx);   // T: const int , param: const int&
f(rx);   // T: const int , param: const int&
```
1、向持有T&型别的模板传入const对象是安全的：该对象的常量性会成为T的型别推导结果的组成部分  
2、rx具有引用型别，但T并未被推导为一个引用。因为rx的引用性在型别推导中会被忽略  
p.s. 传给右值引用形参的，只能是右值引用实参

### 情况2：ParamType是个万能引用
* 如果expr是个左值，T和ParamType会被推导为左值引用
  * 模板型别推导中，T被推导为引用型别的唯一情形
  * 声明时使用的是右值引用语法，结果却是左值引用
* 如果expr是个左值，应用情况1中规则
```cpp
template<typename T>
void f(T&& param);   //param是个万能引用 声明方式类似右值引用

f(x);    // x: 左值 , T: int& , param: int&
f(cx);   // cx: 左值 , T: const int& , param: const int&
f(rx);   // rx: 左值 , T: const int& , param: const int&
f(27);   // 27: 右值 , T: int , param: int&&
```
### 情况2：ParamType既非指针也非引用
```cpp
template<typename T>
void f(T param);   // param按值传递
```
* 忽略expr的引用性、const属性、volatile属性
```cpp
f(x);    // T和param都是int
f(cx);   // T和param都是int
f(rx);   // T和param都是int
```
param是个完全独立于cx和rx存在的对象——是cx和rx的一个副本  
```cpp
template<typename T>
void f(T param);

const char* const ptr =    // ptr是个指涉到const对象的const指针
"Fun with pointers";

f(ptr);                     // 传递型别为const char* const的实参
```
星号右侧const将ptr声明为const：ptr不可以指涉到其他内存位置  
星号左侧const将ptr指涉到的对象声明为const：该字符串不可修改  
此时，ptr的常量性被忽略，但指涉到的对象的常量性会被保留，param的型别会被推导为const char*, 即一个可修改的、指涉到一个const字符串的指针

### 数组实参
```cpp
const char name[] = "J. P. Briggs";   // name型别：const char[13]
const char* ptrToName = name;         // 数组退化成指针

void myFunc(int param[]); 声明等价于 void myFunc(int* param);

template<typename T>
void f(T param);
f(name);    // name是数组，但是T的型别推导成const char*

template<typename T>
void f(T& param);   // 按引用方式传递形参的模板
f(name);
此时 T的型别会被推导成实际的数组型别 T：const char [13] 
f的形参（该数组的一个引用）：const char (&)[13]
```

### 函数实参
```cpp
void someFunc(int, double);   // someFunc: void(int, double)

template<typename T>
void f1(T param);

template<typename T>
void f2(T& param);

f1(someFunc);    // param: 函数指针 void (*)(int, double)

f2(someFunc);    // param: 函数引用 void (&)(int, double)
```

## 条款2：理解auto型别推导
当某变量采用auto来声明时，auto就扮演了模板中的T这个角色，而变量的型别饰词则扮演的是ParamType的角色
```cpp
auto x = 27;          // 型别饰词：auto本身
const auto cx = x;    // 型别饰词：const auto
const auto& rx = x;   // 型别饰词：const auto&
```
