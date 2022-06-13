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
