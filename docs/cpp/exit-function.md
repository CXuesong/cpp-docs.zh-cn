---
title: "exit 函数 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: Exit
dev_langs: C++
helpviewer_keywords: exit function
ms.assetid: 26ce439f-81e2-431c-9ff8-a09a96f32127
caps.latest.revision: "6"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: ee6a34a70465904e6725f42e68eb4a00c03a1661
ms.sourcegitcommit: 9a0a287d6940591523af959ebdac5affa36220da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="exit-function"></a>exit 函数
**退出**在标准包含文件中声明的函数\<stdlib.h >，将终止 C++ 程序。  
  
 提供作为的参数的值**退出**返回到操作系统作为程序的返回代码或退出代码。 按照约定，返回代码为零表示该程序已成功完成。  
  
> [!NOTE]
>  你可以使用常量`EXIT_FAILURE`和`EXIT_SUCCESS`在中定义\<stdlib.h >，以指示成功或失败的程序。  
  
 发出`return`语句从**主要**函数等效于调用**退出**作为其参数返回值的函数。  
  
 有关详细信息，请参阅[退出](../c-runtime-library/reference/exit-exit-exit.md)中*运行时库参考*。  
  
## <a name="see-also"></a>请参阅  
 [程序终止](../cpp/program-termination.md)