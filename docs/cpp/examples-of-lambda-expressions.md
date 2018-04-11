---
title: "Lambda 表达式的示例 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords: lambda expressions [C++], examples
ms.assetid: 52506b15-0771-4190-a966-2f302049ca86
caps.latest.revision: "22"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 3177896b8cfdff78b0af2aeb40873f31099ce7d4
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="examples-of-lambda-expressions"></a>Lambda 表达式的示例
本文演示如何在你的程序中使用 lambda 表达式。 有关 lambda 表达式的概述，请参阅[Lambda 表达式](../cpp/lambda-expressions-in-cpp.md)。 有关 lambda 表达式结构的详细信息，请参阅[Lambda 表达式语法](../cpp/lambda-expression-syntax.md)。  
  
##  <a name="declaringLambdaExpressions"></a>声明 Lambda 表达式  
  
### <a name="example-1"></a>示例 1  
 由于 lambda 表达式已类型化，你可以将其分配给`auto`变量或[函数](../standard-library/function-class.md)对象，如下所示：  
  
### <a name="code"></a>代码  
  
```cpp  
// declaring_lambda_expressions1.cpp  
// compile with: /EHsc /W4  
#include <functional>  
#include <iostream>  
  
int main()  
{  
  
    using namespace std;  
  
    // Assign the lambda expression that adds two numbers to an auto variable.  
    auto f1 = [](int x, int y) { return x + y; };  
  
    cout << f1(2, 3) << endl;  
  
    // Assign the same lambda expression to a function object.  
    function<int(int, int)> f2 = [](int x, int y) { return x + y; };  
  
    cout << f2(3, 4) << endl;  
}  
```  
  
### <a name="output"></a>输出  
  
```Output  
5  
7  
```  
  
### <a name="remarks"></a>备注  
 有关详细信息，请参阅[自动](../cpp/auto-cpp.md)，[函数类](../standard-library/function-class.md)，和[函数调用](../cpp/function-call-cpp.md)。  
  
 虽然 lambda 表达式多在函数的主体中声明，但是可以在初始化变量的任何地方声明。  
  
### <a name="example-2"></a>示例 2  
 Visual C++ 编译器将在声明而非调用 lambda 表达式时，将表达式绑定到捕获的变量。 以下示例显示一个通过值捕获局部变量 `i` 并通过引用捕获局部变量 `j` 的 lambda 表达式。 由于 lambda 表达式通过值捕获 `i`，因此在程序后面部分中重新指派 `i` 不影响该表达式的结果。 但是，由于 lambda 表达式通过引用捕获 `j`，因此重新指派 `j` 会影响该表达式的结果。  
  
### <a name="code"></a>代码  
  
```cpp  
// declaring_lambda_expressions2.cpp  
// compile with: /EHsc /W4  
#include <functional>  
#include <iostream>  
  
int main()  
{  
   using namespace std;  
  
   int i = 3;  
   int j = 5;  
  
   // The following lambda expression captures i by value and  
   // j by reference.  
   function<int (void)> f = [i, &j] { return i + j; };  
  
   // Change the values of i and j.  
   i = 22;  
   j = 44;  
  
   // Call f and print its result.  
   cout << f() << endl;  
}  
```  
  
### <a name="output"></a>输出  
  
