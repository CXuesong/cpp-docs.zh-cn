---
title: "数值、 布尔和指针文本 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- literals, C++
- constants, literals
- literals [C++]
ms.assetid: 17c09fc3-3ad7-47e2-8b48-ba8ae994edc8
caps.latest.revision: "16"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 91f79a2703dee8a162b971a78eba7e13a9849b43
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="numeric-boolean-and-pointer-literals--c"></a>数值、 布尔和指针文本 （C++）
文本是一种直接表示值的程序元素。 本文介绍整数、浮点、布尔和指针类型的文本。 有关字符串和字符文本的信息，请参阅[字符串和字符文本 （C++）](../cpp/string-and-character-literals-cpp.md)。 你还可以定义基于任何这些类别中; 你自己的文本有关详细信息请参阅[用户定义文本 （C++）](../cpp/user-defined-literals-cpp.md)  
  
 . 你可以在许多上下文中使用文本，但文本的最常用法是初始化命名变量以及将参数传递给函数：  
  
```  
const int answer = 42; // integer literal  
double d = sin(108.87);     //floating point literal passed to sin function  
bool b = true;              // boolean literal  
MyClass* mc = nullptr;      // pointer literal  
  
```  
  
 有时需要指示编译器如何解释某个文本或者为其赋予哪种特定类型。 你可以通过为文本追加前缀或后缀来达到此目的。 例如，前缀 0x 指示编译器将其后面的数字解释为十六进制值，例如 0x35。 ULL 后缀指示编译器将值视为 `unsigned long long` 类型，比如在 5894345ULL 中。 有关每个文本类型的前缀和后缀的完整列表，请参阅以下各节。  
  
## <a name="syntax"></a>语法  
  
## <a name="integer-literals"></a>整数文本  
 整数文本以数字开头，没有小数部分或指数。 你可以指定十进制、八进制或十六进制形式的整数文本。 它们可指定有符号类型或无符号类型以及长类型或短类型。  
  
 如果没有前缀或后缀，编译器将为整型文本值赋予 `int` 类型（32 位），前提是该值符合该类型，否则将赋予 `long long` 类型（64 位）。  
  
 要指定十进制整型文本，请以非零数字作为规范的开头。 例如:  
  
```  
int i = 157;   // Decimal literal  
int j = 0198;       // Not a decimal number; erroneous octal literal  
int k = 0365;       // Leading zero specifies octal literal, not decimal  
int m = 36'000'000  // digit separators make large values more readable  
int   
```  
  
 要指定八进制整型文本，请以 0 作为规范的开头，后跟 0 到 7 之间的一系列数字。 在指定八进制文本时，使用数字 8 和 9 是错误做法。 例如:  
  
```  
int i = 0377;   // Octal literal  
int j = 0397;        // Error: 9 is not an octal digit  
```  
  
 要指定十六进制整型文本，请以 `0x` 或 `0X` 作为规范的开头（“x”的大小写形式并不重要），后跟 `0` 到 `9` 以及 `a`（或 `A`）到 `f`（或 `F`）之间的一系列数字。 十六进制数字 `a`（或 `A`）到 `f`（或 `F`）表示介于 10 和 15 之间的值。 例如:  
  
```  
int i = 0x3fff;   // Hexadecimal literal  
int j = 0X3FFF;        // Equal to i  
```  
  
 若要指定无符号的类型，请使用**u**或**U**后缀。 若要指定长类型，请使用**l**或**L**后缀。 要指定 64 位整型类型，请使用 LL 或 ll 后缀。 虽然仍支持 i64 后缀，但应避免使用，因为它特定于 Microsoft 且不可移植。 例如:  
  
```  
unsigned val_1 = 328u;             // Unsigned value  
long val_2 = 0x7FFFFFL;                 // Long value specified   
                                        //  as hex literal  
unsigned long val_3 = 0776745ul;        // Unsigned long value  
auto val_4 = 108LL;                           // signed long long  
auto val_4 = 0x8000000000000000ULL << 16;     // unsigned long long   
```  
  
 **数字分隔符**： 你可以使用单引号字符 （撇号） 分隔中较大的数，以使它们更易于供人读取的位置值。 分隔符不会对编译产生任何影响。  
  
```  
long long i = 24'847'458'121  
```  
  
## <a name="floating-point-literals"></a>浮点文本  
 浮点文本指定必须具有小数部分的值。 这些值包含小数点 (**。**) 并可能包含指数。  
  
 浮点文本具有“尾数”（用于指定数字的值）、“指数”（用于指定数字的量级）和可选的后缀（用于指定文本的类型）。 指定的尾数的格式是一系列位数后跟一个句点，再后跟表示数字的小数部分的可选的一系列位数。 例如：  
  
```  
18.46  
38.  
```  
  
 指数（如果有）指定数字的量级为 10 次幂，如以下示例所示：  
  
```  
18.46e0      // 18.46  
18.46e1           // 184.6  
```  
  
 可能使用指定指数**e**或**E**，具有相同的含义后, 跟一个可选符号 (+ 或-) 和一系列数字。  如果指数存在，则整数（如 `18E0`）中不需要尾随的小数点。  
  
 浮点文本默认为键入**double**。 通过使用后缀**f**或**l** (或**F**或**L** — 后缀不区分大小写)，文本可以指定为**float**或`long double`分别。  
  
 尽管`long double`和**double**具有相同的表示形式，它们不相同的类型。 例如，您可能有类似于下面的重载函数  
  
```  
void func( double );  
```  
  
 和  
  
```  
void func( long double );  
```  
  
## <a name="boolean-literals"></a>布尔文本  
 布尔文本为 `true` 和 `false`。  
  
## <a name="pointer-literal-c11"></a>指针文本 (C++11)  
 C++ 引入[nullptr](../cpp/nullptr.md)文本来指定初始化为零的指针。 在可移植代码中，应使用 `nullptr`，而不是整型类型零或宏（如 NULL）。  
  
## <a name="binary-literals-c14"></a>二进制文本 (C++14)  
 可以通过使用 `0B` 或 `0b` 前缀，后跟一系列 1 和 0，来指定二进制文本：  
  
```  
  
auto x = 0B001101 ; // int  
auto y = 0b000001 ; // int  
```  
  
## <a name="avoid-using-literals-as-magic-constants"></a>避免将文本用作“魔术常量”  
 你可以在表达式和语句中直接使用文本，虽然这种编程做法并不一定好用：  
  
```  
if (num < 100)  
    return "Success";  
  
```  
  
 在上一个示例中，使用能够传达明确含义的命名常量可能会更好，例如“MAXIMUM_ERROR_THRESHOLD”。 如果最终用户看到返回值“成功”，则使用可以存储在文件单一位置中的命名字符串常量可能会更好，它可以在该位置本地化为其他语言。 使用命名常量可帮助你自己和其他人了解代码的含义。  
  
## <a name="see-also"></a>请参阅  
 [词法约定](../cpp/lexical-conventions.md)   
 [C++ 字符串文本](../cpp/string-and-character-literals-cpp.md)   
 [C++ 用户定义的文本](../cpp/user-defined-literals-cpp.md)