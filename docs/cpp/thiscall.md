---
title: "__thiscall |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- __thiscall
- __thiscall_cpp
dev_langs: C++
helpviewer_keywords: __thiscall keyword [C++]
ms.assetid: a6a22dd2-0101-4885-b33b-22f6057965df
caps.latest.revision: "14"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: a55f7d288758b345dfc4f182f2153e0d39a1b349
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="thiscall"></a>__thiscall
## <a name="microsoft-specific"></a>Microsoft 专用  
 `__thiscall`调用约定成员函数上使用，并且是由不使用变量参数的 C++ 成员函数的默认调用约定。 下`__thiscall`，被调用方将清理堆栈，这是不可能`vararg`函数。 参数推送到堆栈上从右向左，与`this`通过寄存器 ECX，而不是在 x86 上的堆栈，传递的指针体系结构。  
  
 若要使用的一个原因`__thiscall`在其成员函数使用的类`__clrcall`默认情况下。 在这种情况下，你可以使用`__thiscall`将各个成员函数可从本机代码调用。  
  
 使用编译时[/clr: pure](../build/reference/clr-common-language-runtime-compilation.md)，所有函数和函数指针`__clrcall`除非另有指定。 **/clr:pure** 和 **/clr:safe** 编译器选项在 Visual Studio 2015 中已弃用。  
  
 在 Visual C++ 2005年之前的版本中，thiscall 调用约定无法显式指定在程序中，因为`thiscall`不是一个关键字。  
  
 `vararg`成员函数使用`__cdecl`调用约定。 所有函数参数都推送到堆栈上，使用`this`指针将位于堆栈上上一次  
  
 由于此调用约定仅适用于 C++，没有任何 C 名称修饰方案。  
  
 ARM 上和[!INCLUDE[vcprx64](../assembler/inline/includes/vcprx64_md.md)]机，`__thiscall`接受和忽略由编译器。  
  
 对于非静态类函数，如果函数是超行定义的，则调用约定修饰符不必在超行定义中指定。 也就是说，对于类非静态成员方法，在定义时假定声明期间指定的调用约定。  
  
**结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [参数传递和命名约定](../cpp/argument-passing-and-naming-conventions.md)