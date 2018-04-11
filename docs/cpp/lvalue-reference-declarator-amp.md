---
title: "左值引用声明符： &amp; |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: '&'
dev_langs: C++
helpviewer_keywords:
- reference operator
- '& operator [C++], reference operator'
ms.assetid: edf0513d-3dcc-4663-b276-1269795dda51
caps.latest.revision: "14"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 5bea07ed3139240279848d94184564ec821a8cd9
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="lvalue-reference-declarator-amp"></a>左值引用声明符：&amp;
保留对象的地址，但行为方式在语法上与对象相似。  
  
## <a name="syntax"></a>语法  
  
```  
  
type-id & cast-expression  
```  
  
## <a name="remarks"></a>备注  
 您可以将左值引用视为对象的另一名称。 左值引用声明由说明符的可选列表后跟一个引用声明符组成。 引用必须初始化且无法更改。  
  
 地址可转换为给定指针类型的任何对象也可转换为相似的引用类型。 例如，地址可转换为类型 `char *` 的任何对象也可转换为类型 `char &`。  
  
 不要混淆引用声明与使用[address-of 运算符](../cpp/address-of-operator-amp.md)。 当`&`*标识符*前面是一种类型，如`int`或`char`，*标识符*声明为对类型的引用。 当`&`*标识符*前面没有一种类型，通过使用情况是，address-of 运算符。  
  
## <a name="example"></a>示例  
 以下示例通过声明 `Person` 对象和对该对象的引用演示了引用声明符。 由于 `rFriend` 是对 `myFriend` 的引用，因此更新任一变量都将更改同一对象。  
  
```  
// reference_declarator.cpp  
// compile with: /EHsc  
// Demonstrates the reference declarator.  
#include <iostream>  
using namespace std;  
  
struct Person  
{  
    char* Name;  
    short Age;  
};  
  
int main()  
{  
   // Declare a Person object.  
   Person myFriend;  
  
   // Declare a reference to the Person object.  
   Person& rFriend = myFriend;  
  
   // Set the fields of the Person object.  
   // Updating either variable changes the same object.  
   myFriend.Name = "Bill";  
   rFriend.Age = 40;  
  
   // Print the fields of the Person object to the console.  
   cout << rFriend.Name << " is " << myFriend.Age << endl;  
}  
```  
  
```Output  
Bill is 40  
```  
  
## <a name="see-also"></a>请参阅  
 [引用](../cpp/references-cpp.md)   
 [引用类型函数参数](../cpp/reference-type-function-arguments.md)   
 [引用类型函数返回](../cpp/reference-type-function-returns.md)   
 [对指针的引用](../cpp/references-to-pointers.md)