---
title: "后缀表达式 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- operators [C++], postfix
- postfix expressions
- expressions [C++], postfix
ms.assetid: 7ac62a57-06df-422f-b012-a75b37d7cb9b
caps.latest.revision: "8"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 8b965027e67cc2b2581c2ab00e51d2be7a899302
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="postfix-expressions"></a>后缀表达式
后缀表达式包含主表达式或者其中的后缀运算符跟在主表达式之后的表达式。 下表列出了后缀运算符。  
  
### <a name="postfix-operators"></a>后缀运算符  
  
|运算符名称|运算符表示法|  
|-------------------|-----------------------|  
|[下标运算符](../cpp/subscript-operator.md)|**[ ]**|  
|[函数调用运算符](../cpp/function-call-operator-parens.md)|**( )**|  
|[显式类型转换运算符](../cpp/explicit-type-conversion-operator-parens.md)|*类型名称* **（)**|  
|[成员访问运算符](../cpp/member-access-operators-dot-and.md)|**.** 或**->**|  
|[后缀递增运算符](../cpp/postfix-increment-and-decrement-operators-increment-and-decrement.md)|`++`|  
|[后缀递减运算符](../cpp/postfix-increment-and-decrement-operators-increment-and-decrement.md)|**--**|  
  
 以下语法描述了可能的后缀表达式：  
  
```  
  
      primary-expression   
postfix-expression[expression]postfix-expression(expression-list)simple-type-name(expression-list)postfix-expression.namepostfix-expression->namepostfix-expression++postfix-expression--cast-keyword < typename > (expression )typeid ( typename )  
```  
  
 *后缀表达式*可能更高版本主表达式或另一个后缀表达式。  请参阅**主表达式**。  后缀表达式从左到右进行分组，这允许表达式按如下方式链接起来：  
  
```  
func(1)->GetValue()++  
```  
  
 在上面的表达式中，func 是主表达式，func(1) 是函数后缀表达式，func(1)->GetData 是指定类成员的后缀表达式，func(1)->GetData() 是另一个函数后缀表达式，整个表达式是增加 GetData 的返回值的后缀表达式。  该表达式的整体含义是作为参数传递 1 的 "call func，并作为返回值获取一个指向类的指针。  然后调用此类上的 GetValue()，接着递增返回的值。  
  
 上面列出的表达式是赋值表达式，这意味着这些表达式的结果必须为右值。  
  
 后缀表达式形式  
  
```  
simple-type-name ( expression-list )  
```  
  
 指示构造函数的调用。  如果 simple-type-name 是基本类型，则表达式列表必须是单个表达式，并且该表达式指示表达式的值将转换为基础类型。  此类强制转换表达式模仿构造函数。  由于此形式允许使用相同的语法来构造基本类型和类，因此它在定义模板类时特别有用。  
  
 *Cast 关键字*是之一`dynamic_cast`，`static_cast`或`reinterpret_cast`。  详细信息可能位于**dynamic_cast**， **static_cast**和**reinterpet_cast**。  
  
 `typeid` 运算符被视为后缀表达式。  请参阅**typeid 运算符**。  
  
## <a name="formal-and-actual-arguments"></a>形式和实际参数  
 调用程序会将信息传递到“实参”中的已调用函数。 已调用函数使用对应的“形参”访问信息。  
  
 当调用函数时，将执行以下任务：  
  
-   计算所有参数（调用方提供的参数）。 没有计算这些参数的隐含顺序，但所有参数都会计算，并且所有副作用都会在进入该函数前完成。  
  
-   使用每个形参在表达式列表中对应的实参来初始化该形参。 （形参是在函数头中声明并在函数体中使用的参数。）转换就像是通过初始化完成的一样 - 标准的和用户定义的转换在将参数转换为正确的类型时执行。 以下代码从概念上演示了所执行的初始化：  
  
    ```  
    void Func( int i ); // Function prototype  
    ...  
    Func( 7 );          // Execute function call  
    ```  
  
     调用前的概念性初始化为：  
  
    ```  
    int Temp_i = 7;  
    Func( Temp_i );  
    ```  
  
     请注意，初始化就像使用等号语法（而不是括号语法）一样执行。 在将值传递到函数之前制作了 `i` 的副本。 (有关详细信息，请参阅[初始值设定项](../cpp/initializers.md)和[转换](../cpp/user-defined-type-conversions-cpp.md))。  
  
     因此，如果函数原型 （声明） 的类型参数调用**长**，并且调用程序提供的类型参数`int`，使用标准类型转换提升该参数若要键入**长**(请参阅[标准转换](../cpp/standard-conversions.md))。  
  
     如果提供了一个实际参数，但它没有到形式参数类型的标准的或用户定义的转换，则是一个错误。  
  
     对于类类型的实际参数，将通过调用类的构造函数初始化形式参数。 (请参阅[构造函数](../cpp/constructors-cpp.md)有关这些特殊类成员函数的详细信息。)  
  
