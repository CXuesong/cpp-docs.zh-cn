---
title: "模板 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: template_cpp
dev_langs: C++
helpviewer_keywords:
- templates, C++
- templates [C++]
ms.assetid: 90fcc14a-2092-47af-9d2e-dba26d25b872
caps.latest.revision: "21"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 935bee8447ad0d49ae965fb92538d2e260ec68ef
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="templates-c"></a>模板 (C++)
模板是用于在 C++ 中泛型的编程的基础。 作为强类型语言，C++ 需要具有特定类型，显式声明由程序员或由编译器推导的所有变量。 但是，许多数据结构和算法如果查阅无论它们只在运行哪种类型相同。 模板启用您定义的操作的类或函数，并使用户能够指定哪些具体类型这些操作不应处理。  
  
## <a name="defining-and-using-templates"></a>定义和使用模板  
 模板是一个构造，用于生成普通类型或函数在编译时根据在用户提供的模板参数的参数。 例如，你可以定义函数模板如下：  
  
```cpp  
template <typename T>  
T minimum(const T& lhs, const T& rhs)  
{  
    return lhs < rhs ? lhs : rhs;  
}  
```  
  
 上面的代码介绍了具有单个类型参数的泛型函数模板`T`，其返回值和调用参数 （lhs 和 rhs） 是所有此类型。 你可以指定任何你类似，但通过约定单个大写字母最常使用名称的类型参数。 `T`是模板参数，则`typename`关键字显示此参数是一种类型的占位符。 当调用函数时，编译器将替换的每个实例`T`与具体的类型参数已由用户指定，或者由编译器推导。 过程中，编译器将生成一个类，或从模板的函数称为*模板实例化*;  `minimum<int>`是实例化的模板`minimum<T>`。  
  
 在其他位置，用户可以声明的模板的专用于 int。 实例假定 get_a() 和 get_b() 是返回 int 的函数：  
  
```  
int a = get_a();  
int b = get_b();  
int i = minimum<int>(a, b);  
```  
  
 但是，因为这是函数模板和编译器可以推断出的一种`T`从参数`a`和`b`，则可以调用它时就像普通函数一样：  
  
```cpp  
int i = minimum(a, b);  
```  
  
 当编译器遇到该最后一条语句时，它将生成新函数中的每个匹配项的*T*模板中使用替换`int`:  
  
```  
  
      int minimum(const int& lhs, const int& rhs)  
{  
    return lhs < rhs ? lhs : rhs;  
}  
```  
  
 如何编译器在函数模板中执行类型推导的规则基于普通函数的规则。 有关详细信息，请参阅[重载解析的函数模板调用](../cpp/overload-resolution-of-function-template-calls.md)。  
  
## <a id="type_parameters"></a>类型参数  
 在`minimum`模板更高版本，请注意，类型参数`T`直到添加 const 和引用限定符下使用在函数调用参数中，未以任何方式进行限定。  
  
 类型参数的数目没有实际限制。 使用逗号分隔多个参数：  
  
```cpp  
template <typename T, typename U, typename V> class Foo{};  
  
```  
  
 关键字`class`等效于`typename`在此上下文中。 您可以表示为前面的示例：  
  
```  
template <class T, class U, class V> class Foo{};   
```  
  
 省略号运算符 （...） 可用于定义采用任意数目的零个或多个类型参数的模板：  
  
```cpp  
template<typename... Arguments> class vtclass;  
  
vtclass< > vtinstance1;  
vtclass<int> vtinstance2;  
vtclass<float, bool> vtinstance3;  
```  
  
 任何内置或用户定义类型可以用作类型参数。 例如，你可以使用 std:: vector 标准库中存储的整数、 双精度型值、 字符串，MyClass、 const MyClass *、 MyClass （& a)。 使用模板时的主要限制是一个类型参数必须支持应用于类型参数的任何操作。 例如，如果我们调用最小如此示例所示使用 MyClass:  
  
```cpp  
class MyClass  
{  
public:  
    int num;  
    std::wstring description;  
};  
  
int main()  
{      
    MyClass mc1 {1, L"hello"};  
    MyClass mc2 {2, L"goodbye"};  
    auto result = minimum(mc1, mc2); // Error! C2678  
}  
  
```  
  
 将生成编译器错误，因为 MyClass 不提供的重载 < 运算符。  
  
 尽管你可以定义强制执行这样的限制的模板，没有任何特定模板中所有的类型参数属于相同的对象层次结构，固有要求。 可将面向对象的技术与模板; 合并例如，你可以存储派生 * 向量中\<基\*>。    请注意的参数必须为指针  
  
