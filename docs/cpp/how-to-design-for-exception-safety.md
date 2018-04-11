---
title: "如何： 设计异常安全性 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: article
dev_langs: C++
ms.assetid: 19ecc5d4-297d-4c4e-b4f3-4fccab890b3d
caps.latest.revision: "20"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 7d15df2f810848bb9349bc98c722ac02ff8cda17
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-design-for-exception-safety"></a>如何：设计异常安全性
异常机制的优势之一是执行以及异常相关数据将直接从引发异常的语句跳至处理异常的第一个 catch 语句。 处理程序可以是调用堆栈中任意数量的级别。 在 try 语句和 throw 语句之间调用的函数无需了解与所引发异常有关的任何信息。  但是，这些函数必须进行设计，以便它们在异常可能从下向上传播时“意外地”超出范围，而这样做不会留下部分创建的对象、泄漏的内存或处于不稳定状态的数据结构。  
  
## <a name="basic-techniques"></a>基本技术  
 可靠的异常处理策略需要细致的思考，并且应该是设计过程的一部分。 一般而言，大部分异常都是在软件模块的低层检测到和引发的，但这些层通常没有足够的上下文来处理错误或将消息显示给最终用户。 在中间层，函数可在必须检测异常对象时，或者它们具有要为最终捕获异常的上层提供的其他有用信息时，捕获和重新引发异常。 函数仅当能够完全恢复异常时才应捕获和“吞并”异常。 在大多数情况下，中间层的正确行为是让异常传播到调用堆栈。 甚至在最高层，如果未经处理的异常使某个程序处于无法保证其正确性的状态，则适当的操作可能是让该异常终止该程序。  
  
 无论函数如何处理异常，为了帮助确保函数是“异常安全的”，必须通过下列基本规则设计函数。  
  
### <a name="keep-resource-classes-simple"></a>保持资源类简单  
 当您将手动资源管理封装到类中时，请使用不会执行任何操作的类来管理资源；否则，您可能引入泄漏。 使用[智能指针](../cpp/smart-pointers-modern-cpp.md)如果可能，如下面的示例中所示。 对于突出显示使用 `shared_ptr` 时的差异，此示例是特意模拟的，非常简单。  
  
```cpp  
// old-style new/delete version  
class NDResourceClass {  
private:  
    int*   m_p;  
    float* m_q;  
public:  
    NDResourceClass() : m_p(0), m_q(0) {  
        m_p = new int;  
        m_q = new float;  
    }  
  
    ~NDResourceClass() {  
        delete m_p;  
        delete m_q;  
    }  
    // Potential leak! When a constructor emits an exception,   
    // the destructor will not be invoked.     
};  
  
// shared_ptr version  
#include <memory>  
  
using namespace std;  
  
class SPResourceClass {  
private:  
    shared_ptr<int> m_p;  
    shared_ptr<float> m_q;  
public:  
    SPResourceClass() : m_p(new int), m_q(new float) { }  
    // Implicitly defined dtor is OK for these members,   
    // shared_ptr will clean up and avoid leaks regardless.  
};  
  
// A more powerful case for shared_ptr  
  
class Shape {  
    // ...  
};  
  
class Circle : public Shape {  
    // ...  
};  
  
class Triangle : public Shape {  
    // ...  
};  
  
class SPShapeResourceClass {  
private:  
    shared_ptr<Shape> m_p;  
    shared_ptr<Shape> m_q;  
public:  
    SPShapeResourceClass() : m_p(new Circle), m_q(new Triangle) { }  
};  
  
```  
  
### <a name="use-the-raii-idiom-to-manage-resources"></a>使用 RAII 习语管理资源  
 若要是异常安全的，函数必须确保销毁它使用 `malloc` 或 `new` 分配的对象以及关闭或释放所有资源（如文件句柄），即使引发异常时也是如此。 *获取资源即初始化*(RAII) 习语使此类资源到自动变量的生存期的管理。 当函数超出范围时，要么正常返回；要么因为异常，调用所有完全构造的自动变量的析构函数。 RAII 包装器对象（如智能指针）将在其析构函数中调用合适的 delete 或 close 函数。 在异常安全的代码中，将每个资源的所有权立即传递给某种 RAII 对象至关重要。 请注意， `vector`， `string`， `make_shared`， `fstream`，和相似类处理为你获取资源。  但是，`unique_ptr`和传统`shared_ptr`构造有些特殊，因为资源获取由用户而不是对象; 因此，它们算作*资源释放是析构*但作为 RAII 可疑。  
  
