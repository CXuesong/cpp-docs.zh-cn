---
title: "const 和 volatile 指针 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: article
dev_langs: C++
helpviewer_keywords:
- volatile keyword [C++], and pointers
- pointers, and const
- pointers, and volatile
- const keyword [C++], volatile pointers
ms.assetid: 0c92dc6c-400e-4342-b345-63ddfe649d7e
caps.latest.revision: "10"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 68089c80528265a4375767d9f0a744cb95cb970b
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="const-and-volatile-pointers"></a>固定和可变指针
[Const](../cpp/const-cpp.md)和[易失性](../cpp/volatile-cpp.md)关键字更改指针的方式。 **Const**关键字指定无法在初始化后修改的指针; 指针将受到保护之后进行修改。  
  
 `volatile` 关键字指定与后跟的名称关联的值可由用户应用程序中的操作以外的操作修改。 因此，`volatile` 关键字对于声明共享内存中可由多个进程访问的对象或用于与中断服务例程通信的全局数据区域很有用。  
  
 如果某个名称被声明为 `volatile`，则每当程序访问该名称时，编译器都会重新加载内存中的值。 这将显著减少可能的优化。 但是，当对象的状态可能意外更改时，这是保证可预见的程序性能的唯一方法。  
  
 若要声明通过指针视为指向的对象**const**或`volatile`，使用以下形式声明：  
  
```  
const char *cpch;  
volatile char *vpch;  
```  
  
 来声明指针值-即指针中存储的实际地址 — 作为**const**或`volatile`，使用以下形式声明：  
  
```  
char * const pchc;  
char * volatile pchv;  
```  
  
 C++ 语言禁止将允许修改的对象的分配或指针声明为**const**。 此类赋值会移除用来声明对象或指针的信息，从而违反原始声明的意图。 请考虑以下声明：  
  
```  
const char cch = 'A';  
char ch = 'B';  
```  
  
 对于两个对象的前面的声明 (`cch`，类型的**const char**，和`ch`，类型的**char)**，以下声明/初始化存在有效：  
  
```  
const char *pch1 = &cch;  
const char *const pch4 = &cch;  
const char *pch5 = &ch;  
char *pch6 = &ch;  
char *const pch7 = &ch;  
const char *const pch8 = &ch;  
```  
  
 以下声明/初始化存在错误。  
  
```  
char *pch2 = &cch;   // Error  
char *const pch3 = &cch;   // Error  
```  
  
 `pch2` 的声明声明了一个可以用来修改常量对象的指针，因此不允许使用。 `pch3` 的声明指定 `pointer` 是常量，而不是对象；与不允许使用 `pch2` 的原因相同，也不允许使用该声明。  
  
 以下八个赋值显示了通过指针进行的赋值以及对前面的声明的指针值的更改；现在，假设 `pch1` 到 `pch8` 的初始化是正确的。  
  
```  
*pch1 = 'A';  // Error: object declared const  
pch1 = &ch;   // OK: pointer not declared const  
*pch2 = 'A';  // OK: normal pointer  
pch2 = &ch;   // OK: normal pointer  
*pch3 = 'A';  // OK: object not declared const  
pch3 = &ch;   // Error: pointer declared const  
*pch4 = 'A';  // Error: object declared const  
pch4 = &ch;   // Error: pointer declared const  
```  
  
 指针声明为`volatile`，或组合的**const**和`volatile`，遵循相同的规则。  
  
 指向**const**对象通常用于函数声明中，如下所示：  
  
```  
errno_t strcpy_s( char *strDestination, size_t numberOfElements, const char *strSource );  
```  
  
 前面的语句声明一个函数， [strcpy_s](../c-runtime-library/reference/strcpy-s-wcscpy-s-mbscpy-s.md)，其中两个三个参数都是指针类型的`char`。 因为通过引用传递的参数并且不通过值，该函数将为随意修改同时`strDestination`和`strSource`如果`strSource`了未声明为**const**。 声明`strSource`作为**const**向调用方保证`strSource`不能更改被调用函数。  
  
> [!NOTE]
>  因为没有从标准转换*typename*  **\*** 到**const** *typename*  **\***，它是合法的类型的参数传递**char \*** 到[strcpy_s](../c-runtime-library/reference/strcpy-s-wcscpy-s-mbscpy-s.md)。 但是，反之不可行的;任何隐式转换存在删除**const**从对象或指针的属性。  
  
 A **const**给定类型的指针可以分配给同一类型的指针。 但是，指针，不是**const**不能分配给**const**指针。 以下代码显示了正确和错误的赋值：  
  
```  
// const_pointer.cpp  
int *const cpObject = 0;  
int *pObject;  
  
int main() {  
pObject = cpObject;  
cpObject = pObject;   // C3892  
}  
```  
  
 以下示例显示了当有指针指向某个指向对象的指针时如何将对象声明为 const。  
  
```  
// const_pointer2.cpp  
struct X {  
   X(int i) : m_i(i) { }  
   int m_i;  
};  
  
int main() {  
   // correct  
   const X cx(10);  
   const X * pcx = &cx;  
   const X ** ppcx = &pcx;  
  
   // also correct  
   X const cx2(20);  
   X const * pcx2 = &cx2;  
   X const ** ppcx2 = &pcx2;  
}  
```  
  
## <a name="see-also"></a>请参阅  
 [指针](../cpp/pointers-cpp.md)