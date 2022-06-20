---
title: 8.1 - 隐式类型转换
alias: 8.1 - 隐式类型转换
origin: /implicit-type-conversion-coercion/
origin_title: "8.1 -- Implicit type conversion (coercion)"
time: 2022-2-18
type: translation
tags:
- type conversion
---
??? note "关键点速记"
    - 类型转换可以通过两种方式触发：隐式地（编译器需要这么做）或是显式地（由程序员发起）
    - C++ 中的大部分类型转换都是隐式类型转换。它会发生在如下时机：
        - 当使用不同类型的变量对另外一种类型的变量进行初始化或赋值的时候
        - 当函数的实际返回值类型不同于函数定义的返回值类型时
        - 当对不同类型的操作数使用某些二元操作符时
        - 当在 if 语句中使用非布尔值时
        - 当传递给函数的实参与函数形参类型不同时
	  - C++ 标准定义了不同的基本类型的转换方式，包括：
		- 数值提升(参考：[[8-2-Floating-point-and-integral-promotion|8.2 - 浮点数和整型提升]])；
		- 数值转换(参考：[[8-3-Numeric-conversions|8.3 - 数值转换]])；
		- 算数转换(参考：[[8-4-Arithmetic-conversions|8.4 - 算数转换]])；
		- 其他转换 (包括多种指针和引用的转换)


## 类型转换简介

对象的值是以一系列的[[bit|比特]]存放的，而数据类型则告诉编译器应该如何将这一系列比特解析成有意义的值。不同的数据类型可以以不同的方式表示*相同*的值。例如，整型值 3 可能被存放为二进制  `0000 0000 0000 0000 0000 0000 0000 0011`，而浮点数 3.0 则可能被存放为二进制 `0100 0000 0100 0000 0000 0000 0000 0000`。

那么当我们这样做的时候会发生什么呢?

```cpp
float f{ 3 }; // initialize floating point variable with int 3
```

这种情况下，编译器不能仅仅把表示 `int` 类型值 3 的比特拷贝到内存中表示 `float` 变量 `f`。相反，它必须将整型值 3 转换为等价的浮点数，然后才能存放到内存中表示变量 `f`。

将一种数据类型转换为另外一种数据类型的过程，称为类型转换。

类型转换可以通过两种方式触发：隐式地（编译器需要这么做）或是显式地（由程序员发起）。我们会在本节课介绍[[implicit-type-conversion|隐式类型转换(implicit type conversion)]]，然后在下节课 [[8-5-Explicit-type-conversion-casting-and-static-cast|8.5 - 显式类型转换]] 中介绍[[explicit-type-conversion|显式类型转换(Explicit type conversion)]]。

## 隐式类型转换

隐式类型转换（也叫做自动类型转换）由编译器在需要时（需要某个变量类型但却给定了另外的类型）自动完成。C++ 中的大部分类型转换都是隐式类型转换。它会发生在如下时机：

当使用不同类型的变量对另外一种类型的变量进行初始化或赋值的时候：

```cpp
double d{ 3 }; // int 3 隐式转换为 double 类型
d = 6; // int 6 隐式转换为 double 类型
```

当函数的实际返回值类型不同于函数定义的返回值类型时：

```cpp
float doSomething()
{
    return 3.0; // double 3.0 隐式转换为 float 类型
}
```

当对不同类型的操作数使用某些二元操作符时：

```cpp
double division{ 4.0 / 3 }; // int 3 隐式转换为 double 类型
```

当在 if 语句中使用非布尔值时：

```cpp
if (5) // int 5 隐式转换为 bool 类型
{
}
```

当传递给函数的实参与函数形参类型不同时：

```cpp
void doSomething(long l)
{
}

doSomething(3); // int 3 隐式转换为 long 类型
```

## 当类型转换触发时会发生什么

当类型转换触发时(无论是隐式还是显式)，编译器需要确定是否可以将值从当前类型转换为所需的类型。如果可以找到有效的转换，那么编译器将生成所需类型的新值。注意，类型转换不会改变被转换的值或对象的值或类型。

如果编译器无法找到可用的转换，则会产生编译错误。类型转换失败的原因有很多。例如，编译器可能不知道如何在原始类型和所需类型之间转换值。或者，语句可能不允许某些类型的转换。例如:

```cpp
int x { 3.5 }; // 括号类型转换不允许类型转换以避免数据丢失
```

即使编译器知道如何将 `double` 类型转换为 `int` 类型，但在使用括号初始化时，这样的转换是不允许的。

还有一些情况下，编译器可能无法确定几种可能的类型转换中哪一种是最适合使用的。我们会在 [[8-11-Function-overload-resolution-and-ambiguous-matches|8.11 - 函数重载解析和匹配歧义]] 中进行介绍。

那么编译器实际上是如何确定它是否可以将一个值从一种类型转换为另一种类型的呢?

## 转换标准

C++语言标准定义了不同的基本类型(也包括一些复合类型)应当如何转换为其他类型。这些转换规则称为转换标准。

转换标准大致可分为四类，每一类涵盖不同的转换方式:

- 数值提升(参考：[[8-2-Floating-point-and-integral-promotion|8.2 - 浮点数和整型提升]])；
- 数值转换(参考：[[8-3-Numeric-conversions|8.3 - 数值转换]])；
- 算数转换(参考：[[8-4-Arithmetic-conversions|8.4 - 算数转换]])；
- 其他转换 (包括多种指针和引用的转换)

当需要类型转换时，编译器将查看是否有标准转换，可以用来将值转换为所需的类型。编译器可以在转换过程中应用 0 个、1 个或多个标准转换。

!!! cite "题外话"

    如何在不进行实际类型转换的同时进行类型转换? 例如，在 `int` 和 `long` 都具有相同大小和范围的架构中，使用相同的位序列来表示这两种类型的值。 因此，在这些类型之间转换值不需要实际的转换——值可以简单地复制。

描述类型转换如何工作的一整套规则既冗长又复杂，而且在大多数情况下，类型转换”就是能够工作“。我们会在后面的课程中介绍你需要了解的关于类型转换的最重要的事情。如果你需要更详细的资料，可以参考 [隐式类型转换技术参考文档](https://en.cppreference.com/w/cpp/language/implicit_conversion) 。