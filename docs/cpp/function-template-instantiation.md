---
title: "函数模板实例化 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- templates, instantiation
- function templates, instantiation
- instantiation, function templates
ms.assetid: f22a07c7-3ad1-465a-84f5-8737e274bd47
caps.latest.revision: "8"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 41bf7f6ba3a2a17c6355ee9239cadb6e5014ee96
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="function-template-instantiation"></a>函数模板实例化
当首次为每个类型调用函数模板时，编译器会创建一个实例化。 每个实例化是专用于该类型的模板化函数版本。 每次将该函数用于该类型时，此实例化都将调用。 如果有几个相同的实例化，即使在不同的模块中，也只有该实例化的一个副本将在可执行文件中结束。  
  
 允许在函数模板中为其中的参数不依赖于模板参数的任何参数和参数对进行函数参数转换。  
  
 通过将特定类型作为参数来声明模板，可以将函数模板显示实例化。 例如，以下代码是允许的：  
  
```cpp
// function_template_instantiation.cpp  
template<class T> void f(T) { }  
  
// Instantiate f with the explicitly specified template.  
// argument 'int'  
//  
template void f<int> (int);  
  
// Instantiate f with the deduced template argument 'char'.  
template void f(char);  
int main()  
{  
}  
```  
  
## <a name="see-also"></a>请参阅  
 [函数模板](../cpp/function-templates.md)
