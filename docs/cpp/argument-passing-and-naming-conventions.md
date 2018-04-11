---
title: "参数传递和命名约定 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- argument passing [C++], conventions
- arguments [C++], widening
- coding conventions, arguments
- arguments [C++], passing
- registers, return values
- thiscall keyword [C++]
- naming conventions [C++], arguments
- arguments [C++], naming
- passing arguments [C++], conventions
- conventions [C++], argument names
ms.assetid: de468979-eab8-4158-90c5-c198932f93b9
caps.latest.revision: "9"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: d57afcd518455ffb836e4d724a68b7d3e5682d6c
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="argument-passing-and-naming-conventions"></a>参数传递和命名约定
**Microsoft 专用**  
  
 利用 Visual C++ 编译器，你可以指定用于传递参数的约定并返回函数与调用方之间的值。 并非所有约定都在所有支持的平台上可用，某些约定使用平台特定的实现。 在大多数情况下，将忽略在特定平台上指定不支持的约定的关键字或编译器开关，并将使用平台默认约定。  
  
 在 x86 平台上，所有参数在传递时都将加宽到 32 位。 返回值也将加宽到 32 位，并将通过 EAX 寄存器返回，但在 EDX:EAX 寄存器对中返回的 8 字节结构除外。 更大的结构将在 EAX 寄存器中作为指向隐藏返回结构的指针返回。 参数将从右到左推送到堆栈中。 不是 POD 的结构不会在寄存器中返回。  
  
 编译器将生成 prolog 和 epilog 代码来保存并还原 ESI、EDI、EBX 和 EBP 寄存器（如果在函数中使用了它们）。  
  
> [!NOTE]
>  当结构、联合或类由值从函数返回时，类型的所有定义需要相同，否则程序可能在运行时失败。  
  
 有关如何定义你自己的函数 prolog 和 epilog 代码的信息，请参阅[裸函数调用](../cpp/naked-function-calls.md)。  
  
 默认值有关的信息在针对 x64 平台上，请参阅的代码中调用约定[概述 x64 调用约定](../build/overview-of-x64-calling-conventions.md)。 有关在面向 ARM 平台的代码中调用约定问题的信息，请参阅[Visual C++ ARM 迁移的常见问题](../build/common-visual-cpp-arm-migration-issues.md)。  
  
 Visual C/C++ 编译器支持下列调用约定。  
  
|关键字|堆栈清理|参数传递|  
|-------------|-------------------|-----------------------|  
|[__cdecl](../cpp/cdecl.md)|调用方|在堆栈上按相反顺序推送参数（从右到左）|  
|[__clrcall](../cpp/clrcall.md)|不可用|按顺序将参数加载到 CLR 表达式堆栈上（从左到右）。|  
|[__stdcall](../cpp/stdcall.md)|被调用方|在堆栈上按相反顺序推送参数（从右到左）|  
|[__fastcall](../cpp/fastcall.md)|被调用方|存储在寄存器中，然后在堆栈上推送|  
|[__thiscall](../cpp/thiscall.md)|被调用方|在堆栈上推送**这**指针存储在 ECX 中|  
|[__vectorcall](../cpp/vectorcall.md)|被调用方|存储在寄存器中，然后按相反顺序在堆栈上推送（从右到左）|  
  
 有关相关信息，请参阅[过时的调用约定](../cpp/obsolete-calling-conventions.md)。  
  
 **结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [调用约定](../cpp/calling-conventions.md)