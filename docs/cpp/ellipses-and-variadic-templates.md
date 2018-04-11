---
title: "省略号和可变参数模板 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
ms.assetid: f20967d9-c967-4fd2-b902-2bb1d5ed87e3
caps.latest.revision: "17"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: b6d3d0fa1dc4e2e4d817280fa83b26c56732cb2c
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="ellipses-and-variadic-templates"></a>省略号和可变参数模板
这篇文章演示如何使用省略号 (`...`) 与 C++ 可变参数模板。 省略号具有 C 和 C++ 中的许多用途。 其中包括变量参数列表的函数。 `printf()` C 运行时库中的功能的已知示例之一。  
  
 A*可变参数模板*是支持任意数量的参数的类或函数模板。 此机制是对 C++ 库开发人员尤其有用，因为您可以将其应用于类模板和函数模板，从而提供了各种各样的功能类型安全和重要的功能和灵活性。  
  
## <a name="syntax"></a>语法  
 可变参数模板通过两种方式使用省略号。 参数名称的左侧，它表示*参数包*，右侧的参数名称，参数包扩展为单独的名称。  
  
 下面是一个基本示例的*可变参数模板类*定义语法：  
  
```cpp  
template<typename... Arguments> class classname;  
```  
  
 对于参数包和扩展，您可根据您的偏好在省略号周围添加空白，如以下这些示例所示：  
  
```cpp  
template<typename ...Arguments> class classname;  
```  
  
 或此操作：  
  
```cpp  
template<typename ... Arguments> class classname;  
```  
  
 请注意，本文使用第一个示例中显示的约定（省略号已附加到 `typename`）。  
  
 在前面的示例中，`Arguments` 是参数包。 类`classname`可以接受数目可变的参数，如这些示例所示：  
  
```cpp  
template<typename... Arguments> class vtclass;  
  
vtclass< > vtinstance1;  
vtclass<int> vtinstance2;  
vtclass<float, bool> vtinstance3;  
vtclass<long, std::vector<int>, std::string> vtinstance4;  
  
```  
  
 通过使用可变参数模板类定义，你可能还需要至少一个参数：  
  
```cpp  
template <typename First, typename... Rest> class classname;  
  
```  
  
 下面是一个基本示例的*可变参数模板函数*语法：  
  
```cpp  
template <typename... Arguments> returntype functionname(Arguments... args);  
```  
  
 `Arguments`如所示在下一步的部分中，然后使用，为扩展参数包**了解可变参数模板**。  
  
 还可能有其他形式的可变参数模板函数的语法，其中包括但不是限于这些示例：  
  
```cpp  
template <typename... Arguments> returntype functionname(Arguments&... args);   
template <typename... Arguments> returntype functionname(Arguments&&... args);  
template <typename... Arguments> returntype functionname(Arguments*... args);  
```  
  
 说明符喜欢`const`还允许将：  
  
```cpp  
template <typename... Arguments> returntype functionname(const Arguments&... args);  
  
```  
  
 作为可变参数模板的类定义，可以进行需要至少一个参数的函数：  
  
```cpp  
template <typename First, typename... Rest> returntype functionname(const First& first, const Rest&... args);  
  
```  
  
 可变参数模板使用`sizeof...()`运算符 (与较旧无关`sizeof()`运算符):  
  
```cpp  
template<typename... Arguments>  
void tfunc(const Arguments&... args)  
{  
    constexpr auto numargs{ sizeof...(Arguments) };  
  
    X xobj[numargs]; // array of some previously defined type X  
  
    helper_func(xobj, args...);  
}  
  
```  
  
## <a name="more-about-ellipsis-placement"></a>有关省略号位置的更多信息  
 之前，本文介绍了省略号位置，此位置将参数包和扩展定义为“参数名称的左侧表示参数包，参数名称的右侧将参数包扩展为单独的名称”。 这从技术上讲的确如此，但在转换为代码的过程中可能会造成混淆。 以此为例：  
  
-   模板参数列表中 (`template <parameter-list>`)，`typename...`引入了模板参数包。  
  
-   在参数声明子句中 (`func(parameter-list)`)、"顶级"省略号引入函数参数包，并且省略号定位是重要：  
  
    ```cpp  
    // v1 is NOT a function parameter pack:  
    template <typename... Types> void func1(std::vector<Types...> v1);   
  
    // v2 IS a function parameter pack:  
    template <typename... Types> void func2(std::vector<Types>... v2);   
    ```  
  
-   其中省略号显示在参数名称的后面，并且您具有一个参数包扩展。  
  
## <a name="example"></a>示例  
 阐释可变参数模板函数机制一种好方法是使用在重新编写的部分的功能`printf`:  
  
```cpp  
#include <iostream>  
  
using namespace std;  
  
void print() {  
    cout << endl;  
}  
  
template <typename T> void print(const T& t) {  
    cout << t << endl;  
}  
  
template <typename First, typename... Rest> void print(const First& first, const Rest&... rest) {  
    cout << first << ", ";  
    print(rest...); // recursive call using pack expansion syntax  
}  
  
int main()  
{  
    print(); // calls first overload, outputting only a newline  
    print(1); // calls second overload  
  
    // these call the third overload, the variadic template,   
    // which uses recursion as needed.  
    print(10, 20);  
    print(100, 200, 300);  
    print("first", 2, "third", 3.14159);  
}  
  
```  
  
## <a name="output"></a>输出  
  
```  
  
1  
10, 20  
100, 200, 300  
first, 2, third, 3.14159  
```  
  
> [!NOTE]
>  大多数合并可变参数模板函数的实现使用某种形式的递归，但它是与传统的递归略有不同。  传统的递归涉及调用其自身使用相同的签名的函数。 （它可能是重载或模板化，但每次选择相同的签名。）可变参数递归涉及通过使用不同 （几乎总是递减） 数量参数，并从而戳出不同的签名每次调用可变参数函数模板。 "基本用例"是仍是必需的但有不同的递归性质。  
  