## <a name="the-three-exception-guarantees"></a>三个异常保证  
 通常情况下，异常安全的一个函数可提供的三个异常保证讨论：*无故障保证*、*增强保证*，和*基本保证*.  
  
### <a name="no-fail-guarantee"></a>无故障保证  
 无故障（或“无引发”）保证是一个函数可提供的最有力的保证。 此保证声明，该函数将不会引发异常或允许异常传播。 但是，您无法可靠地提供此类包装，除非 (a) 您知道该函数调用的所有函数也是无故障的，或 (b) 您知道将在引发的所有异常到达该函数之前捕获这些异常，或者 (c) 您知道如何捕获和正确地处理可能到达该函数的所有异常。  
  
 增强保证和基本保证均依赖析构函数无故障这一假定。 标准库中的所有容器和类型保证其析构函数不会引发。 还有一个相反的要求：标准库要求为其提供的用户定义的类型，例如，作为模板参数，必须具有未引发的析构函数。  
  
### <a name="strong-guarantee"></a>增强保证  
 增强保证声明，如果函数因异常超出范围，则将不会泄漏内存并且不会修改程序状态。 一个提供增强保证的函数主要是一个提交或回滚语义的事务：它要么完全成功，要么无任何效果。  
  
### <a name="basic-guarantee"></a>基本保证  
 基本保证是三个保证是最弱的一个。 但是，当增强保证对于内存消耗或性能来说很昂贵时，此保证可能是最佳选择。 基本保证声明，如果出现异常，内存不会泄漏并且对象将仍处于可用状态，即使可能已修改数据仍是如此。  
  
## <a name="exception-safe-classes"></a>异常安全类  
 类可帮助确保其自己的异常安全，方式为防止自身进行部分构造或部分销毁，即使它由不安全的函数使用也是如此。 如果类构造函数在完成之前就存在，则绝不会创建对象，并且绝不会调用其析构函数。 虽然在异常之前已初始化的自动变量将调用其析构函数，但智能指针或类似自动变量未管理的自动分配的内存或资源将泄漏。  
  
 内置类型均是无故障的，标准库类型支持最低级别的基本保证。 为必须是异常安全的任何用户定义类型遵循这些准则：  
  
-   使用智能指针或其他 RAII 类型的包装器管理所有资源。 在类析构函数中避免资源管理功能，因为如果构造函数引发异常，则将不会调用析构函数。 但是，如果类是仅控制一个资源的专用资源管理器，则可接受使用析构函数管理资源。  
  
-   了解无法在派生类构造函数中吞并基类构造函数中引发的异常。 如果要转换并再次引发派生构造函数中的基类异常，请使用 try 函数块。   
  
-   考虑是否将所有类状态存储在包装在一个智能指针中的数据成员中，尤其是在类具有“允许失败的初始化”概念时。 虽然 C++ 允许未初始化的数据成员，但它不支持未初始化或部分初始化的类实例。 构造函数必须成功或失败；如果构造函数未完成运行，则将不会创建任何对象。  
  
-   不允许任何异常从析构函数转义。 C++ 的基本原理是，析构函数绝不会允许异常传播到调用堆栈。 如果析构函数必须执行潜在引发异常的操作，则它必须使用 try catch 块如此做并吞并异常。 标准库将为其定义的所有析构函数提供此保证。  
  
## <a name="see-also"></a>请参阅  
 [错误和异常处理](../cpp/errors-and-exception-handling-modern-cpp.md)   
 [如何：异常和非异常代码之间的接口](../cpp/how-to-interface-between-exceptional-and-non-exceptional-code.md)