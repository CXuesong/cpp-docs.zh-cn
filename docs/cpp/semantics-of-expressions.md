---
title: "表达式的语义 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- grammar, expressions
- expressions [C++], semantics
- expression evaluation
- expression evaluation, about expression evaluation
ms.assetid: 4a792154-533b-48b9-8709-31bfc170f0a7
caps.latest.revision: "7"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: efdf3f67e488af0e7c20c882552b18c533a031b7
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="semantics-of-expressions"></a>表达式的语义
表达式根据其运算符的优先级和分组来计算。 ([运算符优先级和结合性](../cpp/cpp-built-in-operators-precedence-and-associativity.md)中[词法约定](../cpp/lexical-conventions.md)，显示的关系 C++ 运算符对表达式实施。)  
  
## <a name="order-of-evaluation"></a>计算顺序  
 请看以下示例：  
  
```cpp  
// Order_of_Evaluation.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
int main()  
{  
    int a = 2, b = 4, c = 9;  
  
    cout << a + b * c << "\n";  
    cout << a + (b * c) << "\n";  
    cout << (a + b) * c << "\n";  
}  
```  
  
```Output  
38  
38  
54  
```  
  
 ![在表达式中的计算顺序](../cpp/media/vc38zv1.gif "vc38ZV1")  
表达式计算顺序  
  
 上图中显示的表达式的计算顺序取决于运算符的优先级和关联性：  
  
1.  乘法 (*) 在此表达式中具有最高优先级；因此子表达式 `b * c` 首先计算。  
  
2.  加法 (+) 具有第二高的优先级，因此，`a` 将与 `b` 和 `c` 的乘积相加。  
  
3.  左移 (<<) 在此表达式中具有最低的优先级，但有两个匹配项。 由于左移运算符从左到右分组，因此先计算左子表达式，再计算右子表达式。  
  
 当使用括号为子表达式分组时，它们将更改表达式的计算优先级和顺序，如下图所示。  
  
 ![带括号表达式的计算顺序](../cpp/media/vc38zv2.gif "vc38ZV2")  
带括号的表达式计算顺序  
  
 类似于上图的表达式的计算纯粹是为了展示副作用 - 在本例中是将信息转移到标准输出设备。  
  
## <a name="notation-in-expressions"></a>表达式中的表示法  
 在指定操作数时，C++ 语言指定某些兼容性。 下表显示的操作数的类型可接受到需要类型的操作数的运算符*类型*。  
  
### <a name="operand-types-acceptable-to-operators"></a>运算符可接受的操作数类型  
  
|应为类型|允许的类型|  
|-------------------|-------------------|  
|*type*|`const`*类型*<br /> `volatile`*类型*<br /> *类型*&<br /> `const`*类型*&<br /> `volatile`*类型*&<br /> `volatile const`*类型*<br /> `volatile const`*类型*&|  
|*类型*\*|*类型*\*<br /> `const`*类型*\*<br /> `volatile`*类型*\*<br /> `volatile const`*类型*\*|  
|`const`*类型*|*type*<br /> `const`*类型*<br />`const`*类型*&|  
|`volatile`*类型*|*type*<br /> `volatile`*类型*<br /> `volatile`*类型*&|  
  
 由于上述规则始终可以组合使用，因此，可以在指针所需的位置提供指向可变对象的 const 指针。  
  
## <a name="ambiguous-expressions"></a>不明确的表达式  
 某些表达式的意义不明确。 当在同一表达式中多次修改对象的值时，这些表达式最常见。 当语言没有定义表达式的计算顺序时，这些表达式依赖于特定的顺序计算。 请看下面的示例：  
  
```  
int i = 7;  
  
func( i, ++i );  
```  
  
 C++ 语言不保证计算函数调用的参数的顺序。 因此，在前面的示例中，`func` 的参数可以接受值 7 和 8 或 8 和 8，取决于参数是从左到右还是从右到左计算。  
  
## <a name="c-sequence-points-microsoft-specific"></a>C++ 序列点（Microsoft 专用）  
 在连续的“序列点”之间，表达式只能修改对象的值一次。  
  
 C++ 语言定义当前未指定序列点。 Microsoft C++ 对涉及 C 运算符但不涉及重载运算符的任何表达式使用与 ANSI C 相同的序列点。 当重载运算符时，语义从运算符排序更改为函数调用排序。 Microsoft C++ 使用以下序列点：  
  
-   逻辑“与”运算符 (&&) 的左操作数。 完全计算逻辑“与”运算符的左操作数，并在继续之前完成所有副作用。 不保证一定会计算逻辑“与”运算符的右操作数。  
  
-   逻辑 OR 运算符 (&#124; &#124;) 的左的操作数。 完全计算逻辑“或”运算符的左操作数，并在继续之前完成所有副作用。 不保证一定会计算逻辑“或”运算符的右操作数。  
  
-   逗号运算符的左操作数。 完全计算逗号运算符的左操作数，并在继续之前完成所有副作用。 始终计算逗号运算符的两个操作数。  
  
-   函数调用运算符。 计算函数调用表达式以及函数的所有参数（包括默认参数），并在进入函数之前完成所有副作用。 在参数或函数调用表达式之间没有指定的计算顺序。  
  
-   条件运算符的第一个操作数。 完全计算条件运算符的第一个操作数，并在继续之前完成所有副作用。  
  
-   完整的初始化表达式的末尾，如声明语句中的初始化的末尾。  
  
-   表达式语句中的表达式。 表达式语句由可选表达式后跟分号 (;) 组成。 表达式为其副作用完全计算。  
  
-   选择（if 或 switch）语句中的控制表达式。 完全计算该表达式，并在执行依赖于选择的代码之前完成所有副作用。  
  
-   while 或 do 语句的控制表达式。 完全计算该表达式，并在执行 while 或 do 循环的下一次迭代中的任何语句之前完成所有副作用。  
  
-   for 语句的所有三个表达式。 完全计算每个表达式，并在移动到下一个表达式之前完成所有副作用。  
  
-   return 语句中的表达式。 完全计算该表达式，并在控制权返回到调用函数之前完成所有副作用。  
  
## <a name="see-also"></a>请参阅  
 [表达式](../cpp/expressions-cpp.md)