---
title: "类型转换和类型安全 （现代 C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: article
dev_langs: C++
ms.assetid: 629b361a-2ce1-4700-8b5d-ab4f57b245d5
caps.latest.revision: "23"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 2c77b7269ae70d24878ff02c0661b60365c76d1b
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="type-conversions-and-type-safety-modern-c"></a>类型转换和类型安全（现代 C++）
本文档确定了一些常见的类型转换问题，并介绍了如何在 C++ 代码中避免它们。  
  
 在编写 C++ 程序时，请务必确保它是类型安全的。 这意味着每个变量、函数参数和函数返回值将存储一个可接受的类型的数据，并意味着涉及不同类型的值的操作“有意义”且不会导致数据丢失、位模式解释不正确或内存损坏。 从不显式或隐式地将值从一种类型转换为另一种类型的程序在定义上是类型安全的。 但是，有时需要进行类型转换，甚至需要进行不安全的类型转换。 例如，您可能必须在 `int` 类型的变量中存储浮点运算的结果，或者您可能必须将无符号 `int` 中的值传递到采用有符号 `int` 的函数。 这两个示例列举了不安全的转换，因为它们可能会导致数据丢失或值的重新解释。  
  
 当编译器检测到不安全的转换时，它会发出错误或警告。 如果发出错误，则编译会停止；如果发出警告，则编译可以继续，但会指示代码中可能存在错误。 但是，即使你编译程序后没有收到警告，它仍然可能包含导致生成错误结果的隐式类型转换。 类型错误也可能由代码中的显式转换或强制转换引入。  
  
## <a name="implicit-type-conversions"></a>隐式类型转换  
 当表达式包含不同内置类型的操作数且存在没有显式强制转换时，编译器将使用内置*标准转换*来转换其中一个操作数的类型匹配。 编译器将尝试按一个明确定义的顺序进行转换，直到有一个转换成功。 如果所选转换是提升转换，则编译器不会发出警告。 如果转换是收缩转换，则编译器会发出有关数据可能丢失的警告。 尽管是否真的发生数据丢失取决于涉及的实际值，但我们建议您将此警告视为错误。 如果涉及到用户定义的类型，则编译器将尝试使用您在类定义中指定的转换。 如果编译器找不到可接受的转换，则会发出错误且不会编译程序。 有关用于管理的标准转换的规则的详细信息，请参阅[标准转换](../cpp/standard-conversions.md)。 有关用户定义的转换的详细信息，请参阅[用户定义的转换 (C++ /cli CLI)](../dotnet/user-defined-conversions-cpp-cli.md)。  
  
### <a name="widening-conversions-promotion"></a>扩大转换（提升）  
 在扩大转换中，较小的变量中的值将赋给较大的变量，同时不会丢失数据。 由于扩大转换始终是安全的，编译器将在不提示的情况下执行它们且不会发出警告。 以下转换是扩大转换。  
  
|From|到|  
|----------|--------|  
|任何有符号或无符号的整型，`long long` 和 `__int64` 除外|`double`|  
|`bool` 或 `char`|任何其他内置类型|  
|`short` 或 `wchar_t`|`int`, `long`, `long long`|  
|`int`, `long`|`long long`|  
|`float`|`double`|  
  
### <a name="narrowing-conversions-coercion"></a>收缩转换（强制）  
 编译器隐式执行收缩转换，但会发出有关数据丢失可能的警告。 请重视这些警告。 如果您确定数据丢失不会发生（因为较大的变量中的值始终适合较小的变量），则添加显式强制转换，使编译器不再发出警告。 如果你不确定转换是否安全，请为代码添加某种运行时检查以处理可能出现的数据丢失，从而确保转换不会导致程序生成错误的结果。 
  
 任何从浮点类型到整型的转换都是收缩转换，因为浮点值的小数部分将会丢弃和丢失。  
  
 以下代码示例演示了一些隐式收缩转换以及编译器为其发出的警告。  
  
