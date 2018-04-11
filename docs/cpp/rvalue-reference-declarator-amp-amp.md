---
title: "右值引用声明符： &amp; &amp; |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: '&&'
dev_langs: C++
helpviewer_keywords: '&& rvalue reference declarator'
ms.assetid: eab0ce3a-c5a3-4992-aa70-6a8ab1f7491d
caps.latest.revision: "22"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: e35d0efc92e011cfb4d93746efd1b03ac94a0779
ms.sourcegitcommit: 54035dce0992ba5dce0323d67f86301f994ff3db
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="rvalue-reference-declarator-ampamp"></a>右值引用声明符：&amp;&amp;
保留对右值表达式的引用。  
  
## <a name="syntax"></a>语法  
  
```  
  
type-id && cast-expression  
```  
  
## <a name="remarks"></a>备注  
 利用右值引用，您可以将左值与右值区分开。 左值引用和右值引用在语法和语义上相似，但它们遵循的规则稍有不同。 有关左值和右值的详细信息，请参阅[Lvalues 和 Rvalues](../cpp/lvalues-and-rvalues-visual-cpp.md)。 有关左值引用的详细信息，请参阅[左值引用声明符： &](../cpp/lvalue-reference-declarator-amp.md)。  
  
 以下各节介绍右值引用如何支持的实现*移动语义*和*完美转发*。  
  
## <a name="move-semantics"></a>移动语义  
 右值引用支持的实现*移动语义*，这可以显著提高你的应用程序的性能。 利用移动语义，你可以编写将资源（如动态分配的内存）从一个对象转移到另一个对象的代码。 移动语义很有效，因为它使资源能够从无法在程序中的其他位置引用的临时对象转移。  
  
 若要实现移动语义，通常提供*移动构造函数，*和 （可选） 移动赋值运算符 (`operator=`)，向您的类。 其源是右值的复制和赋值操作随后会自动利用移动语义。 与默认复制构造函数不同，编译器不提供默认移动构造函数。 有关如何编写移动构造函数以及如何在你的应用程序中使用它的详细信息，请参阅[移动构造函数和移动赋值运算符 （C++）](../cpp/move-constructors-and-move-assignment-operators-cpp.md)。  
  
 您还可以重载普通函数和运算符以利用移动语义。 Visual C++ 2010年移动语义引入到 C++ 标准库。 例如，`string` 类实现了执行移动语义的操作。 请考虑以下串联几个字符串并输出结果的示例：  
  
