---
title: "声明符概述 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords: declarators, about declarators
ms.assetid: 0f2e2312-80bd-4154-8345-718bd9ed2173
caps.latest.revision: "10"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 18a3f12ac87f0165c74aaa487913f679f1a9941e
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="overview-of-declarators"></a>声明符概述
声明符是指定对象或函数名称的声明的组成部分。 声明符还指定是否命名对象是对象、指针、引用还是数组。  尽管声明符不指定基类型，但它们会修改基类型中的类型信息以指定派生类型，如指针、引用和数组。  声明符应用于函数，与类型说明符（用于完全指定函数的返回类型是对象、指针还是引用）的工作方式相同。 (中讨论的说明符[声明和定义](declarations-and-definitions-cpp.md)，类型和存储类等属性。 在本部分和讨论的修饰符[Microsoft 专用的修饰符](../cpp/microsoft-specific-modifiers.md)，修改声明符。)下图显示 `MyFunction` 的完整声明，并对声明的各个组成部分进行了标注。  
  
 ![修饰符、 说明符和声明符](../cpp/media/vc38qy1.gif "vc38QY1")  
说明符、修饰符和声明符  
  
 **Microsoft 专用**  
  
 大多数 Microsoft 扩展关键字可用作修饰符来构成派生类型；它们不是说明符或声明符。 (请参阅[Microsoft 专用的修饰符](../cpp/microsoft-specific-modifiers.md)。)  
  
 **结束 Microsoft 专用**  
  
 声明符出现在声明语法中可选的说明符列表后面。 中讨论了这些说明符[声明。](declarations-and-definitions-cpp.md) 声明可包含多个声明符，但每个声明符只声明一个名称。  
  
 以下示例声明显示了如何将说明符和声明符组合以构成一个完整的声明：  
  
```  
const char *pch, ch;  
```  
  
 在前面的声明中，关键字**const**和`char`组成了说明符的列表。 列出了两个声明：`*pch` 和 `ch`。  声明多个实体的声明由类型说明符后跟声明符的逗号分隔列表组成，并以分号终止。  
  
 **简单对象的声明符**  
  
 简单对象的声明符（如 int 或 double）可以就是对象的名称（带有可选的括号）。  
  
 `int i; // declarator is i`  
  
 `int (i); // declarator is (i)`  
  
 **指针、 引用和数组的声明符**  
  
 在名称前面插入的指针运算符使对象成为指针或引用。   **\*** 运算符声明为指针; 名称 **&** 运算符声明为引用。  
  
```  
int *i; // declarator is *i  
int **i; // declarator is **i;  
int &i = x; // declaratory is &i  
```  
  
 追加的 `const` 或 `volatile` 为指针提供了这些特殊属性。  在声明符（而非类型说明符）中使用这些说明符将会修改指针的属性，而不是指针指向的对象：  
  
```  
char *const cpc; // const pointer to char   
const char *pcc; // pointer to const char   
const char *const cpcc; // const pointer to const char  
```  
  
 在中，可找到详细信息[const 和 volatile 指针](../cpp/const-and-volatile-pointers.md)。  
  
 指向类或结构的成员的指针使用适当的嵌套名称说明符来声明：  
  
```  
int X::* pIntMember;   
int ::X::* pIntMember; // the initial :: specifies X is in global scope  
char Outer::Inner::* pIntMember; // pointer to char in a nested class  
```  
  
 在名称后面括起可选常量表达式的方括号将使对象成为数组。  后续的方括号将声明数组的其他维度。  
  
```  
int i[5]; // array with five elements of type int numbered from 0 to 4  
int i[]; // array of unknown size  
char *s[4]; // array of pointers to char  
int i[2][2]; // two dimensional array  
```  
  
 **函数的声明符**  
  
 名称后面的包含参数列表的圆括号可用来声明函数。  下面声明了一个 `int` 返回类型的函数和三个 `int` 类型的参数。  
  
```  
int f(int a, int b, int c);  
```  
  
 针对函数的指针和引用通过将指针或引用运算符添加到函数名称来声明，如下所示。  需要使用圆括号（通常为可选）来区分指向函数的指针和返回指针的函数：  
  
