---
title: "模板和名称解析 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
ms.assetid: 519ba7b5-cd25-4549-865a-9a7b9dffdc28
caps.latest.revision: "6"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 7cb6708a8b7631551a6e245c0777bcc6c9fb1a92
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="templates-and-name-resolution"></a>模板和名称解析

在模板定义中，有三种类型的名称。  
  
-   本部声明的名称，包括模板本身的名称以及在模板定义中声明的任何名称。  
  
-   模板定义之外的封闭范围中的名称。  
  
-   在某种程度上依赖于模板参数的名称（称为依赖名称）。  
  
 尽管前两个名称也属于类和函数范围，但模板定义中需要针对名称解析的特殊规则来处理依赖名称的提高的复杂性。 原因在于，在对模板进行实例化前，编译器几乎不知道这些名称，因为它们可能是依赖于所使用的模板参数的完全不同的类型。 将根据常用规则，在模板的定义点上查找非依赖名称。 将为所有模板专用化查找这些名称（独立于模板参数）一次。 在将模板实例化并为每个专用化单独查找模板之前，将不会查找依赖名称。  
  
 如果某个类型依赖于模板参数，则该类型属于依赖类型。 具体而言，如果类型是以下项，则属于依赖类型：  
  
-   模板参数本身：  
  
    ```cpp
    T  
    ```  
  
-   具有包括依赖类型的限定的限定名：  
  
    ```cpp
    T::myType  
    ```  
  
-   当非限定部分标识依赖类型时的限定名：  
  
    ```cpp
    N::T  
    ```  
  
-   其基类型属于依赖类型的不变或可变类型：  
  
    ```cpp
    const T  
    ```  
  
-   基于依赖类型的指针、引用、数组或函数指针类型：  
  
    ```cpp
    T *, T &, T [10], T (*)()  
    ```  
  
-   大小基于模板参数的数组：  
  
    ```cpp
    template <int arg> class X {  
    int x[arg] ; // dependent type  
    }  
    ```  
  
-   从模板参数构造的模板类型：  
  
    ```cpp
    T<int>, MyTemplate<T>  
    ```  
  
## <a name="type-dependence-and-value-dependence"></a>类型依赖和值依赖

 模板参数上的名称和表达式依赖项分为类型依赖项或值依赖项，具体取决于模板参数是类型参数还是值参数。 此外，在模板参数上有类型依赖项的模板中声明的任何标识符都被视为依赖值，使用值依赖表达式初始化的整数类型或枚举类型也是如此。  
  
 类型依赖和值依赖表达式是涉及类型依赖或值依赖的变量的表达式。 这些表达式可能有不同的语义，具体取决于用于模板的参数。  
  
## <a name="see-also"></a>请参阅

 [模板](../cpp/templates-cpp.md)
