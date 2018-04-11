---
title: "函数模板 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- function templates
- templates, function
- function templates, about function templates
ms.assetid: 59b56a4b-0689-4161-9c07-25021562e2a7
caps.latest.revision: "6"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 721e466e5d7e77592e66aa3ebacb3ad59eb89bb5
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="function-templates"></a>函数模板
类模板可定义一系列相关类，这些类基于在实例化时传递到类的类型参数。 函数模板类似于类模板，但定义的是一系列函数。 利用函数模板，你可以指定基于相同代码但作用于不同类型或类的函数集。 以下函数模板交换两个项：  
  
```cpp
// function_templates1.cpp  
template< class T > void MySwap( T& a, T& b ) {  
   T c(a);   
   a = b;   
   b = c;  
}  
int main() {  
}  
```  
  
 此代码定义交换参数的值的一系列函数。 此模板中，你可以从生成将交换的函数**int**和**长**类型以及用户定义的类型。 如果正确定义了类的复制构造函数和赋值运算符，`MySwap` 甚至会交换类。  
  
 此外，函数模板将阻止您交换不同类型的对象，因为在编译时编译器知道 `a` 和 `b` 参数的类型。  
  
 尽管非模板化函数可以使用 void 指针运行此函数，但模板版本是 typesafe。 请考虑以下调用：  
  
```cpp
int j = 10;  
int k = 18;  
CString Hello = "Hello, Windows!";  
MySwap( j, k );          //OK  
MySwap( j, Hello );      //error  
```  
  
 第二个 `MySwap` 调用触发了编译时错误，因为编译器无法生成具有不同类型的参数的 `MySwap` 函数。 如果使用了 void 指针，两个函数调用都将正确编译，但函数在运行时无法正常工作。  
  
 允许显式指定函数模板的模板参数。 例如:  
  
```cpp
// function_templates2.cpp  
template<class T> void f(T) {}  
int main(int j) {  
   f<char>(j);   // Generate the specialization f(char).  
   // If not explicitly specified, f(int) would be deduced.  
}  
```  
  
 当显式指定模板参数时，将对函数参数执行常规隐式转换以将其转换为对应的函数模板参数的类型。 在上面的示例中，编译器会将转换`char j`类型`int`。  
  
## <a name="see-also"></a>请参阅  
 [模板](../cpp/templates-cpp.md)   
 [函数模板实例化](../cpp/function-template-instantiation.md)   
 [显式实例化](../cpp/explicit-instantiation.md)   
 [函数模板的显式专用化](../cpp/explicit-specialization-of-function-templates.md)
