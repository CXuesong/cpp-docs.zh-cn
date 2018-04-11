---
title: "using 声明 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- using declaration
- declaring namespaces, unqualified names in namespaces
- declarations [C++], using-declaration
- namespaces [C++], unqualified names in
- using keyword [C++]
- declarations [C++], namespaces
ms.assetid: 4184e2b1-3adc-408e-b5f3-0b3f8b554723
caps.latest.revision: "12"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: c6bf39dfdb4f59bcf54ce1ddd5174f1e3a55e3a0
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="using-declaration"></a>using 声明
使用声明将名称引入在其中的声明性区域 using 声明出现。  
  
## <a name="syntax"></a>语法  
  
```  
using [typename] nested-name-specifier unqualified-id ;  
using declarator-list ;  
```  
  
### <a name="parameters"></a>参数
  
*嵌套名称说明符*  
    终止通过范围解析运算符的命名空间、 类或枚举名称和范围解析运算符 （:），一个序列。 单个作用域解析运算符可能用于与全局命名空间中引入一个名称。 关键字`typename`是可选的可能用于解析依赖名称从基类引入到类模板时。  
  
*非限定 id*  
    非限定的 id 的表达式，这可能是标识符、 重载的运算符名称、 用户定义的文本运算符或转换函数名称、 类析构函数名称或模板名称和参数列表。  
  
*声明符列表*  
    逗号分隔的列表 [`typename`]*嵌套名称说明符**非限定 id*声明符，（可选） 跟省略号。
    
