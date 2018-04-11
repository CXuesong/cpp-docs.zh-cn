---
title: "错误和异常处理 （现代 C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: article
dev_langs: C++
ms.assetid: a6c111d0-24f9-4bbb-997d-3db4569761b7
caps.latest.revision: "19"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 5b1ee1c7307f4e19db4ca0b7d03e218b0916538c
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="errors-and-exception-handling-modern-c"></a>错误和异常处理（现代 C++）
在现代 C++，在大多数情况下，报告和处理逻辑错误和运行时错误的首选的方式是使用异常。 这在堆栈可能包含检测到错误的函数和具有要知道如何处理它的上下文的函数之间的多个函数调用时尤其如此。 异常为检测到错误传递遍历调用堆栈信息的代码提供正式且明确定义的方式。  
  
 程序错误通常分为两类： 所导致的编程错误，例如、 出错"的索引超出范围"和是受控制的程序员来说，例如，运行时错误"网络服务不可用"的逻辑错误错误。 通过返回一个值，表示错误代码或特定功能的状态代码或通过设置 （可选） 若要查看每个函数调用后检索调用方，可能的全局变量在 C 样式编程和 COM，管理错误报告是否没有报告错误。 例如，COM 编程使用 HRESULT 返回值来传递给调用方的错误和 Win32 API 具有 GetLastError 函数，以检索已报告的调用堆栈的最后一个错误。 在这两种情况下，它由调用方识别的代码，并相应地对其做出响应。 如果调用方不显式处理的错误代码，则程序可能崩溃而不发出警告，或继续执行用错误的数据和生成不正确的结果。  
  
 由于以下原因，可将异常首选在现代 C++ 中：  
  
-   异常强制调用代码来识别的错误条件并处理。 未经处理的异常停止程序执行。  
  
-   异常跳转到可处理错误的调用堆栈中的点。 中间函数可以让异常传播。 它们不需要与其他层进行协调。  
  
-   异常堆栈展开机制根据定义完善的规则的作用域中的所有对象时都销毁后引发异常。  
  
-   异常实现完全分离之间检测到错误的代码和处理错误的代码。  
  
 以下简化的示例显示必要的语法，用于引发和捕获 C++ 中的异常。  
  
```cpp  
#include <stdexcept>  
#include <limits>  
#include <iostream>  
  
using namespace std;  
class MyClass  
{  
public:  
   void MyFunc(char c)  
   {  
      if(c < numeric_limits<char>::max())  
         throw invalid_argument("MyFunc argument too large.");  
      //...  
   }  
};  
  
int main()  
{  
   try  
   {  
      MyFunc(256); //cause an exception to throw  
   }  
  
   catch(invalid_argument& e)  
   {  
      cerr << e.what() << endl;  
      return -1;  
   }  
   //...  
   return 0;  
}  
  
```  
  
 在 C++ 异常类似于 C# 和 Java 等语言中。 在`try`块，如果引发异常*引发*它将是*捕获*通过第一个关联的`catch`块其类型与匹配的异常。 换而言之，执行将从跳`throw`语句`catch`语句。 如果找到没有任何可用的 catch 块，则`std::terminate`调用并退出程序。 C++ 中可能会引发任何类型;但是，我们建议您引发派生自的直接或间接类型`std::exception`。 在前面的示例中，异常类型， [invalid_argument](../standard-library/invalid-argument-class.md)，在中的标准库中定义[ \<stdexcept >](../standard-library/stdexcept.md)标头文件。 C++ 未提供，并且不需要，`finally`块来确保如果引发异常，会释放所有资源。 资源获取即是初始化 (RAII) 惯用语法，后者使用智能指针，提供所需的功能的资源清理。 有关详细信息，请参阅[如何： 设计异常安全性](../cpp/how-to-design-for-exception-safety.md)。 有关 C++ 堆栈展开机制的信息，请参阅[异常和堆栈展开](../cpp/exceptions-and-stack-unwinding-in-cpp.md)。  
  
## <a name="basic-guidelines"></a>基本指导原则  
 稳健的错误处理方法是任何编程语言中具有挑战性。 尽管异常提供了一些功能，支持良好的错误处理，但它们不能为你执行所有工作。 若要实现异常机制的优点，请记住异常设计你的代码。  
  
-   使用断言来检查应永远不会发生的错误。 使用异常来检查的错误，可能会发生，例如，在公共函数的输入参数验证错误。 有关详细信息，请参阅节**异常与。断言**。  
  
