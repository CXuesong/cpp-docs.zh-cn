---
title: "Catch 块的计算 （C++） 的方式 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- try-catch keyword [C++], catchable types
- catch keyword [C++], types of catch handlers
- C++ exception handling, catch handlers
- exception handling, catching and deleting exceptions
- types [C++], exception handling
ms.assetid: 202dbf07-8ace-4b3b-b3ae-4b45c275e0b4
caps.latest.revision: "7"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 744f75f86fd7d3e2ca2a2545a7914f923c4454b7
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="how-catch-blocks-are-evaluated-c"></a>Catch 块的计算方式 (C++)
虽然通常建议您引发派生自 std::exception 的类型，但 C++ 使您能够引发任何类型的异常。 可由捕获 C++ 异常**捕获**指定相同的类型，为所引发的异常，或可以捕获任何类型的异常处理程序的处理程序。  
  
 如果引发的异常的类型是类，它还具有基类（或类），则它可由接受异常类型的基类和对异常类型的基的引用的处理程序捕获。 请注意，当异常由引用捕获时，会将其绑定到实际引发的异常对象；否则，它将为一个副本（与函数的参数大致相同）。  
  
 当引发异常时，将它由以下类型的中捕获**捕获**处理程序：  
  
-   可以接受任何类型的处理程序（使用省略号语法）。  
  
-   接受与异常对象中; 相同的类型的处理程序因为它是副本， **const**和`volatile`修饰符将被忽略。  
  
-   接受对与异常对象相同的类型的引用的处理程序。  
  
-   接受对引用的处理程序**const**或`volatile`形式与异常对象相同的类型。  
  
-   接受与异常对象中; 相同的类型的基类的处理程序由于它是副本， **const**和`volatile`修饰符将被忽略。 **捕获**的基类的处理程序必须位于**捕获**为派生的类处理程序。  
  
-   接受对与异常对象相同的类型的基类的引用的处理程序。  
  
-   接受对引用的处理程序**const**或`volatile`窗体的与异常对象相同的类型的基类。  
  
-   接受可通过标准指针转换规则将引发的指针对象转换为的指针的处理程序。  
  
 顺序**捕获**处理程序显示很重要，因为处理程序给定**重**块检查其出现的顺序。 例如，将基类的处理程序放置在派生类的处理程序的前面是错误的。   匹配后**捕获**找到处理程序，不会检查后续处理程序。 因此，省略号**捕获**处理程序必须是最后一个处理程序其**重**块。 例如:  
  
```  
// ...  
try  
{  
    // ...  
}  
catch( ... )  
{  
    // Handle exception here.  
}  
// Error: the next two handlers are never examined.  
catch( const char * str )  
{  
    cout << "Caught exception: " << str << endl;  
}  
catch( CExcptClass E )  
{  
    // Handle CExcptClass exception here.  
}  
```  
  
 在此示例中，省略号**捕获**处理程序的唯一处理程序都要检查。  
  
## <a name="see-also"></a>请参阅  
 [C++ 异常处理](../cpp/cpp-exception-handling.md)