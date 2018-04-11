---
title: "函数 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 01/25/2018
ms.reviewer: 
ms.suite: 
ms.technology:
- cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs:
- C++
helpviewer_keywords:
- defaults, arguments
- function definitions
- function definitions, about function definitions
- default arguments
- declarators, functions
ms.assetid: 33ba01d5-75b5-48d2-8eab-5483ac7d2274
caps.latest.revision: 
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload:
- cplusplus
ms.openlocfilehash: 88031e4f47bea363c441986c72d5f890c03447f7
ms.sourcegitcommit: 185e11ab93af56ffc650fe42fb5ccdf1683e3847
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="functions-c"></a>函数 (C++)
函数是执行某种操作的代码块。 函数可以选择性地定义使调用方可以将实参传递到函数中的输入形参。 函数可以选择性地返回值作为输出。 函数可用于在单个可重用块中封装常用操作（理想情况是使用可清晰地描述函数行为的名称）。 以下函数接受从调用方的两个整数并返回其总和;`a`和`b`是*参数*类型的`int`。  
  
```  
int sum(int a, int b)  
{  
    return a + b;  
}  
```  
  
 该函数可以是调用，或*调用*，从任意数量的程序中的位置。 传递给函数的值是*参数*，其类型必须与函数定义中的参数类型兼容。  
  
```  
int main()  
{  
    int i = sum(10, 32);  
    int j = sum(i, 66);  
    cout << "The value of j is" << j << endl; // 108  
}  
```  
  
 对于函数长度没有实际限制，不过良好的设计应以执行单个明确定义的任务的函数为目标。 复杂算法应尽可能分解成易于理解的更简单函数。  
  
 在类范围中定义的函数称为成员函数。 在 C++ 中（与其他语言不同），函数还可以在命名空间范围中定义（包括隐式全局命名空间）。 此类函数称为*free 函数*或*非成员函数*; 它们在标准库中广泛使用。  

函数可能*重载*，这意味着不同版本的函数可能会共享相同的名称，如果它们的数量和/或形参类型不同。 有关详细信息，请参阅[函数重载](../cpp/function-overloading.md)。
  
## <a name="parts-of-a-function-declaration"></a>函数声明的各个部分  
 最小函数*声明*包含返回类型、 函数名称和参数列表 （可能为空），以及向编译器提供附加说明的可选关键字。 下面的示例是函数声明：

 ```cpp
 int sum(int a, int b);
 ```

 函数定义包含声明，加上*正文*，这是大括号之间的所有代码：
 
 ```cpp
int sum(int a, int b)  
{  
    return a + b;  
}  
```
 后接分号的函数声明可以出现在程序中的多个位置处。 它必须在每个翻译单元中对该函数的任何调用之前出现。 根据单个定义规则 (ODR)，函数定义必须仅在程序中出现一次。  
  
 函数声明的必需部分有：  
  
1.  返回类型，指定函数将返回的值的类型，如果不返回任何值，则为 `void`。 在 C++ 11 中，`auto`是有效的返回类型，指示编译器从返回语句推断类型。 在 C++14 中，还允许使用 decltype(auto)。 有关详细信息，请参阅下面的“返回类型中的类型推导”。  
  
2.  函数名，必须以字母或下划线开头，不能包含空格。 一般而言，标准库函数名中的前导下划线指示私有成员函数，或不是供你的代码使用的非成员函数。 
  
3.  参数列表（一组用大括号限定、逗号分隔的零个或多个参数），指定类型以及可以用于在函数体内访问值的可选局部变量名。 
  
 函数声明的可选部分有：  
  
1.  `constexpr`，指示函数的返回值是常量值，可以在编译时进行计算。  
  
    ```  
    constexpr float exp(float x, int n)  
    {  
        return n == 0 ? 1 :  
            n % 2 == 0 ? exp(x * x, n / 2) :  
            exp(x * x, (n - 1) / 2) * x;  
    };  
    ```  
  
2.  其 `linkage` 规范（`extern` 或 `static`）。  
  
    ```  
    Declare printf with C linkage.  
    extern "C" int printf( const char *fmt, ... );  
  
    ```  
  
     有关详细信息，请参阅[程序和链接](../cpp/program-and-linkage-cpp.md)。  
  