```Output  
47  
```  
  
 [[本文内容](#top)]  
  
##  <a name="callingLambdaExpressions"></a>调用 Lambda 表达式  
 你可以立即调用 Lambda 表达式，如下面的代码片段所示。 第二个代码段演示如何 lambda 作为参数传递到 C++ 标准库算法如`find_if`。  
  
### <a name="example-1"></a>示例 1  
 以下示例声明的 lambda 表达式将返回两个整数的总和并使用参数 `5` 和 `4` 立即调用该表达式：  
  
### <a name="code"></a>代码  
  
```cpp  
// calling_lambda_expressions1.cpp  
// compile with: /EHsc  
#include <iostream>  
  
int main()  
{  
   using namespace std;  
   int n = [] (int x, int y) { return x + y; }(5, 4);  
   cout << n << endl;  
}  
```  
  
### <a name="output"></a>输出  
  
```Output  
9  
```  
  
### <a name="example-2"></a>示例 2  
 以下示例将 lambda 表达式作为参数传递给 `find_if` 函数。 如果 lambda 表达式的参数是偶数，则返回 `true`。  
  
### <a name="code"></a>代码  
  
```cpp  
// calling_lambda_expressions2.cpp  
// compile with: /EHsc /W4  
#include <list>  
#include <algorithm>  
#include <iostream>  
  
int main()  
{  
    using namespace std;  
  
    // Create a list of integers with a few initial elements.  
    list<int> numbers;  
    numbers.push_back(13);  
    numbers.push_back(17);  
    numbers.push_back(42);  
    numbers.push_back(46);  
    numbers.push_back(99);  
  
    // Use the find_if function and a lambda expression to find the   
    // first even number in the list.  
    const list<int>::const_iterator result =   
        find_if(numbers.begin(), numbers.end(),[](int n) { return (n % 2) == 0; });  
  
    // Print the result.  
    if (result != numbers.end()) {  
        cout << "The first even number in the list is " << *result << "." << endl;  
    } else {  
        cout << "The list contains no even numbers." << endl;  
    }  
}  
```  
  
### <a name="output"></a>输出  
  
```Output  
The first even number in the list is 42.  
```  
  
### <a name="remarks"></a>备注  
 有关详细信息`find_if`函数中，请参阅[find_if](../standard-library/algorithm-functions.md#find_if)。 有关执行公共算法的 C++ 标准库函数的详细信息，请参阅[\<算法 >](../standard-library/algorithm.md)。  
  
 [[本文内容](#top)]  
  
##  <a name="nestingLambdaExpressions"></a>嵌套 Lambda 表达式  
  
### <a name="example"></a>示例  
 你可以将 lambda 表达式嵌套在另一个中，如下例所示。 内部 lambda 表达式将其参数与 2 相乘并返回结果。 外部 lambda 表达式通过其参数调用内部 lambda 表达式并在结果上加 3。  
  
### <a name="code"></a>代码  
  
```cpp  
// nesting_lambda_expressions.cpp  
// compile with: /EHsc /W4  
#include <iostream>  
  
int main()  
{  
    using namespace std;  
  
    // The following lambda expression contains a nested lambda  
    // expression.  
    int timestwoplusthree = [](int x) { return [](int y) { return y * 2; }(x) + 3; }(5);  
  
    // Print the result.  
    cout << timestwoplusthree << endl;  
}  
  
```  
  
### <a name="output"></a>输出  
  
```Output  
13  
```  
  
### <a name="remarks"></a>备注  
 在该示例中，`[](int y) { return y * 2; }` 是嵌套的 lambda 表达式。  
  
 [[本文内容](#top)]  
  
##  <a name="higherOrderLambdaExpressions"></a>高阶 Lambda 函数  
  
### <a name="example"></a>示例  
 许多编程语言都支持这一概念*高阶函数。* 高阶函数是采用另一个 lambda 表达式作为其参数或返回 lambda 表达式的 lambda 表达式。 你可以使用[函数](../standard-library/function-class.md)类，使得 C++ lambda 表达式的行为类似高阶函数。 以下示例显示返回 `function` 对象的 lambda 表达式和采用 `function` 对象作为其参数的 lambda 表达式。  
  
### <a name="code"></a>代码  
  
```cpp  
// higher_order_lambda_expression.cpp  
// compile with: /EHsc /W4  
#include <iostream>  
#include <functional>  
  
int main()  
{  
    using namespace std;  
  
    // The following code declares a lambda expression that returns   
    // another lambda expression that adds two numbers.   
    // The returned lambda expression captures parameter x by value.  
    auto addtwointegers = [](int x) -> function<int(int)> {   
        return [=](int y) { return x + y; };   
    };  
  
    // The following code declares a lambda expression that takes another  
    // lambda expression as its argument.  
    // The lambda expression applies the argument z to the function f  
    // and multiplies by 2.  
    auto higherorder = [](const function<int(int)>& f, int z) {   
        return f(z) * 2;   
    };  
  
    // Call the lambda expression that is bound to higherorder.   
    auto answer = higherorder(addtwointegers(7), 8);  
  
    // Print the result, which is (7+8)*2.  
    cout << answer << endl;  
}  
  
```  
  
### <a name="output"></a>输出  
  
```Output  
30  
```  
  
 [[本文内容](#top)]  
  
##  <a name="methodLambdaExpressions"></a>在函数中使用 Lambda 表达式  
  
### <a name="example"></a>示例  
 你可以在函数的主体中使用 lambda 表达式。 lambda 表达式可以访问该封闭函数可访问的任何函数或数据成员。 你可以显式或隐式捕获 `this` 指针，以提供对封闭类的函数和数据成员的访问路径。  
**Visual Studio 2017 15.3 及更高版本**(适用于[/std:C++ 17](../build/reference/std-specify-language-standard-version.md)): 捕获`this`通过值 (`[*this]`) 当 lambda 将使用在异步或并行操作中代码的可能位置执行后的原始对象超出范围。
  
 你可以在函数中显式使用 `this` 指针，如下所示：  
  
```cpp  

// capture "this" by reference
void ApplyScale(const vector<int>& v) const  
{  
   for_each(v.begin(), v.end(),   
      [this](int n) { cout << n * _scale << endl; });  
}  

// capture "this" by value (Visual Studio 2017 version 15.3 and later)
void ApplyScale2(const vector<int>& v) const  
{  
   for_each(v.begin(), v.end(),   
      [*this](int n) { cout << n * _scale << endl; });  
}  

```  
  
 你也可以隐式捕获 `this` 指针：  
  
```  
void ApplyScale(const vector<int>& v) const  
{  
   for_each(v.begin(), v.end(),   
      [=](int n) { cout << n * _scale << endl; });  
}  
```  
  
 以下示例显示封装小数位数值的 `Scale` 类。  
  
```cpp  
// function_lambda_expression.cpp  
// compile with: /EHsc /W4  
#include <algorithm>  
#include <iostream>  
#include <vector>  
  
using namespace std;  
  
class Scale  
{  
public:  
    // The constructor.  
    explicit Scale(int scale) : _scale(scale) {}  
  
    // Prints the product of each element in a vector object   
    // and the scale value to the console.  
    void ApplyScale(const vector<int>& v) const  
    {  
        for_each(v.begin(), v.end(), [=](int n) { cout << n * _scale << endl; });  
    }  
  
private:  
    int _scale;  
};  
  
int main()  
{  
    vector<int> values;  
    values.push_back(1);  
    values.push_back(2);  
    values.push_back(3);  
    values.push_back(4);  
  
    // Create a Scale object that scales elements by 3 and apply  
    // it to the vector object. Does not modify the vector.  
    Scale s(3);  
    s.ApplyScale(values);  
}  
  
```  
  
### <a name="output"></a>输出  
  
```Output  
3  
6  
9  
12  
  
```  
  
### <a name="remarks"></a>备注  
 `ApplyScale` 函数使用 lambda 表达式打印小数位数值与 `vector` 对象中的每个元素的乘积。 lambda 表达式隐式捕获 `this` 指针，以便访问 `_scale` 成员。  
  
 [[本文内容](#top)]  
  
##  <a name="templateLambdaExpressions"></a>与模板配合使用 Lambda 表达式  
  
### <a name="example"></a>示例  
 由于 lambda 表达式已类型化，因此你可以将其与 C++ 模板一起使用。 下面的示例显示 `negate_all` 和 `print_all` 函数。 `negate_all` 函数将一元 `operator-` 应用于 `vector` 对象中的每个元素。 `print_all` 函数将 `vector` 对象中的每个元素打印到控制台。  
  
### <a name="code"></a>代码  
  
```cpp  
// template_lambda_expression.cpp  
// compile with: /EHsc  
#include <vector>  
#include <algorithm>  
#include <iostream>  
  
using namespace std;  
  
// Negates each element in the vector object. Assumes signed data type.  
template <typename T>  
void negate_all(vector<T>& v)  
{  
    for_each(v.begin(), v.end(), [](T& n) { n = -n; });  
}  
  
// Prints to the console each element in the vector object.  
template <typename T>  
void print_all(const vector<T>& v)  
{  
    for_each(v.begin(), v.end(), [](const T& n) { cout << n << endl; });  
}  
  
int main()  
{  
    // Create a vector of signed integers with a few elements.  
    vector<int> v;  
    v.push_back(34);  
    v.push_back(-43);  
    v.push_back(56);  
  
    print_all(v);  
    negate_all(v);  
    cout << "After negate_all():" << endl;  
    print_all(v);  
}  
  
```  
  
### <a name="output"></a>输出  
  
```Output  
34  
-43  
56  
After negate_all():  
-34  
43  
-56  
  
```  
  
### <a name="remarks"></a>备注  
 有关 C++ 模板的详细信息，请参阅[模板](../cpp/templates-cpp.md)。  
  
 [[本文内容](#top)]  
  
##  <a name="ehLambdaExpressions"></a>处理异常  
  
### <a name="example"></a>示例  
 lambda 表达式的主体遵循结构化异常处理 (SEH) 和 C++ 异常处理的原则。 你可以在 lambda 表达式主体中处理引发的异常或将异常处理推迟至封闭范围。 以下示例使用 `for_each` 函数和 lambda 表达式将一个 `vector` 对象的值填充到另一个中。 它使用`try` / `catch`块来处理无效访问第一个向量。  
  
### <a name="code"></a>代码  
  
```cpp  
// eh_lambda_expression.cpp  
// compile with: /EHsc /W4  
#include <vector>  
#include <algorithm>  
#include <iostream>  
using namespace std;  
  
int main()  
{  
    // Create a vector that contains 3 elements.  
    vector<int> elements(3);  
  
    // Create another vector that contains index values.  
    vector<int> indices(3);  
    indices[0] = 0;  
    indices[1] = -1; // This is not a valid subscript. It will trigger an exception.  
    indices[2] = 2;  
  
    // Use the values from the vector of index values to   
    // fill the elements vector. This example uses a   
    // try/catch block to handle invalid access to the   
    // elements vector.  
    try  
    {  
        for_each(indices.begin(), indices.end(), [&](int index) {   
            elements.at(index) = index;   
        });  
    }  
    catch (const out_of_range& e)  
    {  
        cerr << "Caught '" << e.what() << "'." << endl;  
    };  
}  
```  
  
### <a name="output"></a>输出  
  
```Output  
Caught 'invalid vector<T> subscript'.  
```  
  
### <a name="remarks"></a>备注  
 有关异常处理的详细信息，请参阅[异常处理](../cpp/exception-handling-in-visual-cpp.md)。  
  
 [[本文内容](#top)]  
  
##  <a name="managedLambdaExpressions"></a>托管类型传递配合使用 Lambda 表达式 (C++ /cli CLI)  
  
### <a name="example"></a>示例  
 lambda 表达式的捕获子句不能包含具有托管类型的变量。 但是，你可以将具有托管类型的实际参数传递到 lambda 表达式的形式参数列表。 以下示例包含一个 lambda 表达式，它通过值捕获局部非托管变量 `ch`，并采用 <xref:System.String?displayProperty=fullName> 对象作为其参数。  
  
### <a name="code"></a>代码  
  
```cpp  
// managed_lambda_expression.cpp  
// compile with: /clr  
using namespace System;  
  
int main()  
{  
    char ch = '!'; // a local unmanaged variable  
  
    // The following lambda expression captures local variables  
    // by value and takes a managed String object as its parameter.  
    [=](String ^s) {   
        Console::WriteLine(s + Convert::ToChar(ch));   
    }("Hello");  
}  
  
```  
  
### <a name="output"></a>输出  
  
```Output  
Hello!  
```  
  
### <a name="remarks"></a>备注  
 你还可以配合使用 lambda 表达式和 STL/CLR 库。 有关详细信息，请参阅[STL/CLR 库参考](../dotnet/stl-clr-library-reference.md)。  
  
> [!IMPORTANT]
>  以下公共语言运行时 (CLR) 托管实体中不支持 Lambda：`ref class`、`ref struct`、`value class` 和 `value struct`。  
  
 [[本文内容](#top)]  
  
## <a name="see-also"></a>请参阅  
 [Lambda 表达式](../cpp/lambda-expressions-in-cpp.md)   
 [Lambda 表达式语法](../cpp/lambda-expression-syntax.md)   
 [自动](../cpp/auto-cpp.md)   
 [function 类](../standard-library/function-class.md)   
 [find_if](../standard-library/algorithm-functions.md#find_if)   
 [\<算法 >](../standard-library/algorithm.md)   
 [函数调用](../cpp/function-call-cpp.md)   
 [模板](../cpp/templates-cpp.md)   
 [异常处理](../cpp/exception-handling-in-visual-cpp.md)   
 [STL/CLR 库参考](../dotnet/stl-clr-library-reference.md)