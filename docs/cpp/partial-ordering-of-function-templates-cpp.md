---
title: "函数模板 （C++） 的部分排序 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords: partial ordering of function templates
ms.assetid: 0c17347d-0e80-47ad-b5ac-046462d9dc73
caps.latest.revision: "9"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: cddc0f1680a3354276a2135dd28c31a2037a8202
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="partial-ordering-of-function-templates-c"></a>函数模板的部分排序 (C++)

有多个与函数调用的参数列表匹配的函数模板可用。 C++ 定义了函数模板的部分排序以指定应调用的函数。 由于有一些模板可能会视为专用化程度相同，因此排序只是部分的。

编译器从可能的匹配项中选择可用的专用化程度最高的模板函数。 例如，如果函数模板采用类型__T__，和另一个函数模板采用__T\*__ 不可用， __T\*__ 版本称为要更多专用化并且优先于泛型__T__当参数是指针类型，即使都将是允许的匹配项的版本。

通过以下过程可确定一个函数模板候选项是否更加专用化：

1. 考虑两个函数模板：T1 和 T2。

2. 将 T1 中的参数替换为假想的唯一类型 X。

3. 当 T1 中存在参数列表时，查看 T2 是否为该参数列表的有效模板。 忽略任何隐式转换。

4. 对 T1 和 T2 执行相反的过程。

5. 如果一个模板是另一个模板的有效模板参数列表，但反之不成立，则认为第一个模板的专用化程度低于第二个模板。 如果使用的上一个步骤窗体有效参数对于每个其他两个模板，则它们被视为同等专用化，并不明确的调用导致当你尝试使用它们。

6. 使用以下规则：

     1. 针对特定类型的模板的专用化程度高于采用泛型类型参数的模板。

     2. 模板仅采用__T\*__ 化程度高于仅采用一个__T__，因为一个假想键入__X\*__ 是有效的参数__T__模板参数，但__X__不是有效参数__T\*__ 模板参数。

     3. __const T__化程度高于__T__，这是因为__const X__是的有效参数__T__模板参数，但__X__不是有效参数__const T__模板参数。

     4. __const T\*__ 化程度高于__T\*__，这是因为__const X\*__ 是的有效参数__T\*__模板参数，但__X\*__ 不是有效参数__const T\*__ 模板参数。

## <a name="example"></a>示例

下面的示例的作用是作为标准中的指定：

```cpp
// partial_ordering_of_function_templates.cpp
// compile with: /EHsc
#include <iostream>

extern "C" int printf(const char*,...);
template <class T> void f(T) {
   printf_s("Less specialized function called\n");
}

template <class T> void f(T*) {
   printf_s("More specialized function called\n");
}

template <class T> void f(const T*) {
   printf_s("Even more specialized function for const T*\n");
}

int main() {
   int i =0;
   const int j = 0;
   int *pi = &i;
   const int *cpi = &j;

   f(i);   // Calls less specialized function.
   f(pi);  // Calls more specialized function.
   f(cpi); // Calls even more specialized function.
   // Without partial ordering, these calls would be ambiguous.
}
```  
  
### <a name="output"></a>输出  
  
```  
Less specialized function called  
More specialized function called  
Even more specialized function for const T*  
```  
  
## <a name="see-also"></a>请参阅

[函数模板](../cpp/function-templates.md)