3.  `inline`，指示编译器将对函数的每个调用替换为函数代码本身。 在某个函数快速执行并且在性能关键代码段中重复调用的情况下，内联可以帮助提高性能。  
  
    ```  
    inline double Account::GetBalance()  
    {  
        return balance;  
    }  
    ```  
  
     有关详细信息，请参阅[内联函数](../cpp/inline-functions-cpp.md)。  
  
4.  A`noexcept`表达式，指定函数是否可以引发异常。 在下面的示例中，函数在 `is_pod` 表达式计算结果为 `true` 时不引发异常。  
  
    ```  
    #include <type_traits>  
  
    template <typename T>  
    T copy_object(T& obj) noexcept(std::is_pod<T>) {...}  
    ```  
  
     有关详细信息，请参阅[noexcept](../cpp/noexcept-cpp.md)。  
  
5.  （仅限成员函数）cv 限定符，指定函数是 `const` 还是 `volatile`。  
  
6.  （仅限成员函数）`virtual`、`override` 或 `final`。 `virtual` 指定可以在派生类中重写函数。 `override` 表示派生类中的函数在重写虚函数。 `final` 表示函数不能在任何进一步的派生类中进行重写。 有关详细信息，请参阅[虚函数](../cpp/virtual-functions.md)。  
  
7.  （仅限成员函数仅）应用于成员函数的 `static` 表示函数不与类的任何对象实例关联。  
  