-   执行函数调用。  
  
 以下程序片段演示了函数调用：  
  
```  
// expre_Formal_and_Actual_Arguments.cpp  
void func( long param1, double param2 );  
  
int main()  
{  
    long i = 1;  
    double j = 2;  
  
    // Call func with actual arguments i and j.  
    func( i, j );  
}  
  
// Define func with formal parameters param1 and param2.  
void func( long param1, double param2 )  
{  
}  
```  
  
 当`func`从 main，形式参数调用`param1`用的值初始化`i`(`i`将转换为类型**长**以与使用一种标准的正确类型相对应转换） 和形参`param2`用的值初始化`j`(`j`将转换为类型**double**使用标准转换)。  
  
## <a name="treatment-of-argument-types"></a>参数类型的处理  
 不能在函数主题内更改声明为 const 类型的形式参数。 函数可以更改的类型不是任何参数**const**。 但是，更改是为函数，且不影响实际参数的值，除非实参是对不是类型的对象的引用**const**。  
  
 以下函数阐释了其中的一些概念：  
  
```  
// expre_Treatment_of_Argument_Types.cpp  
int func1( const int i, int j, char *c ) {  
   i = 7;   // C3892 i is const.  
   j = i;   // value of j is lost at return  
   *c = 'a' + j;   // changes value of c in calling function  
   return i;  
}  
  
double& func2( double& d, const char *c ) {  
   d = 14.387;   // changes value of d in calling function.  
   *c = 'a';   // C3892 c is a pointer to a const object.  
    return d;  
}  
```  
  
## <a name="ellipses-and-default-arguments"></a>省略号和默认参数  
 通过使用下列两种方法之一，可以声明函数以接受比函数定义中指定的参数更少的参数：省略号 (`...`) 或默认参数。  
  
 省略号表示可能需要参数，但声明中未指定数目和类型。 这通常是较差的 C++ 编程做法，因为它使您无法获得 C++ 的一个优点，即类型安全。 不同的转换将应用于使用省略号声明的函数，而不是应用于那些已知其形式和实际参数类型的函数：  
  
-   如果实际参数的类型是**float**，其提升为键入**double**函数调用前。  
  
-   所有有符号或无符号`char`，**短**、 枚举类型，或位域转换为有符号或无符号`int`使用整型提升。  
  
-   类类型的所有参数都作为数据结构通过值进行传递；副本是由二进制复制创建的，而不是通过调用类的复制构造函数（如果存在）创建的。  
  
 如果使用省略号，则必须在参数列表中最后声明它。 有关传递可变数量的参数的详细信息，请参阅的讨论[va_arg、 va_start 和 va_list](../c-runtime-library/reference/va-arg-va-copy-va-end-va-start.md)中*运行时库参考*。  
  
 有关 CLR 编程中默认参数的信息，请参阅[变量参数列表 （...）(C++ /CLI CLI)](../windows/variable-argument-lists-dot-dot-dot-cpp-cli.md).  
  
 如果函数调用中没有提供值，则可通过默认参数指定参数应采用的值。 以下代码片段演示默认参数的工作方式。 指定默认参数的限制的详细信息，请参阅[默认参数](../cpp/default-arguments.md)。  
  
```  
// expre_Ellipses_and_Default_Arguments.cpp  
// compile with: /EHsc  
#include <iostream>  
  
// Declare the function print that prints a string,  
// then a terminator.  
void print( const char *string,  
            const char *terminator = "\n" );  
  
int main()  
{  
    print( "hello," );  
    print( "world!" );  
  
    print( "good morning", ", " );  
    print( "sunshine." );  
}  
  
using namespace std;  
// Define print.  
void print( const char *string, const char *terminator )  
{  
    if( string != NULL )  
        cout << string;  
  
    if( terminator != NULL )  
        cout << terminator;  
}  
```  
  
 上面的程序声明一个采用两个参数的函数 `print`。 而第二个参数 `terminator` 具有默认值 `"\n"`。 在**主要**，对的前两个调用`print`允许默认第二个参数提供新行以终止打印的字符串。 第三个调用为第二个参数指定显式值。 该程序的输出为  
  
```  
hello,  
world!  
good morning, sunshine.  
```  
  
## <a name="see-also"></a>请参阅  
 [表达式类型](../cpp/types-of-expressions.md)