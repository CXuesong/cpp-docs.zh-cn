---
title: "noexcept （C++） |Microsoft 文档"
ms.custom: 
ms.date: 01/12/2018
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: noexcept_cpp
dev_langs: C++
ms.assetid: df24edb9-c6a6-4e37-9914-fd5c0c3716a8
caps.latest.revision: "5"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 9b78c19cb156312b6087b75e50c0e0fa28a00246
ms.sourcegitcommit: c2e990450ccd528d85b2783fbc63042612987cfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2018
---
# <a name="noexcept-c"></a>noexcept (C++)
**C++ 11:**指定函数是否可能引发异常。  
  
## <a name="syntax"></a>语法  
  
> *noexcept-expression*:  
> &nbsp;&nbsp;&nbsp;&nbsp;**noexcept**  
> &nbsp;&nbsp;&nbsp;&nbsp;**noexcept(** *constant-expression* **)**  
  
### <a name="parameters"></a>参数  
 *constant-expression*  
 类型的常量表达式`bool`表示是否可能的异常类型的集为空。 无条件版本相当于`noexcept(true)`。  
  
## <a name="remarks"></a>备注  
 A *noexcept 表达式*是一种类型的*异常规范*，表示一组的可能的异常处理程序退出的任何异常由匹配的类型的函数声明后缀函数。 一元条件运算符`noexcept(` *constant_expression* `)`其中*constant_expression* yeilds `true`，及其无条件同义词`noexcept`，指定的一套可能可以退出函数的异常类型为空。 这就是说，函数绝不会引发异常，而永远不会允许异常传播其作用域之外。 运算符`noexcept(` *constant_expression* `)`其中*constant_expression* yeilds `false`，或不存在的异常规范 (除析构函数或释放函数），指示的潜在可以退出该函数的异常集的所有类型的集合。  
 
 标记一个函数作为`noexcept`仅当调用，直接或间接的所有函数都都还`noexcept`或`const`。 编译器不一定会检查可能归因于的异常的每个代码路径`noexcept`函数。 如果异常未退出标记的函数的外部范围`noexcept`， [std:: terminate](../standard-library/exception-functions.md#terminate)立即，调用和将调用的任何范围内对象的析构函数不能保证。 使用`noexcept`而不是动态异常说明符`throw()`，该标准中现已弃用。 我们建议你应用`noexcept`向永远不会允许异常传播到调用堆栈的任何函数。 声明函数时`noexcept`，它使得编译器能够在多种不同的上下文中生成更高效的代码。 有关详细信息，请参阅[异常规范](exception-specifications-throw-cpp.md)。   
  
## <a name="example"></a>示例  
可将复制其参数的模板函数声明为`noexcept`前提是要复制的对象是普通的旧数据类型 (POD)。 此类函数可以如下声明：  
  
```cpp  
#include <type_traits>  
  
template <typename T>  
T copy_object(const T& obj) noexcept(std::is_pod<T>)  
{  
   // ...   
}  
```  
  
## <a name="see-also"></a>请参阅  
 [C++ 异常处理](cpp-exception-handling.md)[异常规范 （throw，noexcept）](exception-specifications-throw-cpp.md)