```cpp  
int i = INT_MAX + 1; //warning C4307:'+':integral constant overflow  
wchar_t wch = 'A'; //OK  
char c = wch; // warning C4244:'initializing':conversion from 'wchar_t'  
              // to 'char', possible loss of data  
unsigned char c2 = 0xfffe; //warning C4305:'initializing':truncation from  
                           // 'int' to 'unsigned char'  
int j = 1.9f; // warning C4244:'initializing':conversion from 'float' to  
              // 'int', possible loss of data  
int k = 7.7; // warning C4244:'initializing':conversion from 'double' to  
             // 'int', possible loss of data  
```  
  
### <a name="signed---unsigned-conversions"></a>有符号到无符号的转换  
 有符号整型及其对应的无符号整型的大小总是相同，但它们的区别在于为值转换解释位模式的方式。 以下代码示例演示将相同的位模式解释为有符号值和无符号值时发生的情况。 存储在 `num` 和 `num2` 中的位模式将与前面的演示中所示的位模式保持相同，绝不会发生更改。  
  
```cpp  
using namespace std;  
unsigned short num = numeric_limits<unsigned short>::max(); // #include <limits>  
short num2 = num;  
cout << "unsigned val = " << num << " signed val = " << num2 << endl;  
// Prints: unsigned val = 65535 signed val = -1  
  
// Go the other way.  
num2 = -1;  
num = num2;  
cout << "unsigned val = " << num << " signed val = " << num2 << endl;  
// Prints: unsigned val = 65535 signed val = -1  
  
```  
  
 请注意，值将在两个方向重新解释。 如果程序生成了奇怪的结果，其中的值的符号似乎与您预期的相反，请查找有符号和无符号整型之间的隐式转换。 在下面的示例中，该表达式的结果 (0-1) 将隐式转换从`int`到`unsigned int`时存储在`num`。 这将导致位模式被重新解释。  
  
```cpp  
unsigned int u3 = 0 - 1;  
cout << u3 << endl; // prints 4294967295  
  
```  
  
 编译器不会发出有关有符号和无符号整型之间的隐式转换的警告。 因此，建议您完全避免有从符号到无符号的转换。 如果您无法避免它们，则向代码添加运行时检查，以检测所转换的值是大于或等于零还是小于或等于有符号类型的最大值。 此范围内的值将从有符号转换为无符号或从无符号转换为有符号而不用重新解释。  
  
### <a name="pointer-conversions"></a>指针转换  
 在很多表达式中，C 样式数组将隐式转换为指向该数组中的第一个元素的指针，并且可能在没有提示的情况下进行常量转换。 尽管此方法很方便，但它也可能容易出错。 例如，以下设计得不合理的代码示例似乎没有意义，但它会在 Visual C++ 中编译并生成结果“p”。 首先，“Help”字符串常量将转换为指向数组的第一个元素的 `char*`；该指针随后递增 3 个元素，以便现在能够指向最后一个元素“p”。  
  
```cpp  
char* s = "Help" + 3;  
  
```  
  
## <a name="explicit-conversions-casts"></a>显式转换（强制转换）  
 利用强制转换运算，您可以指示编译器将一种类型的值转换为另一种类型。 在某些情况下，如果两个类型完全无关，编译器将引发错误，但在其他情况下，即使运算不是类型安全的，编译器也不会引发错误。 应谨慎使用强制转换，因为从一种类型到另一个类型的任何转换都可能导致程序错误。 但是，强制转换有时是必需的，并且不是所有强制转换都同样危险。 强制转换的一个有效的使用情况是：当您的代码执行收缩转换并且您知道该转换不会导致程序生成错误的结果时。 实际上，这是在告诉编译器您知道自己在做什么，不要发出相关警告来打扰您。 另一种使用情况是从指向派生类的指针到指向基类的指针的强制转换。 还有一种使用情况是转换掉变量的 `const` 特性以将其传递给需要非 `const` 参数的函数。 大多数强制转换运算都存在一定的风险。  
  
 在 C 样式程序中，同一 C 样式强制转换运算符可用于所有类型的强制转换。  
  