-   处理错误的代码可能由一个或多个干预的函数调用分隔的代码中检测到错误时，请使用异常。 考虑是否将检测到它的代码与紧密耦合处理该错误的代码时改为使用在性能关键的循环中的错误代码。 
  
-   对于每个函数可能会引发或传播异常，提供一个三个异常保证： 增强保证、 基本保证或 nothrow (noexcept) 保证。 有关详细信息，请参阅[如何： 设计异常安全性](../cpp/how-to-design-for-exception-safety.md)。  
  
-   通过值引发异常，通过引用捕获它们。 不捕获你无法处理。 
  
-   不要使用异常规范，后者在 C++ 11 中弃用。 有关详细信息，请参阅节**异常规范和 noexcept**。  
  
-   适用时，请使用标准库异常类型。 派生自定义异常类型从[异常类](../standard-library/exception-class.md)层次结构。  
  
-   不允许例外，以便从析构函数转义或内存释放函数。  
  
## <a name="exceptions-and-performance"></a>异常和性能  
 异常机制具有非常短的性能开销，如果不会引发异常。 如果引发异常，堆栈遍历的成本和展开是大致类似于函数调用的成本。 其他数据结构所需跟踪调用堆栈后的`try`输入块时，和其他说明需要展开堆栈，如果引发异常。 但是，在大多数情况下，在性能和内存需求量的成本并不重要。 对性能的异常的负面影响是，可能需要重大仅受到严重内存限制在系统上，或在性能关键循环错误可能会定期进行，并且与报告它的代码紧密耦合代码来处理它。 在任何情况下，不可能无需分析和测量知道异常的实际成本。 即使在这些少数情况下时成本相当高，你可以衡量它增加的正确性、 更轻松的可维护性和设计良好的异常策略提供其他好处。  
  
## <a name="exceptions-vs-assertions"></a>与断言异常  
 异常和断言是一种用于在程序中检测运行时错误的两个不同机制。 使用断言来在不应可从你的代码是否正确，则返回 true 的开发过程中测试条件。 在处理此类错误，使用异常，因为该错误指示代码中的一些东西，必须固定的、 没有点并不表示该程序必须在运行时从恢复的条件。 断言终止的语句处的执行，以便你可以检查调试器; 中的程序状态异常会继续从第一个适当的 catch 处理程序的执行。 使用异常来检查可能发生在运行时中，即使你的代码正确，例如，"文件找不到"或"内存不足。"的错误条件 你可能想要从这些情况，恢复，即使恢复只输出到日志消息并退出该程序。 始终使用异常检查对公共函数的参数。 即使您的函数是错误的你可能没有对用户可能将传递给它的参数的完全控制。  
  
## <a name="c-exceptions-versus-windows-seh-exceptions"></a>与 Windows SEH 异常的 C++ 异常  
 C 和 C++ 程序可使用结构化的异常处理 (SEH) 机制在 Windows 操作系统中。 中 SEH 的概念类似于 C++ 异常中的那些，只不过 SEH 使用`__try`， `__except`，和`__finally`构造而不是`try`和`catch`。 在 Visual C++，是为 SEH 实现 C++ 异常。 但是，当你编写 C++ 代码，使用 C++ 异常语法。  
  
 有关 SEH 的详细信息，请参阅[结构化异常处理 （C/C++）](../cpp/structured-exception-handling-c-cpp.md)。  
  
## <a name="exception-specifications-and-noexcept"></a>异常规范和 noexcept  
 异常规范的引入了 C++ 中作为一种方式指定函数可能引发的异常。 但是，异常规范事实证明，在实践中，有问题，以及 C++ 11 草稿标准中已弃用。 我们建议你不要使用异常规范除`throw()`，指示的函数使没有异常进行转义。 如果必须使用的类型的异常规范`throw(`*类型*`)`，请注意 Visual C++ 偏离以某些方式的标准。 有关详细信息，请参阅[异常规范 (throw)](../cpp/exception-specifications-throw-cpp.md)。 `noexcept`说明符在 C++ 11 中引入的首选替代作为`throw()`。  
  
## <a name="see-also"></a>请参阅  
 [如何： 异常和非异常代码之间的接口](../cpp/how-to-interface-between-exceptional-and-non-exceptional-code.md)   
 [欢迎回到 C++](../cpp/welcome-back-to-cpp-modern-cpp.md)   
 [C++ 语言参考](../cpp/cpp-language-reference.md)   
 [C++ 标准库](../standard-library/cpp-standard-library-reference.md)