```  
vector<MyClass*> vec;  
   MyDerived d(3, L"back again", time(0));  
   vec.push_back(&d);  
  
   // or more realistically:  
   vector<shared_ptr<MyClass>> vec2;  
   vec2.push_back(make_shared<MyDerived>());  
```  
  
 向量以及其他标准库容器施加的元素的基本要求`T`在于`T`为副本分配和复制构造。  
  
## <a name="non-type-parameters"></a>非类型参数  
 与不同的是 C# 和 Java 等其他语言中的泛型类型，C++ 模板支持非类型参数，也称为值参数。 例如，你可以与此示例中，类似于标准库中的 std:: array 类提供一个常量的整数值来指定数组的长度：  
  
```  
template<typename T, size_t L>  
class MyArray  
{  
    T arr[L];  
public:  
    MyArray() { ... }  
};  
  
```  
  
 请注意模板声明中的语法。 Size_t 值传入作为模板参数在编译时，并且必须为常量或 constexpr 表达式。 你将使用它如下：  
  
```cpp  
MyArray<MyClass*, 10> arr;  
```  
  
 其他类型的值包括指针和引用可以作为非类型参数传递中。 例如，你可以传递一个指针到函数或函数对象，以自定义模板代码内的某些操作。  
  
## <a id="template_parameters"></a>作为模板参数的模板  
 模板可以是模板参数。 在此示例中，MyClass2 具有两个模板参数： 类型名称参数`T`和模板参数`Arr`:  
  
```cpp  
template<typename T, template<typename U, int I> class Arr>  
class MyClass2  
{  
    T t; //OK  
    Arr<T, 10> a;  
    U u; //Error. U not in scope  
};  
```  
  
 因为`Arr`参数本身具有没有正文，不需要其参数名称。 事实上，则会出错来指代`Arr`的 typename 或类参数名称的主体内`MyClass2`。 为此，`Arr`的可以省略类型参数名称，如本示例中所示：  
  
```cpp  
template<typename T, template<typename, int> class Arr>  
class MyClass2  
{  
    T t; //OK  
    Arr<T, 10> a;  
};  
```  
  
## <a name="default-template-arguments"></a>默认模板参数  
 类和函数模板可以具有默认参数。 当模板具有默认参数可以将它未指定何时使用它。 例如，已分配器的默认参数的 std:: vector 模板：  
  
```cpp  
template <class T, class Allocator = allocator<T>> class vector;  
```  
  
 在大多数情况下默认 std::allocator 类都可以接受，这样你可以将一个向量，如下：  
  
```cpp  
vector<int> myInts;  
```  
  
 但是，如果必要您可以指定自定义分配器如下所示：  
  
```cpp  
vector<int, MyAllocator> ints;  
```  
  
 对于多个模板参数，第一个默认参数后的所有参数必须具有默认参数。  
  
 当使用其参数都所有默认的模板，则使用空尖括号：  
  
```cpp  
template<typename A = int, typename B = double>  
class Bar  
{  
    //...  
};  
...  
int main()  
{  
    Bar<> bar; // use all default type arguments  
}  
  
```  
  
## <a name="template-specialization"></a>模板专用化  
 在某些情况下，它并不可能或必需实现的一个模板来定义为任何类型相同的代码。 例如，你可能想要定义要执行仅当类型参数是一个指针或 std:: wstring，或从特定的基类派生的类型的代码路径。  在这种情况下可以定义*专用化*该特定类型的模板。 如果用户与该类型实例化模板，编译器将使用专用化生成类，并且对于所有其他类型，因此该编译器选择更多常规模板。 所有参数都专用都化的专用化是*完成专用化*。 如果仅专用的某些参数，它称为*部分专用化*。  
  
```cpp  
template <typename K, typename V>  
class MyMap{/*...*/};  
  
// partial specialization for string keys  
template<typename V>  
class MyMap<string, V> {/*...*/};  
...  
MyMap<int, MyClass> classes; // uses original template  
MyMap<string, MyClass> classes2; // uses the partial specialization  
  
```  
  
 一个模板可以有任意数量的专用化，只要是唯一的每个专用的类型参数。   仅类模板可以部分专用化。 必须在原始模板相同的命名空间中声明所有完成和部分专用化模板。  
  
 有关详细信息，请参阅[模板专用化](../cpp/template-specialization-cpp.md)。