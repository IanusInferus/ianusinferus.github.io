---
layout: default
---

# 再议C风格变量声明

Ianus Inferus

2017-01-03

NeoRAGEx2002曾经有[一篇文章](http://www.cnblogs.com/neoragex2002/archive/2005/11/06/269974.html)提到这个问题，但是有很多内容并没有包括，例如const和__declspec。

最近我遇到一些这方面的问题，感觉有必要做一个系统性的总结。后来经过一些实验，得出了一些结论，在这里分享给大家。

## C风格变量声明

C风格的变量声明，如

```c
extern __declspec(dllexport) void(__stdcall * const p[10])(int a, int b);
```

和其他语言都不一样，规则很不直观，需要一些理解

要理解它为什么是现在这样，首先需要理解它的思路，最初C的类型的写法，模拟了变量的实际使用

例如

```c++
#include <functional>

int add(int a, int b)
{
    return a + b;
}

int main(int argc, char **argv)
{
    int a[10] = {};
    int a0 = a[0];

    int *p = &a0;
    int p0 = *p;

    int (*f)(int a, int b) = add;
    int result = (*f)(1, 2);
    int result2 = f(1, 2); //使用函数指针时可以将(*f)简记为f，但声明时不能，因为会和函数原型混淆

    int g(int a, int b); //函数原型

    auto h = static_cast<int (*)(int a, int b)>(f); //转换时，需要用到没有变量名的类型声明，此时只需要去掉变量名本身

    auto i = std::function<int(int a, int b)>(h); //std::function的参数是函数本身的类型
    typedef int FuncType(int a, int b);
    auto j = std::function<FuncType>(i); //可以用typedef来将函数本身的类型起一个名字

    return 0;
}
```

因此，要声明一个变量，可以先写出它的实际的用法，再转换成变量声明

比如说我想要写一个包含10个函数指针元素的数组，函数为`(int, int) -> int`，可以先写调用

```c
int b = (*a[0])(1, 2);
```

再从调用写声明

```c
int (*a[10])(int a, int b);
```

注意C中的运算符结合优先级，首先是按括号，然后是右边的索引[]、函数()、成员.或者->，然后是左边的*
C风格变量声明 - const, method const

```c++
int add(int a, int b)
{
    return a + b;
}

class A
{
private:
    int a{};
    int b{};
public:
    void foo() const //函数后方的const表示函数调用时this为const的
    {
        this->a = 0; //编译错误，this为A * const;
        b = 0; //编译错误
        const_cast<A *>(this)->a = 0; //使用const_cast将this转换为A *则可以编译成功
    }
};

int main(int argc, char **argv)
{
    //const加一个类型名和类型名加const效果相同
    const int a1 = 0;
    int const a2 = 0;

    //const不与后方的*结合
    const int *a3 = 0;
    int const *a4 = 0;
    a3 = a4; //a3、a4为到不可改变的int的指针，但指针本身可以改变

    //const与前方的*结合
    int (* const a5) = 0;
    int * const a6 = 0;
    a5 = a6; //编译错误，a5不可改变

    //函数指针加const的规则与正常指针相同
    int (* const f)(int a, int b) = add;

    //成员函数指针加const的规则与正常指针相同
    void (A::* const g)() const = &A::foo;

    return 0;
}
```

## C风格变量声明 - extern, __declspec, __stdcall等

extern、__declspec(Visual C++)以及一些其他链接标记，是变量的属性，直接加在最前面

__stdcall(Visual C++)等调用规范，是函数的属性，加在函数名称前面或者函数指针的*前面

这样我们就可以明白下面这个声明的含义

```c
extern __declspec(dllexport) void(__stdcall * const p[10])(int a, int b);
//一个extern __declspec(dllexport)的变量p
//p是一个长度为10的数组，数组的每个元素是void(__stdcall * const)(int a, int b)
//这些元素是一个const的函数指针，函数本身的调用规范是__stdcall
//函数的参数是(int a, int b)，返回值是void
```

[back](../../)
