---
title: "内联函数 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- __forceinline_cpp
- __inline_cpp
- inline_cpp
dev_langs: C++
helpviewer_keywords: inline functions [C++], class members
ms.assetid: 355f120c-2847-4608-ac04-8dda18ffe10c
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: de370d8dbff1f1340539adc825f7f5316c59a468
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="inline-functions-c"></a>内联函数 (C++)
类声明的主体中定义的函数是内联函数。  
  
## <a name="example"></a>示例  
 在下面的类声明中，`Account` 构造函数是内联函数。 成员函数`GetBalance`， `Deposit`，和`Withdraw`未指定为**内联**但可以作为内联函数实现。  
  
```  
// Inline_Member_Functions.cpp  
class Account  
{  
public:  
    Account(double initial_balance) { balance = initial_balance; }  
    double GetBalance();  
    double Deposit( double Amount );  
    double Withdraw( double Amount );  
private:  
    double balance;  
};  
  
inline double Account::GetBalance()  
{  
    return balance;  
}  
  
inline double Account::Deposit( double Amount )  
{  
    return ( balance += Amount );  
}  
  
inline double Account::Withdraw( double Amount )  
{  
    return ( balance -= Amount );  
}  
int main()  
{  
}  
```  
  
> [!NOTE]
>  在类声明中，声明函数而无需**内联**关键字。 **内联**关键字可以在类声明中指定; 结果也相同。  
  
 给定的内联成员函数在每个编译单元中必须以相同的方式进行声明。 此约束会导致内联函数像实例化函数一样运行。 此外，必须有精确的内联函数的定义。  
  
 类成员函数默认为外部链接，除非该函数的定义包含**内联**说明符。 前面的示例说明，这些函数需要不显式声明与**内联**说明符; 使用**内联**函数中定义会使它成为一个内联函数。 但是，它是非法重新声明一个函数作为**内联**之后对该函数的调用。  
  
## <a name="inline-inline-and-forceinline"></a>Inline、 __inline、 和\__forceinline  
 `inline` 和 `__inline` 说明符指示编译器将函数体的副本插入调用函数的每个位置处。  
  
 仅当编译器的成本/收益分析显示有好处时，才会进行插入（称为内联展开或内联）。 内联展开以代码大小较大的潜在成本减少函数调用的系统开销。  
  
 `__forceinline` 关键字会重写成本/收益分析，改为依赖于程序员的判断。 使用 `__forceinline` 时应谨慎。 不加选择地使用 `__forceinline` 可能会形成较大代码但是仅获得边际性能提升，或是在某些情况下，甚至会损失性能（例如，由于较大可执行文件的分页会增加）。  
  
 使用内联函数可以使程序更快，因为它们消除了与函数调用关联的开销。 内联展开的函数可以进行无法对普通函数使用的代码优化。  
  
 编译器将内联扩展选项和关键字视为建议。 不保证会对函数进行内联。 无法强制编译器对特定函数进行内联（即使使用 `__forceinline` 关键字）。 使用编译时**/clr**，编译器将不内联函数是否存在安全属性应用于该函数。  
  
 **内联**关键字是仅在 C++ 中可用。 `__inline` 和 `__forceinline` 关键字在 C 和 C++ 中都可用。 为了与早期版本中，兼容**_inline**是同义词`__inline`。  
  
 **内联**关键字告知编译器内联展开是首选。 但是，编译器可以创建函数的单独实例（实例化），并创建标准调用链接而不是内联插入代码。 可能会出现这种行为的两种情况是：  
  
-   递归函数。  
  
-   在翻译单元中的其他位置通过指针引用的函数。  
  
 这些原因可能会干扰内联，*如其他*，编译器; 自行你不应依赖于**内联**说明符使函数进行内联。  
  
 与普通函数一样，对内联函数计算参数没有明确顺序。 事实上，这可能与使用普通函数调用协议传递时计算参数的顺序不同。  
  
 [/Ob](../build/reference/ob-inline-function-expansion.md)编译器优化选项有助于确定是否实际进行内联函数扩展。  
  
 [/LTCG](../build/reference/ltcg-link-time-code-generation.md)执行跨模块内联无论是否已在源代码中进行了请求。  
  
### <a name="example-1"></a>示例 1  
  
```  
// inline_keyword1.cpp  
// compile with: /c  
inline int max( int a , int b ) {  
   if( a > b )   
      return a;  
   return b;  
}  
```  
  
 可以通过使用的内联声明类的成员函数**内联**关键字或通过将函数定义置于类定义。  
  
### <a name="example-2"></a>示例 2  
  
```  
// inline_keyword2.cpp  
// compile with: /EHsc /c  
#include <iostream>  
using namespace std;  
  
class MyClass {  
public:  
   void print() { cout << i << ' '; }   // Implicitly inline  
private:  
   int i;  
};  
```  
  
### <a name="microsoft-specific"></a>Microsoft 专用  
 `__inline`关键字等效于**内联**。  
  
 即使使用 `__forceinline`，编译器也无法在所有情况下都对代码进行内联。 如果出现以下情况，编译器无法对函数进行内联：  
  
-   使用 /Ob0（调试生成的默认选项）编译函数或其调用方。  
  
-   函数和调用方使用不同类型的异常处理（一个中使用 C++ 异常处理，另一个中使用结构化异常处理）。  
  
-   函数具有变量参数列表。  
  
-   除非使用 /Og、/Ox、/O1 或 /O2 进行编译，否则函数使用内联程序集。  
  
