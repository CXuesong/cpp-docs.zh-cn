---
title: "函数模板调用的重载解析 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords: function templates overload resolution
ms.assetid: a2918748-2cbb-4fc6-a176-e256f120bee4
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 64bc9371fcddad5f76f1474832a8d69188b60583
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="overload-resolution-of-function-template-calls"></a>函数模板调用的重载解析
函数模板可以重载具有相同名称的非模板函数。 在此方案中，首先通过使用模板参数推理来解析函数调用，以便使用唯一的专用化来实例化函数模板。 如果模板参数推理失败，则考虑其他函数重载来解析调用。 这些其他重载（也称为候选集）包括非模板函数和其他实例化的函数模板。 如果模板参数推理成功，则遵循重载决策的规则，将生成的函数与其他函数进行比较以确定最佳匹配。 有关详细信息，请参阅[函数重载](function-overloading.md)。  
  
## <a name="example"></a>示例

 如果非模板函数是模板函数的很好的匹配，则选择非模板函数（除非已显式指定模板参数），如以下示例中的 `f(1, 1)` 调用。  
  
```cpp
// template_name_resolution9.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
void f(int, int) { cout << "f(int, int)" << endl; }  
void f(char, char) { cout << "f(char, char)" << endl; }  
  
template <class T1, class T2>  
void f(T1, T2)  
{  
   cout << "void f(T1, T2)" << endl;  
};  
  
int main()  
{  
   f(1, 1);   // Equally good match; choose the nontemplate function.  
   f('a', 1); // Chooses the template function.  
   f<int, int>(2, 2);  // Template arguments explicitly specified.  
}  
```  
  
```Output  
f(int, int)  
void f(T1, T2)  
void f(T1, T2)  
```  
  
## <a name="example"></a>示例

 下一个示例阐释了一点，即如果非模板函数需要转换，则完全匹配的模板函数是首选的。  
  
```cpp
// template_name_resolution10.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
void f(int, int) { cout << "f(int, int)" << endl; }  
  
template <class T1, class T2>  
void f(T1, T2)  
{  
   cout << "void f(T1, T2)" << endl;  
};  
  
int main()  
{  
   long l = 0;  
   int i = 0;  
   // Call the template function f(long, int) because f(int, int)  
   // would require a conversion from long to int.  
   f(l, i);  
}  
```  
  
```Output  
void f(T1, T2)  
```  
  
## <a name="see-also"></a>请参阅

 [名称解析](../cpp/templates-and-name-resolution.md)   
 [typename](../cpp/typename.md)   
 