## <a name="remarks"></a>备注  
一个 using 声明引入作为实体的同义词的非限定的名称声明在其他位置。 它允许从特定的命名空间，而无需显式限定它显示在其中声明区域中使用单个名称。 这是与此相反[using 指令](../cpp/namespaces-cpp.md#using_directives)，这样，*所有*而无需限定使用的命名空间中的名称。 `using`关键字还用于[键入别名](../cpp/aliases-and-typedefs-cpp.md)。  
  
## <a name="example"></a>示例  
 一个声明可以使用类定义中。  
  
```cpp  
// using_declaration1.cpp  
#include <stdio.h>  
class B {  
public:  
   void f(char) {  
      printf_s("In B::f()\n");  
   }  
  
   void g(char) {  
      printf_s("In B::g()\n");  
   }  
};  
  
class D : B {  
public:  
   using B::f;    // B::f(char) is now visible as D::f(char)  
   using B::g;    // B::g(char) is now visible as D::g(char)  
   void f(int) {  
      printf_s("In D::f()\n");  
      f('c');     // Invokes B::f(char) instead of recursing  
   }  
  
   void g(int) {  
      printf_s("In D::g()\n");  
      g('c');     // Invokes B::g(char) instead of recursing  
   }  
};  
  
int main() {  
   D myD;  
   myD.f(1);  
   myD.g('a');  
}  
```  
  
```Output  
In D::f()  
In B::f()  
In B::g()  
```  
  
## <a name="example"></a>示例  
当用于声明一个成员，using 声明必须指向基类的成员。  
  
```cpp  
// using_declaration2.cpp  
#include <stdio.h>  
  
class B {  
public:  
   void f(char) {  
      printf_s("In B::f()\n");  
   }  
  
   void g(char) {  
      printf_s("In B::g()\n");  
   }  
};  
  
class C {  
public:  
   int g();  
};  
  
class D2 : public B {  
public:  
   using B::f;   // ok: B is a base of D2  
   // using C::g;   // error: C isn't a base of D2  
};  
  
int main() {  
   D2 MyD2;  
   MyD2.f('a');  
}  
```  
  
```Output  
In B::f()  
```  
  
## <a name="example"></a>示例  
使用 using 声明成员声明可以通过使用显式限定引用。 `::`前缀引用的全局命名空间。  
  
```cpp  
// using_declaration3.cpp  
#include <stdio.h>  
  
void f() {  
   printf_s("In f\n");  
}  
  
namespace A {  
   void g() {  
      printf_s("In A::g\n");  
   }  
}  
  
namespace X {  
   using ::f;   // global f is also visible as X::f  
   using A::g;   // A's g is now visible as X::g 
}  
  
void h() {  
   printf_s("In h\n");  
   X::f();   // calls ::f  
   X::g();   // calls A::g  
}  
  
int main() {  
   h();  
}  
```  
  
```Output  
In h  
In f  
In A::g  
```  
  
## <a name="example"></a>示例  
使用时进行声明，由声明所创建同义词仅指在使用有效的定义声明。 定义在使用后添加到命名空间声明不是有效的同义词。  
  
通过定义的名称`using`声明是其原始名称的别名。 它不影响类型、 链接或原始声明的其他特性。  
  
```cpp  
// post_declaration_namespace_additions.cpp  
// compile with: /c  
namespace A {  
   void f(int) {}  
}  
  
using A::f;   // f is a synonym for A::f(int) only  
  
namespace A {  
   void f(char) {}  
}  
  
void f() {  
   f('a');   // refers to A::f(int), even though A::f(char) exists  
}  
  
void b() {  
   using A::f;   // refers to A::f(int) AND A::f(char)  
   f('a');   // calls A::f(char);  
}  
```  
  
## <a name="example"></a>示例  
对于命名空间，如果一组本地声明中的函数和单个名称提供声明性区域中，它们必须都引用同一个实体，或者它们必须都引用函数使用声明。  
  
```cpp  
// functions_in_namespaces1.cpp  
// C2874 expected  
namespace B {  
    int i;  
    void f(int);  
    void f(double);  
}  
  
void g() {  
    int i;  
    using B::i;   // error: i declared twice  
    void f(char);  
    using B::f;   // ok: each f is a function  
}  
```  
  
 在上例中，`using B::i`语句会导致第二个`int i`中声明`g()`函数。 `using B::f`语句不与冲突`f(char)`正常，因为引入了函数名称`B::f`具有不同的参数类型。  
  
## <a name="example"></a>示例  
 本地函数声明不能将相同的名称和类型作为通过使用声明引入的函数。 例如:  
  
```cpp  
// functions_in_namespaces2.cpp  
// C2668 expected  
namespace B {  
    void f(int);  
    void f(double);  
}  
  
namespace C {  
    void f(int);  
    void f(double);  
    void f(char);  
}  
  
void h() {  
    using B::f;          // introduces B::f(int) and B::f(double)  
    using C::f;          // C::f(int), C::f(double), and C::f(char)  
    f('h');              // calls C::f(char)  
    f(1);                // C2668 ambiguous: B::f(int) or C::f(int)?  
    void f(int);         // C2883 conflicts with B::f(int) and C::f(int)  
}  
```  
  
## <a name="example"></a>示例  
 相对于继承，使用时声明引入一个名称从基类派生的类范围，派生的类重写虚拟成员函数中与基类中相同的名称和参数类型的成员函数。  
  
```cpp  
// using_declaration_inheritance1.cpp  
#include <stdio.h>  
struct B {  
   virtual void f(int) {  
      printf_s("In B::f(int)\n");  
   }  
  
   virtual void f(char) {  
      printf_s("In B::f(char)\n");  
   }  
  
   void g(int) {  
      printf_s("In B::g\n");  
   }  
  
   void h(int);  
};  
  
struct D : B {  
   using B::f;  
   void f(int) {   // ok: D::f(int) overrides B::f(int)  
      printf_s("In D::f(int)\n");  
   }  
  
   using B::g;  
   void g(char) {   // ok: there is no B::g(char)  
      printf_s("In D::g(char)\n");  
   }  
  
   using B::h;  
   void h(int) {}   // Note: D::h(int) hides non-virtual B::h(int)  
};  
  
void f(D* pd) {  
   pd->f(1);     // calls D::f(int)  
   pd->f('a');   // calls B::f(char)  
   pd->g(1);     // calls B::g(int)  
   pd->g('a');   // calls D::g(char)  
}  
  
int main() {  
   D * myd = new D();  
   f(myd);  
}  
```  
  
```Output  
In D::f(int)  
In B::f(char)  
In B::g  
In D::g(char)  
```  
  
## <a name="example"></a>示例  
使用所述的名称的所有实例声明必须是可访问。 具体而言，如果派生的类使用 using 声明，以访问基类的成员名称的成员必须可访问性。 如果名称是，一个重载的成员函数，则名为的所有函数必须都是可访问。  
  
可访问性成员的详细信息，请参阅[成员访问控制](../cpp/member-access-control-cpp.md)。  
  
```cpp  
// using_declaration_inheritance2.cpp  
// C2876 expected  
class A {  
private:  
   void f(char);  
public:  
   void f(int);  
protected:  
   void g();  
};  
  
class B : public A {  
   using A::f;   // C2876: A::f(char) is inaccessible  
public:  
   using A::g;   // B::g is a public synonym for A::g  
};  
```  
  
## <a name="see-also"></a>请参阅  
 [命名空间](../cpp/namespaces-cpp.md)   
 [关键字](../cpp/keywords-cpp.md)