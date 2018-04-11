---
title: "decltype （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: decltype_cpp
dev_langs: C++
helpviewer_keywords:
- operators [C++], decltype
- decltype operator
- operators [C++], type of an expression
- operators [C++], deduce expression type
ms.assetid: 6dcf8888-8196-4f13-af50-51e3797255d4
caps.latest.revision: "14"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: ee3c83512929e4592a5ee75b954bc6c19f52f448
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="decltype--c"></a>decltype （C++）
`decltype` 类型说明符生成指定表达式的类型。 `decltype`连同类型说明符， [auto 关键字](../cpp/auto-cpp.md)，主要对编写模板库的开发人员很有用。 使用 `auto` 和 `decltype` 声明其返回类型取决于其模板参数类型的模板函数。 或者，使用 `auto` 和 `decltype` 声明包装对其他函数的调用，然后返回包装函数的返回类型的模板函数。  
  
## <a name="syntax"></a>语法  
  
```  
decltype( expression )  
```  
  
### <a name="parameters"></a>参数  
  
|参数|描述|  
|---------------|-----------------|  
|`expression`|一个表达式。 有关详细信息，请参阅[表达式](../cpp/expressions-cpp.md)。|  
  
## <a name="return-value"></a>返回值  
 `expression` 参数的类型。  
  
## <a name="remarks"></a>备注  
 `decltype` 类型说明符在 Visual C++ 2010 或更高版本中受支持，并可与本机或托管代码一起使用。 Visual Studio 2015 及更高版本支持 `decltype(auto)` (C++14)。  
  
 编译器使用下列规则来确定 `expression` 参数的类型。  
  
-   如果`expression`参数是标识符或[类成员访问](../cpp/member-access-operators-dot-and.md)，`decltype(expression)`是命名的实体的类型`expression`。 如果不存在此类实体或 `expression` 参数命名一组重载函数，则编译器将生成错误消息。  
  
-   如果`expression`参数是对一个函数或重载的运算符函数的调用`decltype(expression)`是函数的返回类型。 将忽略重载运算符两边的括号。  
  
-   如果`expression`参数是[右值](../cpp/lvalues-and-rvalues-visual-cpp.md)，`decltype(expression)`是一种`expression`。 如果`expression`参数是[左值](../cpp/lvalues-and-rvalues-visual-cpp.md)，`decltype(expression)`是[左值引用](../cpp/lvalue-reference-declarator-amp.md)为的类型`expression`。  
  
 下面的代码示例演示 `decltype` 类型标识符的一些用途。 首先，假定已编码下列语句。  
  
```cpp  
int var;  
const int&& fx();   
struct A { double x; }  
const A* a = new A();  
```  
  
 接下来，请检查由下表中四个 `decltype` 语句返回的类型。  
  
|语句|类型|说明|  
|---------------|----------|-----------|  
|`decltype(fx());`|`const int&&`|[右值引用](../cpp/rvalue-reference-declarator-amp-amp.md)到`const int`。|  
|`decltype(var);`|`int`|变量 `var` 的类型。|  
|`decltype(a->x);`|`double`|成员访问的类型。|  
|`decltype((a->x));`|`const double&`|内部括号导致语句作为表达式而不是成员访问计算。 由于 `a` 声明为 `const` 指针，因此类型是对 `const double` 的引用。|  
  
## <a name="decltype-and-auto"></a>Decltype 和 Auto  
 在 C++ 14 中，你可以使用`decltype(auto)`不带尾随返回类型来声明其返回类型的模板函数取决于其模板参数的类型。  
  
 在 C++11 中，可以结合使用尾随返回类型上的 `decltype` 类型说明符和 `auto` 关键字来声明其返回类型依赖于其模板参数类型的模板函数。 例如，考虑下面的代码示例，其中模板函数的返回类型取决于模板参数类型。 在代码示例中，*未知*占位符指示无法指定返回类型。  
  
```cpp  
template<typename T, typename U>  
UNKNOWN func(T&& t, U&& u){ return t + u; };   
```  
  
 `decltype` 类型说明符的引入使开发人员能够获取模板函数返回的表达式的类型。 使用*替代函数声明语法*更高版本，显示`auto`关键字，与`decltype`类型说明符来声明*后期指定*返回类型。 后指定返回类型是在对声明进行编译而不是编码时确定的。  
  
 以下原型阐述一个替代函数声明的语法。 请注意，`const`和`volatile`限定符，和`throw`[异常规范](../cpp/exception-specifications-throw-cpp.md)都是可选的。 *Function_body*占位符表示指定函数的行为的复合语句。 作为最佳编码做法，*表达式*中的占位符`decltype`语句应与指定的表达式匹配`return`语句，如果有的话，在*function_body*。  
  
 **自动** *function_name* **(** *参数*<sub>选择</sub> **)** **const**<sub>选择</sub>**易失性**<sub>选择</sub>  **->**  **decltype (***表达式* **)** **引发**<sub>选择</sub> **{** *function_body***};**  
  
 在下面的代码示例中，`myFunc` 模板函数的后指定返回类型取决于 `t` 和 `u` 模板参数的类型。 作为最佳编码做法，此代码示例还使用右值引用和`forward`函数模板来支持*完美转发*。 有关详细信息，请参阅[右值引用声明符：&&](../cpp/rvalue-reference-declarator-amp-amp.md)。  
  