```cpp  
(int) x; // old-style cast, old-style syntax  
int(x); // old-style cast, functional syntax  
  
```  
  
 C 样式强制转换运算符与调用运算符 () 相同，因此在代码中不显眼，容易被忽略。 这两个运算符都不好，因为它们难以让人一眼认出，并且它们差异很大，无法调用 `static`、`const` 和 `reinterpret_cast` 的任意组合。 确定旧式强制转换的实际作用可能很困难并且容易出错。 基于所有这些原因，当需要进行强制转换时，我们建议使用以下 C++ 强制转换运算符之一。在某些情况下，它们更加类型安全，并且可以更加明确地表达编程目的：  
  
-   `static_cast`，用于仅在编译时检查的强制转换。 如果编译器检测到您尝试在完全不兼容的类型之间强制转换，`static_cast` 将返回错误。 您还可以使用它在指向基对象的指针和指向派生对象的指针之间强制转换，但编译器无法总是判断出此类转换在运行时是否安全。  
  
    ```cpp  
    double d = 1.58947;  
    int i = d;  // warning C4244 possible loss of data  
    int j = static_cast<int>(d);       // No warning.  
    string s = static_cast<string>(d); // Error C2440:cannot convert from  
                                       // double to std:string  
  
    // No error but not necessarily safe.  
    Base* b = new Base();  
    Derived* d2 = static_cast<Derived*>(b);  
  
    ```  
  
     有关详细信息，请参阅[static_cast](../cpp/static-cast-operator.md)。  
  
-   `dynamic_cast`，用于从指向基对象的指针到指向派生对象的指针的、安全且经过运行时检查的强制转换。 `dynamic_cast` 在向下转换方面比 `static_cast` 更安全，但运行时检查会产生一些开销。  
  
    ```cpp  
    Base* b = new Base();  
  
    // Run-time check to determine whether b is actually a Derived*  
    Derived* d3 = dynamic_cast<Derived*>(b);  
  
    // If b was originally a Derived*, then d3 is a valid pointer.  
    if(d3)  
    {  
       // Safe to call Derived method.  
       cout << d3->DoSomethingMore() << endl;  
    }  
    else  
    {  
       // Run-time check failed.  
       cout << "d3 is null" << endl;  
    }  
  
    //Output: d3 is null;  
  
    ```  
  
     有关详细信息，请参阅[dynamic_cast](../cpp/dynamic-cast-operator.md)。  
  
-   `const_cast`，用于转换掉变量的 `const` 性，或者将非 `const` 变量转换为 `const`。 使用此运算符转换掉 `const` 性与使用 C 样式强制转换一样容易出错，只不过使用 `const-cast` 时不太可能意外地执行强制转换。 有时候，您必须转换掉变量的 `const` 特性。例如，将 `const` 变量传递给采用非 `const` 参数的函数。 下面的示例演示如何执行此操作。  
  
    ```cpp  
    void Func(double& d) { ... }  
    void ConstCast()  
    {  
       const double pi = 3.14;  
       Func(const_cast<double&>(pi)); //No error.  
    }  
  
    ```  
  
     有关详细信息，请参阅[const_cast](../cpp/const-cast-operator.md)。  
  
-   `reinterpret_cast`，用于无关类型（如 `pointer` 和 `int`）之间的强制转换。  
  
    > [!NOTE]
    >  此强制转换运算符不像其他运算符一样常用，并且不能保证可将其移植到其它编译器。  
  
     以下示例演示 `reinterpret_cast` 与 `static_cast` 的差异。  
  
    ```cpp  
    const char* str = "hello";  
    int i = static_cast<int>(str);//error C2440: 'static_cast' : cannot  
                                  // convert from 'const char *' to 'int'  
    int j = (int)str; // C-style cast. Did the programmer really intend  
                      // to do this?  
    int k = reinterpret_cast<int>(str);// Programming intent is clear.  
                                       // However, it is not 64-bit safe.  
  
    ```  
  
     有关详细信息，请参阅[reinterpret_cast 运算符](../cpp/reinterpret-cast-operator.md)。  
  
## <a name="see-also"></a>请参阅  
 [C++ 类型系统](../cpp/cpp-type-system-modern-cpp.md)   
 [欢迎回到 C++](../cpp/welcome-back-to-cpp-modern-cpp.md)   
 [C++ 语言参考](../cpp/cpp-language-reference.md)   
 [C++ 标准库](../standard-library/cpp-standard-library-reference.md)