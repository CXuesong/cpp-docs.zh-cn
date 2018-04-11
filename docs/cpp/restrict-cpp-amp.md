---
title: "限制 (C++ AMP) |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- cpu_CPP
- amp_CPP
dev_langs: C++
helpviewer_keywords: restrict clause (C++ AMP)
ms.assetid: 07d3291f-7edf-456b-8828-283ac8673661
caps.latest.revision: "22"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 60ac40e2cb64c307574d14c1f7cc7a5290c740ac
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="restrict-c-amp"></a>restrict (C++ AMP)
可将限制说明符应用于函数和 lambda 声明。 它会强制对函数中的代码以及使用 C++ Accelerated Massive Parallelism (C++ AMP) 运行时的应用程序中的函数的行为实施限制。  
  
> [!NOTE]
>  璝惠`restrict`关键字属于`__declspec`存储类特性，请参阅[限制](../cpp/restrict.md)。  
  
 `restrict` 子句采用以下形式：  
  
|子句|描述|  
|------------|-----------------|  
|`restrict(cpu)`|函数可使用完整的 C++ 语言。 只有使用 restrict(cpu) 函数声明的其他函数功能可调用函数。|  
|`restrict(amp)`|函数只能使用 C++ AMP 可为其加速的一部分 C++ 语言。|  
|一系列 `restrict(cpu)` 和 `restrict(amp)`。|函数必须遵循 `restrict(cpu)` 和 `restrict(amp)`的限制。 函数可由使用 `restrict(cpu)`、`restrict(amp)`、`restrict(cpu, amp)` 或 `restrict(amp, cpu)` 声明的函数调用。<br /><br /> `restrict(A) restrict(B)` 形式可以编写为 `restrict(A,B)`。|  
  
## <a name="remarks"></a>备注  
 `restrict` 关键字是上下文关键字。 限制说明符、`cpu` 和 `amp` 不是保留字。 说明符列表不可扩展。 没有 `restrict` 子句的函数与具有 `restrict(cpu)` 子句的函数相同。  
  
 包含 `restrict(amp)` 子句的函数具有以下限制：  
  
-   函数只能调用具有 `restrict(amp)` 子句的函数。  
  
-   函数必须可内联。  
  
-   函数只能声明 `int`、`unsigned int`、`float` 和 `double` 变量，以及只包含这些类型的类和结构。 也允许使用 `bool`，但如果您在复合类型中使用它，则它必须是 4 字节对齐的。  
  
-   Lambda 函数无法通过引用捕获，并且无法捕获指针。  
  
-   仅支持引用和单一间接指针作为局部变量、函数参数和返回类型。  
  
-   不允许使用以下项：  
  
    -   递归。  
  
    -   用声明的变量[易失性](../cpp/volatile-cpp.md)关键字。  
  
    -   虚函数。  
  
    -   指向函数的指针。  
  
    -   指向成员函数的指针。  
  
    -   结构中的指针。  
  
    -   指向指针的指针。  
  
    -   `goto` 语句。  
  
    -   Labeled 语句。  
  
    -   `try`、`catch` 或 `throw` 语句。  
  
    -   全局变量。  
  
    -   静态变量。 使用[tile_static 关键字](../cpp/tile-static-keyword.md)相反。  
  
    -   `dynamic_cast` 强制转换。  
  
    -   `typeid` 运算符。  
  
    -   asm 声明。  
  
    -   Varargs。  
  
 函数限制的讨论，请参阅[restrict(amp) 限制](http://go.microsoft.com/fwlink/p/?LinkId=251089)。  
  
## <a name="example"></a>示例  
 下面的示例演示如何使用`restrict(amp)`子句。  
  
```  
  
void functionAmp() restrict(amp) {}   
void functionNonAmp() {}   
  
void callFunctions() restrict(amp)   
{   
    // int is allowed.  
    int x;  
    // long long int is not allowed in an amp-restricted function. This generates a compiler error.  
    // long long int y;   
  
    // Calling an amp-restricted function is allowed.  
    functionAmp();   
  
    // Calling a non-amp-restricted function is not allowed.  
    // functionNonAmp();   
  
}  
```  
  
## <a name="see-also"></a>请参阅  
 [C++ AMP (C++ Accelerated Massive Parallelism)](../parallel/amp/cpp-amp-cpp-accelerated-massive-parallelism.md)