-   函数是递归函数，不附带**#pragma inline_recursion （on)**。 递归函数使用杂注内联为 16 个调用的默认深度。 若要减小内联深度，使用[inline_depth](../preprocessor/inline-depth.md)杂注。  
  
-   函数是虚函数，进行虚拟调用。 对虚函数的直接调用可以进行内联。  
  
-   程序采用函数地址，调用通过指向函数的指针进行。 对采用其地址的函数的直接调用可以进行内联。  
  
-   此函数也标记与[裸](../cpp/naked-cpp.md) [__declspec](../cpp/declspec.md)修饰符。  
  
 如果编译器无法内联的函数声明与`__forceinline`，它会生成级别 1 警告，除非：
  
-   该函数是通过 /Od 或 /Ob0 编译。 不内联预期在这些情况下。     
  
-   该函数在包括的库或另一个翻译单元中，定义外部使用，或者它是虚拟调用目标或间接调用目标。 编译器无法确定它找不到当前的翻译单元中的非内联代码。  
  
 递归函数可以内联替换为指定的深度[inline_depth](../preprocessor/inline-depth.md)杂注，最多 16 个调用。 该深度之后，递归函数调用被视为对函数实例的调用。  内联启发式对递归函数检查到的深度不能超过 16。 [Inline_recursion](../preprocessor/inline-recursion.md)杂注控制当前在扩展下的函数的内联扩展。 请参阅[内联函数展开](../build/reference/ob-inline-function-expansion.md)(/ Ob) 编译器选项设置为相关信息。  
  
**结束 Microsoft 专用**  
 有关详细信息使用**内联**说明符，请参阅：  
  
-   [内联类成员函数](../cpp/inline-functions-cpp.md)  
  
-   [使用 dllexport 和 dllimport 定义内联 (C++) 函数](../cpp/defining-inline-cpp-functions-with-dllexport-and-dllimport.md)  
  
## <a name="when-to-use-inline-functions"></a>何时使用内联函数  
 内联函数最适用于小函数使用，例如访问私有数据成员。 这些一行或两行代码的“访问器”函数的主要用途是返回有关对象的状态信息；短函数对函数调用的开销很敏感。 较长的函数在调用/返回序列方面花费的时间可成比例地减少，而从内联的获益也会减少。  
  
 A `Point` ，如下所示定义类：  
  
```  
// when_to_use_inline_functions.cpp  
class Point  
{  
public:  
    // Define "accessor" functions as  
    //  reference types.  
    unsigned& x();  
    unsigned& y();  
private:  
    unsigned _x;  
    unsigned _y;  
};  
  
inline unsigned& Point::x()  
{  
    return _x;  
}  
inline unsigned& Point::y()  
{  
    return _y;  
}  
int main()  
{  
}  
```  
  
 假设操作坐标是这样的类，指定两个访问器函数的客户端中相对常见操作 (`x`和`y`在前面的示例) 作为**内联**通常将节省上的开销：  
  
-   函数调用（包括参数传递和在堆栈上放置对象地址）  
  
-   保留调用者的堆栈帧  
  
-   设置新的堆栈帧  
  
-   返回值通信  
  
-   旧堆栈帧还原  
  
-   返回  
  
## <a name="inline-functions-vs-macros"></a>内联函数与宏  
 虽然内联函数类似于宏（因为在编译时进行调用会展开函数代码），但内联函数是通过编译器分析的，而宏是通过预处理器展开的。 因此，存在很多重大差异：  
  
-   内联函数遵循对正常函数强制执行的所有类型安全协议。  
  
-   与任何其他函数中使用相同的语法，只不过它们包括指定内联函数**内联**函数声明中的关键字。  
  
-   计算一次作为内联函数的参数传递的表达式。 在某些情况下，作为宏的参数传递的表达式可计算多次。  
  
 下面的示例演示了将小写字母转换为大写字母的宏：  
  
```  
// inline_functions_macro.c  
#include <stdio.h>  
#include <conio.h>  
  
#define toupper(a) ((a) >= 'a' && ((a) <= 'z') ? ((a)-('a'-'A')):(a))  
  
int main() {  
   char ch;  
   printf_s("Enter a character: ");  
   ch = toupper( getc(stdin) );  
   printf_s( "%c", ch );  
}  
//  Sample Input:  xyz  
// Sample Output:  Z  
  
```  
  
 表达式的意图`toupper(getc(stdin))`是，应从控制台设备中读取一个字符 (`stdin`) 并且，如果有必要，请转换为大写形式。  
  
 由于宏的实现，执行一次 `getc` 以确定字符是否大于或等于“a”，再将其执行一次以确定字符是否小于或等于“z”。 如果它在该范围内，则再次执行 `getc` 以将其转换为大写形式。 这意味着，程序将等待两个或三个字符，而在理想情况下，它只应等待一个字符。  
  
 内联函数纠正了前面所述的问题：  
  
```  
// inline_functions_inline.cpp  
#include <stdio.h>  
#include <conio.h>  
  
inline char toupper( char a ) {  
   return ((a >= 'a' && a <= 'z') ? a-('a'-'A') : a );  
}  
  
int main() {  
   printf_s("Enter a character: ");  
   char ch = toupper( getc(stdin) );  
   printf_s( "%c", ch );  
}  
```  
  
```Output  
Sample Input: a  
Sample Output: A  
```  
  
## <a name="see-also"></a>请参阅  
 [noinline](../cpp/noinline.md)   
 [auto_inline](../preprocessor/auto-inline.md)