8.  （仅限非静态成员函数）Ref 限定符，向编译器指定的函数时要选择的重载隐式对象参数 (* 这) 是右值引用与左值引用。 有关详细信息，请参阅[函数重载](function-overloading.md#ref-qualifiers)。 
  
 下图显示了函数定义的各个部分。 灰色区域是函数体。  
  
 ![函数定义部分](../cpp/media/vc38ru1.gif "vc38RU1")  
函数定义部分  
  
## <a name="function-definitions"></a>函数定义  
 函数体内声明的变量称为局部变量。 它们会在函数退出时超出范围；因此，函数应永远不返回对局部变量的引用！  
  
## <a name="function-templates"></a>函数模板  
 函数模板类似于类模板；它基于模板参数生成具体功能。 在许多情况下，模板能够推断类型参数，因此无需显式指定它们。  
  
```  
template<typename Lhs, typename Rhs>  
auto Add2(const Lhs& lhs, const Rhs& rhs)  
{  
    return lhs + rhs;  
}  
  
auto a = Add2(3.13, 2.895); // a is a double  
auto b = Add2(string{ "Hello" }, string{ " World" }); // b is a std::string  
```  
  
 有关详细信息，请参阅[函数模板](../cpp/function-templates.md)  
  
## <a name="function-parameters-and-arguments"></a>函数参数和参数  
 函数具有零种或多种类型的逗号分隔参数列表，其中每个参数都具有可以用于在函数体内访问它的名称。 函数模板可以指定其他类型或值参数。 调用方传递实参（其类型与形参列表兼容的具体值）。  
  
 默认情况下，参数通过值传递给函数，这意味着函数会收到所传递的对象的副本。 对于大型对象，创建副本可能成本高昂，并非始终必要。 若要使实参通过引用（特别是左值引用）进行传递，请向形参添加引用限定符：  
  
```  
void DoSomething(std::string& input){...}  
```  
  
 当函数修改通过引用传递的参数时，它会修改原始对象，而不是本地副本。 若要防止函数修改这类参数，请将参数限定为 const&：  
  
```  
void DoSomething(const std::string& input){...}  
```  
  
 **C++ 11:**若要显式处理通过右值引用或左值引用传递参数，使用双与号参数以指示通用引用：  
  
```  
void DoSomething(const std::string&& input){...}  
```  
  
 只要关键字 `void` 是实参声明列表中的第一个也是唯一一个成员，那么在形参声明列表中使用单个关键字 `void` 声明的函数就没有实参。 列表中的其他地方的 `void` 类型的参数产生错误。 例如:  
  
```  
  
// OK same as GetTickCount()  
long GetTickCount( void );   
```  
  
 请注意，尽管指定 `void` 参数是非法（此处所述的除外），但派生自类型 `void` 的类型（如指向 `void` 的指针和 `void` 的数组）可以出现在参数声明列表中的任何位置。  
  
### <a name="default-arguments"></a>默认参数  
 函数签名中的最后一个或几个形参可能会分配有默认实参，这意味着调用方可能会在调用函数时省略实参（除非要指定某个其他值）。  
  
```  
int DoSomething(int num,   
    string str,   
    Allocator& alloc = defaultAllocator)  
{ ... }  
  
// OK both parameters are at end  
int DoSomethingElse(int num,   
    string str = string{ "Working" },   
    Allocator& alloc = defaultAllocator)  
{ ... }  
  
// C2548: 'DoMore': missing default parameter for parameter 2  
int DoMore(int num = 5, // Not a trailing parameter!  
    string str,  
    Allocator& = defaultAllocator)  
{...}  
```  
  
 有关详细信息，请参阅[默认参数](../cpp/default-arguments.md)。  
  
## <a name="function-return-types"></a>函数返回类型  
 函数可能不会返回另一个函数或内置数组;但是它可以返回到这些类型的指针或*lambda*，由此产生的函数对象。 除了这些情况，函数可以返回处于范围内的任何类型的值，或者它可以返回任何值（在这种情况下返回类型是 `void`）。  
  
### <a name="trailing-return-types"></a>结尾返回类型  
 “普通”返回类型位于函数签名左侧。 A*尾随返回类型*位于签名的大多数的右侧，前面有-> 运算符。 当返回值的类型取决于模板参数时，结尾返回类型在函数模板中尤其有用。  
  
```  
template<typename Lhs, typename Rhs>  
auto Add(const Lhs& lhs, const Rhs& rhs) -> decltype(lhs + rhs)  
{  
    return lhs + rhs;   
}  
```  
  
 当 `auto` 与结尾返回类型结合使用时，它对于 decltype 表达式生成的任何内容都只用作占位符，本身不执行类型推导。  

   
## <a name="function-local-variables"></a>函数局部变量  
 在函数体内声明的变量称为*局部变量*或只需*本地*。 非静态局部变量仅在函数体中可见，如果它们在堆栈上声明，则会在函数退出时超出范围。 构造局部变量并通过值返回它时，编译器通常可以执行返回值优化以避免不必要的复制操作。 如果通过引用返回局部变量，则编译器会发出警告，因为调用方为使用该引用而进行的任何尝试会在局部变量已销毁之后进行。  
  
 局部静态对象将在 `atexit` 指定的终止期间销毁。 如果某个静态对象由于程序的控制流跳过了其声明而未构造，则不会尝试销毁该对象。  
  
### <a name="static-local-variables"></a>静态局部变量  
 在 C++ 中，局部变量可以声明为静态。 变量仅在函数体中可见，但是对于函数的所有实例，存在变量的单个副本。  
  
###  <a name="type_deduction"></a>返回类型 (C++ 14) 中的类型推导  
 在 C++14 中，可以使用 `auto` 指示编译器从函数体推断返回类型，而不必提供结尾返回类型。 请注意，`auto` 始终推导为按值返回。 使用 `auto&&` 可指示编译器推导引用。  
  
 在此示例中，`auto` 会推导为 lhs 和 rhs 之和的非常量值副本。  
  
```  
template<typename Lhs, typename Rhs>  
auto Add2(const Lhs& lhs, const Rhs& rhs)  
{  
    return lhs + rhs; //returns a non-const object by value  
}  
```  
  
 请注意，`auto`不会保留它推导的类型的常量性。 对于返回值需要保留其参数的常量性或引用性的转发函数，可以使用 `decltype(auto)` 关键字，该关键字使用 `decltype` 类型推断规则并保留所有类型信息。 `decltype(auto)` 可以用作左侧的普通返回值，或结尾返回值。  
  
 下面的示例 (基于代码从[N3493](http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2013/n3493.html))，显示`decltype(auto)`正在使用的模板实例化之前未知的返回类型的函数参数完美转发。  
  
```  
template<typename F, typename Tuple = tuple<T...>, int... I>  
decltype(auto) apply_(F&& f, Tuple&& args, index_sequence<I...>)   
{  
    return std::forward<F>(f)(std::get<I>(std::forward<Tuple>(args))...);  
}  
  
template<typename F, typename Tuple = tuple<T...>,  
    typename Indices = make_index_sequence<tuple_size<Tuple>::value >>  
   decltype( auto)  
    apply(F&& f, Tuple&& args)      
{  
    return apply_(std::forward<F>(f), std::forward<Tuple>(args), Indices());  
}  
}  
```  
## <a name="returning-multiple-values-from-a-function"></a>从函数返回多个值
有多种方法，以从函数返回多个值：

1. 封装命名类或结构对象中的值。 需要对调用方可见的类或结构的定义：

```cpp
#include <string>
#include <iostream>

using namespace std;

struct S
{
    string name;
    int num;
};

S g()
{
    string t{ "hello" };
    int u{ 42 };
    return { t, u };
}

int main()
{
    S s = g();
    cout << s.name << " " << s.num << endl;
    return 0;
}
```

2. 返回的 std:: tuple 或 std:: pair 对象：

```cpp
#include <tuple>
#include <string>
#include <iostream>

using namespace std;


tuple<int, string, double> f()
{
    int i{ 108 };
    string s{ "Some text" };
    double d{ .01 };
    return { i,s,d };
}

int main()
{
    auto t = f();
    cout << get<0>(t) << " " << get<1>(t) << " " << get<2>(t) << endl;
    
    // --or--

    int myval;
    string myname;
    double mydecimal;
    tie(myval, myname, mydecimal) = f();
    cout << myval << " " << myname << " " << mydecimal << endl;

    return 0;
}
```

3. **Visual Studio 2017 15.3 及更高版本**(适用于[/std:C++ 17](../build/reference/std-specify-language-standard-version.md)): 使用结构化绑定。 结构化绑定的优点是，存储返回值的变量进行初始化它们在声明的同时，在某些情况下，这可能会显著更有效。 在此语句-`auto[x, y, z] = f();`-括号引入并初始化在整个函数块的范围内的名称。  

```cpp
#include <tuple>
#include <string>
#include <iostream>

using namespace std;

tuple<int, string, double> f()
{
    int i{ 108 };
    string s{ "Some text" };
    double d{ .01 };
    return { i,s,d };
}
struct S
{
    string name;
    int num;
};

S g()
{
    string t{ "hello" };
    int u{ 42 };
    return { t, u };
}

int main()
{
    auto[x, y, z] = f(); // init from tuple
    cout << x << " " << y << " " << z << endl;

    auto[a, b] = g(); // init from POD struct
    cout << a << " " << b << endl;
    return 0;
}
```

4. 除了使用返回值本身，你可以"返回"值通过定义任意数量的参数以使用按引用传递，以便可以修改该函数或将其初始化的调用方提供的对象的值。 有关详细信息，请参阅[引用类型函数参数](reference-type-function-arguments.md)。  
  
## <a name="function-pointers"></a>函数指针  
 C++ 通过与 C 语言相同的方式支持函数指针。 但是更加类型安全的替代方法通常是使用函数对象。  
  
 建议使用 `typedef` 声明函数指针类型的别名（如果声明返回函数指针类型的函数）。  例如  
  
```  
typedef int (*fp)(int);  
fp myFunction(char* s); // function returning function pointer  
```  
  
 如果不执行此操作，则函数声明的正确语法可以通过用函数名称和参数列表替换标识符（上例中为 `fp`）来从函数指针的声明符语法推导出，如下所示：  
  
```  
int (*myFunction(char* s))(int);  
```  
  
 前面的声明与使用上面的 typedef 的声明等效。  
  
## <a name="see-also"></a>请参阅  
 [函数重载](../cpp/function-overloading.md)   
 [具有变量参数列表的函数](../cpp/functions-with-variable-argument-lists-cpp.md)   
 [显式默认函数和已删除函数](../cpp/explicitly-defaulted-and-deleted-functions.md)   
 [针对函数的依赖于参数的名称 (Koenig) 查找](../cpp/argument-dependent-name-koenig-lookup-on-functions.md)   
 [默认参数](../cpp/default-arguments.md)   
 [内联函数](../cpp/inline-functions-cpp.md)