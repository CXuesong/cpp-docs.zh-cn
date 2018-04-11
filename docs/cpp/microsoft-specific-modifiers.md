---
title: "Microsoft 专用的修饰符 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
ms.assetid: 22c7178c-f854-47fa-9de6-07d23fda58e1
caps.latest.revision: "10"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 1f9533ffc2d21c3e8ee006fc97f7c61f4cb41115
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="microsoft-specific-modifiers"></a>Microsoft 专用的修饰符
本节从以下方面描述特定于 Microsoft 的 C++ 扩展：  
  
-   [基于寻址](../cpp/based-addressing.md)，使用指针用作其他指针可进行偏移的基的做法  
  
-   [函数调用约定](../cpp/calling-conventions.md)  
  
-   扩展存储类特性声明与[__declspec](../cpp/declspec.md)关键字  
  
-   [__W64](../cpp/w64.md)关键字  
  
 许多特定于 Microsoft 的关键字可用于修改声明符以构成派生类型。 有关声明符的详细信息，请参阅[声明符](http://msdn.microsoft.com/en-us/8a7b9b51-92bd-4ac0-b3fe-0c4abe771838)。  
  
### <a name="microsoft-specific-keywords"></a>Microsoft 专用关键字  
  
|关键字|含义|是否已用于构成派生类型？|  
|-------------|-------------|---------------------------------|  
|[__based](../cpp/based-grammar.md)|后跟的名称将 32 位偏移量声明为包含在声明中的 32 位基。|是|  
|[__cdecl](../cpp/cdecl.md)|后跟的名称使用 C 命名和调用约定。|是|  
|[__declspec](../cpp/declspec.md)|后跟的名称指定 Microsoft 特定的存储类特性。|否|  
|[__fastcall](../cpp/fastcall.md)|后跟的名称声明一个函数，该函数使用寄存器（如果可用）而不是用于参数传递的堆栈。|是|  
|[__restrict](../cpp/extension-restrict.md)|类似于 __declspec ([限制](../cpp/restrict.md))，但在变量上使用。|否|  
|[__stdcall](../cpp/stdcall.md)|后跟的名称指定遵循标准调用约定的函数。|是|  
|[__w64](../cpp/w64.md)|将数据类型标记为 64 位编译器上较大数据类型。|否|  
|[__unaligned](../cpp/unaligned.md)|指定指向类型或其他数据的指针未对齐。|否|  
|[__vectorcall](../cpp/vectorcall.md)|后跟的名称声明一个函数，如果可能，该函数将使用寄存器（包括 SSE 寄存器）而不是用于参数传递的堆栈。|是|  
  
## <a name="see-also"></a>请参阅  
 [C++ 语言参考](../cpp/cpp-language-reference.md)