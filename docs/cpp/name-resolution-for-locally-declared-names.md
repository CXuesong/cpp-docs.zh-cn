---
title: "本地声明名称的名称解析 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
ms.assetid: 743b88f3-de11-48f4-ae83-931449ea3886
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 92037e6194499372061c8c1e2ded82af86612f95
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="name-resolution-for-locally-declared-names"></a>本地声明名称的名称解析

通过和不通过模板参数都可引用模板名称本身。 在类模板的范围内，名称本身引用模板。 在模板专用化或部分专用化的范围中，名称单独引用专用化或部分专用化。 也可以通过适当的模板参数引用模板的其他专用化或部分专用化。  
  
## <a name="example"></a>示例

 以下代码演示在专用化或部分专用化的范围内以不同的方式解释类模板的名称 A。  
  
```cpp
// template_name_resolution3.cpp  
// compile with: /c  
template <class T> class A {  
   A* a1;   // A refers to A<T>  
   A<int>* a2;  // A<int> refers to a specialization of A.  
   A<T*>* a3;   // A<T*> refers to the partial specialization A<T*>.  
};  
  
template <class T> class A<T*> {  
   A* a4; // A refers to A<T*>.  
};  
  
template<> class A<int> {  
   A* a5; // A refers to A<int>.  
};  
```  
  
## <a name="example"></a>示例

 在模板参数与另一个对象之间出现名称冲突的情况下，模板参数可隐藏也可不隐藏。 下列规则将帮助确定优先顺序。  
  
 模板参数位于从其首先出现的位置开始一直到类或函数末尾的范围中。 如果名称再次出现在模板参数列表或基类列表中，则它将引用同一类型。 在标准 C++ 中，无法在同一范围中声明与模板参数相同的其他名称。 Microsoft 扩展允许在模板范围内重新定义模板参数。 以下示例演示在类模板的基规范中使用模板参数。  
  
```cpp
// template_name_resolution4.cpp  
// compile with: /EHsc  
template <class T>  
class Base1 {};  
  
template <class T>  
class Derived1 : Base1<T> {};  
  
int main() {  
   // Derived1<int> d;  
}  
```  
  
## <a name="example"></a>示例

 当在类模板外定义模板的成员函数时，可以使用不同的模板参数名称。 如果模板成员函数定义与声明对模板参数使用了不同的名称，并且定义中使用的名称与声明的其他成员冲突，则模板声明中的成员优先。  
  
```cpp
// template_name_resolution5.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
template <class T> class C {  
public:  
   struct Z {  
      Z() { cout << "Z::Z()" << endl; }  
   };  
   void f();  
};  
  
template <class Z>  
void C<Z>::f() {  
   // Z refers to the struct Z, not to the template arg;  
   // Therefore, the constructor for struct Z will be called.  
   Z z;  
}  
  
int main() {  
   C<int> c;  
   c.f();  
}  
```  
  
```Output  
Z::Z()  
```  
  
## <a name="example"></a>示例

 在声明模板的命名空间外定义模板函数或成员函数时，模板参数将优先于命名空间中其他成员的名称。  
  
```cpp
// template_name_resolution6.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
namespace NS {  
   void g() { cout << "NS::g" << endl; }  
  
   template <class T> struct C {  
      void f();  
      void g() { cout << "C<T>::g" << endl; }  
   };  
};  
  
template <class T>  
void NS::C<T>::f() {  
   g(); // C<T>::g, not NS::g  
};  
  
int main() {  
   NS::C<int> c;  
   c.f();  
}  
```  
  
```Output  
C<T>::g  
```  
  
## <a name="example"></a>示例

 在位于模板类声明之外的定义中，如果模板类具有不依赖于模板参数的基类，并且该基类或其成员之一与模板参数的名称相同，则该基类或成员名称将隐藏模板参数。  
  
```cpp
// template_name_resolution7.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
struct B {  
   int i;  
   void print() { cout << "Base" << endl; }  
};  
  
template <class T, int i> struct C : public B {  
   void f();  
};  
  
template <class B, int i>  
void C<B, i>::f() {  
   B b;   // Base class b, not template argument.  
   b.print();  
   i = 1; // Set base class's i to 1.  
}  
  
int main() {  
   C<int, 1> c;  
   c.f();  
   cout << c.i << endl;  
}  
```  
  
```Output  
Base  
1  
```  
  
## <a name="see-also"></a>请参阅

 [名称解析](../cpp/templates-and-name-resolution.md)
