---
title: "编译器错误 C2731 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-tools
ms.tgt_pltfrm: 
ms.topic: error-reference
f1_keywords: C2731
dev_langs: C++
helpviewer_keywords: C2731
ms.assetid: 9b563999-febd-4582-9147-f355083c091e
caps.latest.revision: "8"
author: corob-msft
ms.author: corob
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: b6df60a548c5601c7f7ff7827a8982dab56605ce
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="compiler-error-c2731"></a>编译器错误 C2731
identifier： 无法重载函数  
  
 函数`main`， `WinMain`， `DllMain`，和`LibMain`无法进行重载。  
  
 下面的示例生成 C2731:  
  
```  
// C2731.cpp  
extern "C" void WinMain(int, char *, char *);  
void WinMain(int, short, char *, char*);   // C2731  
```