```  
int (*pf)(int); // pointer to function returning int  
int *f(int i); // function returning pointer to int  
int (&pf)(int); // reference to function   
```  
  
 指向成员函数的指针通过嵌套名称说明符来区分：  
  
```  
int (X::* pmf)(); // pointer to member function of X returning int  
int* (X::* pmf)(); // pointer to member function returning pointer to int  
```  
  
 另请参阅[指向成员的指针](../cpp/pointers-to-members.md)。  
  
 **函数和同一声明中的对象**  
  
 函数和对象可在同一声明中进行声明，如下所示：  
  
```  
int i, *j, f(int k);  // int, pointer to int, function returning int  
```  
  
 该语法在某些情况下可能令人误解。  以下声明  
  
```  
int* i, f(int k);  // pointer to int, function returning int (not int*)  
```  
  
 可能看起来像 `int` 指针的声明和返回 `int*` 的函数，但它不是。  那是因为 * 是 `i` 的声明符的一部分，而不是 `f` 的声明符的一部分。  
  
 **用 typedef 简化声明符语法**  
  
 但是，更好的方法是使用 `typedef` 或括号与 `typedef` 关键字的组合。 请考虑声明指向函数的指针的数组：  
  
```  
//  Function returning type int that takes one   
//   argument of type char *.  
typedef int (*PIFN)( char * );  
//  Declare an array of 7 pointers to functions   
//   returning int and taking one argument of type   
//   char *.  
PIFN pifnDispatchArray[7];  
```  
  
 可在不使用 `typedef` 声明的情况下编写等效声明，但这种方法很复杂，以至于出错的可能性掩盖了它带来的好处：  
  
```  
int ( *pifnDispatchArray[7] )( char * );  
```  
  
 有关 typedef 的详细信息，请参阅[别名和 typedef](aliases-and-typedefs-cpp.md)。  
  
 指针、引用以及单个基类型的数组可以在单个声明中合并（以逗号分隔）为  
  
```  
int a, *b, c[5], **d, &e=a;  
```  
  
 **更复杂的声明符语法**  
  
-   可将指针、引用、数组和函数声明符组合起来以指定这样一种对象：指向函数的指针、指向数组的指针等指针的数组。  
  
-   以下递归语法全面介绍了指针声明符语法。  
  
-   `declarator` 被定义为以下项之一：  
  
```  
1. identifier   
2. qualified-name   
3. declarator ( argument-list ) [cv-qualfiers] [exception-spec]  
4. declarator [ [ constant-expression ] ]   
  
5. pointer-operatordeclarator   
6. ( declarator )  
```  
  
-   和*指针运算符*是之一：  
  
```  
  
      * [cv-qualifiers]  
& [cv-qualifiers]  
::nested-name-specifier * [cv-qualfiers]  
```  
  
 由于一个声明符可能包含多个声明符，因此可以使用上述规则来构造更复杂的派生类型，如指针数组和返回函数指针数组的函数。  若要制定构造的所有步骤，请以呈现基数据类型的标识符开头，并应用上面的语法规则（将上一个表达式作为 `declarator`）。  应用语法规则的顺序应该与用英语表示表达式的方法相反。  如果将应用*指针运算符*语法规则应用于数组或函数表达式，如果你需要指向该数组或函数下, 表中的最后一行所示使用括号。  
  
 以下示例显示了“指向‘指向 int 的 10 个指针的数组’的指针”的构造。  
  
|文字表达式|声明符|应用的语法规则|  
|-----------------------|----------------|-------------------------|  
||`i`|1|  
|指针|`*i`|5|  
|10 的数组|`(*i)[10]`|4|  
|指针|`*((*i)[10])`|6，然后 5|  
  
 当使用多个指针、引用、数组或函数修饰符时，声明符可能变得十分复杂。  主题[解释更复杂声明符](../c-language/interpreting-more-complex-declarators.md)描述如何阅读更复杂的声明符语法。  主题是适用于 C 和 C++，但在 C++，任何位置 * 用于指示指针，一个限定的名称，例如 MyClass::\*可能用于指定指向类的成员的指针。