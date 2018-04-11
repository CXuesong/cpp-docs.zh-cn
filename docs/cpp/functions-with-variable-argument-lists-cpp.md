---
title: "函数具有变量参数列表 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- arguments [C++], variable number of
- variable argument lists
- declarators, functions
- argument lists [C++], variable number of
- declaring functions [C++], variables
- function calls, variable number of arguments
ms.assetid: 27c2f83a-21dd-44c6-913c-2834cb944703
caps.latest.revision: "16"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: a715662ac6680218e3b15822108f429733569bfa
ms.sourcegitcommit: 9a0a287d6940591523af959ebdac5affa36220da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="functions-with-variable-argument-lists--c"></a>包含变量参数列表的函数 （C++）
如果函数声明中最后一个成员是省略号 (...)，则函数声明可采用数量可变的参数。 在这些情况下，C++ 只为显式声明的参数提供类型检查。 即使参数的数量和类型是可变的，在需要使函数泛化时也可使用变量参数列表。 函数的系列是一个示例使用变量参数列表的函数。`printf`*参数声明列表*  
  
## <a name="functions-with-variable-arguments"></a>包含变量参数的函数  
 若要访问声明后的参数，请使用包含在标准包含文件中的宏\<g.h > 如下所述。  
  
 **Microsoft 专用**  
  
 Microsoft C++ 允许将省略号指定为参数（如果省略号是最后一个参数且在逗号的后面）。 因此，声明 `int Func( int i, ... );` 是合法的，但 `int Func( int i ... );` 不是合法的。  
  
 **结束 Microsoft 专用**  
  
 采用数量可变的参数的函数声明至少需要一个占位符参数（即使不使用它）。 如果未提供此占位符参数，则无法访问其余参数。  
  
 当 `char` 类型的参数作为变量参数进行传递时，它们将被转换为 `int` 类型。 同样，当类型参数**float**传递作为变量参数，它们会转换为类型**double**。 其他类型的参数受常见整型和浮点型提升的限制。 请参阅[标准转换](standard-conversions.md)有关详细信息。  
  
 使用参数列表中的省略号 (...) 来声明需要变量列表的函数。 使用的类型和中所述的宏\<g.h > 包含文件访问参数传递的变量列表。 有关这些宏的详细信息，请参阅[va_arg、 va_copy、 va_end、 va_start](../c-runtime-library/reference/va-arg-va-copy-va-end-va-start.md)。 （处于 C 运行时库文档中）。  
  
 下面的示例演示如何与类型一起使用宏 (在中声明\<g.h >): 
  
```  
// variable_argument_lists.cpp  
#include <stdio.h>  
#include <stdarg.h>  
  
//  Declaration, but not definition, of ShowVar.  
void ShowVar( char *szTypes, ... );  
int main() {  
   ShowVar( "fcsi", 32.4f, 'a', "Test string", 4 );  
}  
  
//  ShowVar takes a format string of the form  
//   "ifcs", where each character specifies the  
//   type of the argument in that position.  
//  
//  i = int  
//  f = float  
//  c = char  
//  s = string (char *)  
//  
//  Following the format specification is a variable   
//  list of arguments. Each argument corresponds to   
//  a format character in the format string to which   
// the szTypes parameter points   
void ShowVar( char *szTypes, ... ) {  
   va_list vl;  
   int i;  
  
   //  szTypes is the last argument specified; you must access   
   //  all others using the variable-argument macros.  
   va_start( vl, szTypes );  
  
   // Step through the list.  
   for( i = 0; szTypes[i] != '\0'; ++i ) {  
      union Printable_t {  
         int     i;  
         float   f;  
         char    c;  
         char   *s;  
      } Printable;  
  
      switch( szTypes[i] ) {   // Type to expect.  
         case 'i':  
            Printable.i = va_arg( vl, int );  
            printf_s( "%i\n", Printable.i );  
         break;  
  
         case 'f':  
             Printable.f = va_arg( vl, double );  
             printf_s( "%f\n", Printable.f );  
         break;  
  
         case 'c':  
             Printable.c = va_arg( vl, char );  
             printf_s( "%c\n", Printable.c );  
         break;  
  
         case 's':  
             Printable.s = va_arg( vl, char * );  
             printf_s( "%s\n", Printable.s );  
         break;  
  
         default:  
         break;  
      }  
   }  
   va_end( vl );  
}  
//Output:   
// 32.400002  
// a  
// Test string  
```  
  
 上一个示例演示以下重要概念：  
  
1.  在访问任何变量参数前，必须建立一个列表标记作为类型 `va_list` 的变量。 在前面的示例中，该标记称为 `vl`。  
  
2.  使用 `va_arg` 宏访问各个参数。 必须告知 `va_arg` 宏要检索的参数的类型，以便它可以从堆栈中传输正确的字节数。 如果为 `va_arg` 指定的大小的类型与通过调用程序提供的类型不同，则结果是不可预知的。  
  
3.  应将使用 `va_arg` 宏获取的结果显式强制转换为所需类型。  
  
 必须调用宏以终止可变参数处理。`va_end`