```cpp  
//C++11  
 template<typename T, typename U>  
auto myFunc(T&& t, U&& u) -> decltype (forward<T>(t) + forward<U>(u))   
        { return forward<T>(t) + forward<U>(u); };  
  
//C++14  
template<typename T, typename U>  
decltype(auto) myFunc(T&& t, U&& u)   
        { return forward<T>(t) + forward<U>(u); };  
  
```  
  
## <a name="decltype-and-forwarding-functions-c11"></a>Decltype 和转发函数 (C++11)  
 转发函数包装对其他函数的调用。 请考虑将其参数或包含这些参数的表达式的结果转发到其他函数的函数模板。 此外，转发函数返回调用其他函数的结果。 在此方案中，转发函数的返回类型应与包装函数的返回类型相同。  
  
 在此方案中，没有 `decltype` 类型说明符，您无法编写适当的类型表达式。 `decltype` 类型说明符将启用泛型转发函数，因为该说明符不会丢失有关函数是否返回引用类型的必需信息。 有关转发函数的代码示例，请参阅上面的 `myFunc` 模板函数示例。  
  
## <a name="example"></a>示例  
 下面的代码示例声明模板函数 `Plus()` 的后指定返回类型。 `Plus` 函数将使用 `operator+` 重载处理其两个操作数。 因此，对 `Plus` 函数的加运算符 (+) 和返回类型的解释取决于函数参数的类型。  
  
```cpp  
// decltype_1.cpp  
// compile with: cl /EHsc decltype_1.cpp  
  
#include <iostream>  
#include <string>  
#include <utility>  
#include <iomanip>  
  
using namespace std;  
  
template<typename T1, typename T2>  
auto Plus(T1&& t1, T2&& t2) ->   
   decltype(forward<T1>(t1) + forward<T2>(t2))  
{  
   return forward<T1>(t1) + forward<T2>(t2);  
}  
  
class X  
{  
   friend X operator+(const X& x1, const X& x2)  
   {  
      return X(x1.m_data + x2.m_data);  
   }  
  
public:  
   X(int data) : m_data(data) {}  
   int Dump() const { return m_data;}  
private:  
   int m_data;  
};  
  
int main()  
{  
   // Integer   
   int i = 4;  
   cout <<   
      "Plus(i, 9) = " <<   
      Plus(i, 9) << endl;  
  
   // Floating point  
   float dx = 4.0;  
   float dy = 9.5;  
   cout <<     
      setprecision(3) <<   
      "Plus(dx, dy) = " <<  
      Plus(dx, dy) << endl;  
  
   // String        
   string hello = "Hello, ";  
   string world = "world!";  
   cout << Plus(hello, world) << endl;  
  
   // Custom type  
   X x1(20);  
   X x2(22);  
   X x3 = Plus(x1, x2);  
   cout <<   
      "x3.Dump() = " <<   
      x3.Dump() << endl;  
}  
```  
  
```Output  
Plus(i, 9) = 13
Plus(dx, dy) = 13.5
Hello, world!
x3.Dump() = 42
```
  
## <a name="example"></a>示例
**2017 及更高版本的 visual Studio:**编译器模板将声明而不是实例化时就会分析 decltype 参数。 因此，如果在 decltype 参数中找到非依赖专用化，则它不会被推迟到实例化时间，而会被立即处理，并且将在此时诊断产生的所有错误。

以下示例显示了在声明时引发的这类编译器错误：

```cpp
#include <utility>
template <class T, class ReturnT, class... ArgsT> class IsCallable
{
public:
   struct BadType {};
   template <class U>
   static decltype(std::declval<T>()(std::declval<ArgsT>()...)) Test(int); //C2064. Should be declval<U>
   template <class U>
   static BadType Test(...);
   static constexpr bool value = std::is_convertible<decltype(Test<T>(0)), ReturnT>::value;
};

constexpr bool test1 = IsCallable<int(), int>::value;
static_assert(test1, "PASS1");
constexpr bool test2 = !IsCallable<int*, int>::value;
static_assert(test2, "PASS2");
```

## <a name="requirements"></a>惠?  
 Visual C++ 2010 或更高版本。  
  
 `decltype(auto)`需要 Visual Studio 2015 或更高版本。  
  
