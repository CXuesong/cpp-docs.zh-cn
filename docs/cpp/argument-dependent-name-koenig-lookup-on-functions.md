---
title: "针对函数的依赖于参数的名称 (Koenig) 查找 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- Koenig lookup
- argument-dependent lookup [C++]
ms.assetid: c0928401-da2c-4658-942d-9ba4df149c35
caps.latest.revision: "10"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 5a468d5eef9a400bfa5e12c90ca62e05ea2f160d
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="argument-dependent-name-koenig-lookup-on-functions"></a>针对函数的依赖于参数的名称 (Koenig) 查找
编译器可以使用依赖于参数的名称查找来查找非限定函数调用的定义。 依赖于参数的名称查找也称为 Koenig 查找。 在命名空间、类、结构、联合或模板的层次结构中定义函数调用中每个参数的类型。 如果指定未限定[后缀](../cpp/postfix-expressions.md)函数调用，编译器将搜索与每个参数类型关联的层次结构中的函数定义。  
  
## <a name="example"></a>示例  
 在此示例中，编译器注意到函数 `f()` 采用了参数 `x`。 参数 `x` 是命名空间 `A::X` 中定义的类型 `A`。 编译器搜索命名空间 `A` 并查找采用类型 `f()` 的参数的函数 `A::X` 的定义。  
  
```  
// argument_dependent_name_koenig_lookup_on_functions.cpp  
namespace A  
{  
   struct X  
   {  
   };  
   void f(const X&)  
   {  
   }  
}  
int main()  
{  
// The compiler finds A::f() in namespace A, which is where   
// the type of argument x is defined. The type of x is A::X.  
   A::X x;  
   f(x);     
}  
```  