```  
// string_concatenation.cpp  
// compile with: /EHsc  
#include <iostream>  
#include <string>  
using namespace std;  
  
int main()  
{  
   string s = string("h") + "e" + "ll" + "o";  
   cout << s << endl;  
}  
```  
  
 在 Visual C++ 2010 中之前, 每个调用到`operator+`分配并返回一个新的临时`string`对象 （右值）。 `operator+` 不能将一个字符串追加到另一个字符串，因为它不知道源字符串是左值还是右值。 如果两个源字符串都是左值，则它们可能会在程序中的其他位置引用，因此不能修改。 利用右值引用，可以将 `operator+` 修改为采用右值，而右值不能在程序中的其他位置引用。 因此，`operator+` 现在可将一个字符串追加到另一个字符串。 这可以显著减少 `string` 类必须执行的动态内存分配的数量。 有关详细信息`string`类，请参阅[basic_string 类](../standard-library/basic-string-class.md)。  
  
 当编译器无法使用返回值优化 (RVO) 或命名返回值优化 (NRVO) 时，移动语义也很有用。 在这些情况下，如果类型定义了移动构造函数，则编译器将调用该函数。 有关命名返回值优化的详细信息，请参阅[Visual C++ 2005年中名为返回值优化](http://go.microsoft.com/fwlink/p/?linkid=131571)。  
  
 若要更好地了解移动语义，请考虑将元素插入 `vector` 对象的示例。 如果超出 `vector` 对象的容量，则 `vector` 对象必须为其元素重新分配内存，然后将所有元素复制到其他内存位置，以便为插入的元素腾出空间。 当插入操作复制某个元素时，它将创建一个新元素，调用复制构造函数以将数据从上一个元素复制到新元素，然后销毁上一个元素。 利用移动语义，您可以直接移动对象而不必执行成本高昂的内存分配和复制操作。  
  
 若要在 `vector` 示例中利用移动语义，则可以编写将数据从一个对象移动到另一个对象的移动构造函数。  
  
 有关移动语义引入 Visual C++ 2010年中的 C++ 标准库的详细信息，请参阅[C++ 标准库](../standard-library/cpp-standard-library-reference.md)。  
  
## <a name="perfect-forwarding"></a>完美转发  
 完美转发可减少对重载函数的需求，并有助于避免转发问题。 *转发问题*编写采用引用作为其参数的泛型函数并传递时可能发生 (或*将转发*) 给另一个函数这些参数。 例如，如果泛型函数采用 `const T&` 类型的参数，则调用的函数无法修改该参数的值。 如果泛型函数采用 `T&` 类型的参数，则无法使用右值（如临时对象或整数文本）来调用该函数。  
  
 通常，若要解决此问题，则必须提供为其每个参数采用 `T&` 和 `const T&` 的重载版本的泛型函数。 因此，重载函数的数量将基于参数的数量呈指数方式增加。 利用右值引用，您可以编写一个版本的函数，该函数可接受任意参数并将其转发给另一个函数，就像已直接调用其他函数一样。  
  
 请考虑以下声明了四个类型 `W``X`、`Y` 和 `Z` 的示例。 每个类型的构造函数采用 `const` 和非 `const` 左值引用的不同组合作为其参数。  
  
```  
struct W  
{  
   W(int&, int&) {}  
};  
  
struct X  
{  
   X(const int&, int&) {}  
};  
  
struct Y  
{  
   Y(int&, const int&) {}  
};  
  
struct Z  
{  
   Z(const int&, const int&) {}  
};  
```  
  
 假定您要编写生成对象的泛型函数。 以下示例演示了一种编写此函数的方式：  
  
```  
template <typename T, typename A1, typename A2>  
T* factory(A1& a1, A2& a2)  
{  
   return new T(a1, a2);  
}  
```  
  
 以下示例演示了对 `factory` 函数的有效调用：  
  
```  
int a = 4, b = 5;  
W* pw = factory<W>(a, b);  
```  
  
 但是，以下示例不包含对 `factory` 函数的有效调用，因为 `factory` 采用可修改的左值引用作为其参数，但该函数是使用右值调用的：  
  
```  
Z* pz = factory<Z>(2, 2);  
```  
  
 通常，若要解决此问题，您必须为 `factory` 和 `A&` 的参数的每个组合创建一个重载版本的 `const A&` 函数。 利用右值引用，您可以编写一个版本的 `factory` 函数，如以下示例所示：  
  
```  
template <typename T, typename A1, typename A2>  
T* factory(A1&& a1, A2&& a2)  
{  
   return new T(std::forward<A1>(a1), std::forward<A2>(a2));  
}  
```  
  
 此示例使用右值引用作为 `factory` 函数的参数。 用途[std:: forward](../standard-library/utility-functions.md#forward)函数是模板类的构造函数将 factory 函数的参数转发。  
  
 以下示例演示了使用修改后的 `main` 函数创建 `factory`、`W`、`X` 和 `Y` 类的实例的 `Z` 函数。 修改后的 `factory` 函数会将其参数（左值和右值）转发给适当的类构造函数。  
  
```  
int main()  
{  
   int a = 4, b = 5;  
   W* pw = factory<W>(a, b);  
   X* px = factory<X>(2, b);  
   Y* py = factory<Y>(a, 2);  
   Z* pz = factory<Z>(2, 2);  
  
   delete pw;  
   delete px;  
   delete py;  
   delete pz;  
}  
```  
  
## <a name="additional-properties-of-rvalue-references"></a>右值引用的其他属性  
 **您可以重载采用左值引用和右值引用的函数。**  
  
 通过重载函数来采用 `const` 左值引用或右值引用，您可以编写能区分不可更改的对象（左值）和可修改的临时值（右值）的代码。 您可以将对象传递给采用右值引用的函数，除非该对象标记为 `const`。 以下示例演示了函数 `f`，该函数将被重载以采用左值引用和右值引用。 `main` 函数同时使用左值和右值来调用 `f`。  
  
```  
// reference-overload.cpp  
// Compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
// A class that contains a memory resource.  
class MemoryBlock  
{  
   // TODO: Add resources for the class here.  
};  
  
void f(const MemoryBlock&)  
{  
   cout << "In f(const MemoryBlock&). This version cannot modify the parameter." << endl;  
}  
  
void f(MemoryBlock&&)  
{  
   cout << "In f(MemoryBlock&&). This version can modify the parameter." << endl;  
}  
  
int main()  
{  
   MemoryBlock block;  
   f(block);  
   f(MemoryBlock());  
}  
```  
  
 该示例产生下面的输出：  
  
```  
In f(const MemoryBlock&). This version cannot modify the parameter.  
In f(MemoryBlock&&). This version can modify the parameter.  
```  
  
 在此示例中，对 `f` 的第一个调用将局部变量（左值）作为其参数传递。 对 `f` 的第二个调用将临时对象作为其参数传递。 由于无法在程序中的其他位置引用临时对象，因此调用将绑定到采用右值引用的重载版本的 `f`，该版本可以随意修改对象。  
  
 **编译器将命名的右值引用视为左值和未命名的右值引用视为右值。**  
  
 当编写采用右值引用作为其参数的函数时，该参数被视为函数体中的左值。 编译器将已命名的右值引用视为左值是因为已命名的对象可以由程序中的多个部分引用；而允许程序的多个部分在该对象中修改或移除资源是很危险的。 例如，如果程序的多个部分尝试从同一对象转移资源，则只有第一个部分能成功转移该资源。  
  
 以下示例演示了函数 `g`，该函数将被重载以采用左值引用和右值引用。 函数 `f` 采用右值引用作为其参数（已命名的右值引用），并返回右值引用（未命名的右值引用）。 在从 `g` 到 `f` 的调用中，重载决策选择采用左值引用的 `g` 版本，因为 `f` 的主体将其参数视为左值。 在从 `g` 到 `main` 的调用中，重载决策选择采用右值引用的 `g` 版本，因为 `f` 返回右值引用。  
  
```  
// named-reference.cpp  
// Compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
// A class that contains a memory resource.  
class MemoryBlock  
{  
   // TODO: Add resources for the class here.  
};  
  
void g(const MemoryBlock&)   
{  
   cout << "In g(const MemoryBlock&)." << endl;  
}  
  
void g(MemoryBlock&&)   
{  
   cout << "In g(MemoryBlock&&)." << endl;  
}  
  
MemoryBlock&& f(MemoryBlock&& block)  
{  
   g(block);  
   return block;  
}  
  
int main()  
{  
   g(f(MemoryBlock()));  
}  
```  
  
 该示例产生下面的输出：  
  
```  
In g(const MemoryBlock&).  
In g(MemoryBlock&&).  
```  
  
 在此示例中，`main` 函数将右值传递给 `f`。 `f` 的主体将其命名参数视为左值。 从 `f` 到 `g` 的调用会将参数绑定到左值引用（第一个重载版本的 `g`）。  
  
-   **您可以强制转换为右值引用左值。**  
  
 C++ 标准库[std:: move](../standard-library/utility-functions.md#move)函数使您能够将对象转换为对该对象的右值引用。 或者，您也可以使用 `static_cast` 关键字将左值强制转换为右值引用，如以下示例所示：  
  
```  
// cast-reference.cpp  
// Compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
// A class that contains a memory resource.  
class MemoryBlock  
{  
   // TODO: Add resources for the class here.  
};  
  
void g(const MemoryBlock&)   
{  
   cout << "In g(const MemoryBlock&)." << endl;  
}  
  
void g(MemoryBlock&&)   
{  
   cout << "In g(MemoryBlock&&)." << endl;  
}  
  
int main()  
{  
   MemoryBlock block;  
   g(block);  
   g(static_cast<MemoryBlock&&>(block));  
}  
```  
  
 该示例产生下面的输出：  
  
```  
In g(const MemoryBlock&).  
In g(MemoryBlock&&).  
```  
  
 **函数模板推导出其模板参数类型，然后使用引用折叠规则。**  
  
 通常需要编写将传递一个函数模板 (或*将转发*) 给另一个函数其参数。 了解模板类型推导如何对采用右值引用的函数模板起作用很重要。  
  
 如果函数参数是右值，则编译器将参数推导为右值引用。 例如，如果将对 `X` 类型的对象的右值引用传递给采用类型 `T&&` 作为其参数的模板函数，则模板参数推导会将 `T` 推导为 `X`。 因此，参数具有类型 `X&&`。 如果函数参数是左值或 `const` 右值，则编译器会将其类型推导为右值引用或该类型的 `const` 左值引用。  
  
 以下示例声明了一个结构模板，然后针对不同引用类型对其进行了专用化。 `print_type_and_value` 函数采用右值引用作为其参数，并将它转发给适当专用版本的 `S::print` 方法。 `main` 函数演示了调用 `S::print` 方法的各种方式。  
  
```  
// template-type-deduction.cpp  
// Compile with: /EHsc  
#include <iostream>  
#include <string>  
using namespace std;  
  
template<typename T> struct S;  
  
// The following structures specialize S by   
// lvalue reference (T&), const lvalue reference (const T&),   
// rvalue reference (T&&), and const rvalue reference (const T&&).  
// Each structure provides a print method that prints the type of   
// the structure and its parameter.  
  
template<typename T> struct S<T&> {  
   static void print(T& t)  
   {  
      cout << "print<T&>: " << t << endl;  
   }  
};  
  
template<typename T> struct S<const T&> {  
   static void print(const T& t)  
   {  
      cout << "print<const T&>: " << t << endl;  
   }  
};  
  
template<typename T> struct S<T&&> {  
   static void print(T&& t)  
   {  
      cout << "print<T&&>: " << t << endl;  
   }  
};  
  
template<typename T> struct S<const T&&> {  
   static void print(const T&& t)  
   {  
      cout << "print<const T&&>: " << t << endl;  
   }  
};  
  
// This function forwards its parameter to a specialized  
// version of the S type.  
template <typename T> void print_type_and_value(T&& t)   
{  
   S<T&&>::print(std::forward<T>(t));  
}  
  
// This function returns the constant string "fourth".  
const string fourth() { return string("fourth"); }  
  
int main()  
{  
   // The following call resolves to:  
   // print_type_and_value<string&>(string& && t)  
   // Which collapses to:  
   // print_type_and_value<string&>(string& t)  
   string s1("first");  
   print_type_and_value(s1);   
  
   // The following call resolves to:  
   // print_type_and_value<const string&>(const string& && t)  
   // Which collapses to:  
   // print_type_and_value<const string&>(const string& t)  
   const string s2("second");  
   print_type_and_value(s2);  
  
   // The following call resolves to:  
   // print_type_and_value<string&&>(string&& t)  
   print_type_and_value(string("third"));  
  
   // The following call resolves to:  
   // print_type_and_value<const string&&>(const string&& t)  
   print_type_and_value(fourth());  
}  
```  
  
 该示例产生下面的输出：  
  
```  
print<T&>: first  
print<const T&>: second  
print<T&&>: third  
print<const T&&>: fourth  
```  
  
 为了解析对 `print_type_and_value` 函数的每个调用，编译器首先会执行模板参数推导。 然后，编译器在用推导出的模板参数替换参数类型时应用引用折叠规则。 例如，将局部变量 `s1` 传递给 `print_type_and_value` 函数将导致编译器生成以下函数签名：  
  
```  
print_type_and_value<string&>(string& && t)  
```  
  
 编译器使用引用折叠规则将签名缩短为以下形式：  
  
```  
print_type_and_value<string&>(string& t)  
```  
  
 此版本的 `print_type_and_value` 函数随后将其参数转发到正确的专用版本的 `S::print` 方法。  
  
 下表汇总了模板参数类型推导的引用折叠规则：  
  
|||  
|-|-|  
|展开类型|折叠类型|  
|`T& &`|`T&`|  
|`T& &&`|`T&`|  
|`T&& &`|`T&`|  
|`T&& &&`|`T&&`|  
  
 模板参数推导是实现完美转发的重要因素。 本主题前面部分的“完美转发”一节更详细地介绍了完美转发。  
  
## <a name="summary"></a>摘要  
 右值引用可将左值和右值区分开。 它们可以帮助您消除不必要的内存分配和复制操作需求，从而提高应用程序的性能。 它们还使您能够编写一个版本的函数，该函数可接受任意参数并将其转发给另一个函数，就像已直接调用其他函数一样。  
  
## <a name="see-also"></a>请参阅  
 [使用一元运算符的表达式](../cpp/expressions-with-unary-operators.md)   
 [左值引用声明符： &](../cpp/lvalue-reference-declarator-amp.md)   
 [Lvalues 和 Rvalues](../cpp/lvalues-and-rvalues-visual-cpp.md)   
 [移动构造函数和移动赋值运算符 (C++)](../cpp/move-constructors-and-move-assignment-operators-cpp.md)   
 [C++ 标准库](../standard-library/cpp-standard-library-reference.md)   
