---
title: "复制构造函数和复制赋值运算符 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- = operator [C++], copying objects
- assignment statements [C++], copying objects
- assignment operators [C++], for copying objects
- objects [C++], copying
- initializing objects, by copying objects
- copying objects
- assigning values to copy objects
ms.assetid: a94fe1f9-0289-4fb9-8633-77c654002c0d
caps.latest.revision: "12"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 1f2ebf51f28912c3cb798acc1ff4aa377c1bebb5
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="copy-constructors-and-copy-assignment-operators-c"></a>复制构造函数和复制赋值运算符 (C++)
> [!NOTE]
>  从 C++ 11 开始，两种类型的分配支持的语言：*复制分配*和*移动赋值*。 在本文中，“赋值”意味着复制赋值，除非有其他显式声明。 有关移动分配的信息，请参阅[移动构造函数和移动赋值运算符 （C++）](http://msdn.microsoft.com/en-us/1442de5f-37a5-42a1-83a6-ec9cfe0414db)。  
>   
>  赋值操作和初始化操作都会导致对象被复制。  
  
-   **分配**： 如果一个对象的值分配给另一个对象，第一个对象将复制到第二个对象。 因此，  
  
    ```cpp  
    Point a, b;  
    ...  
    a = b;  
    ```  
  
     导致 `b` 的值被复制到 `a` 中。  
  
-   **初始化**： 声明新对象、 参数传递给函数的值，或当值从函数返回值时，便会初始化。  
  
 您可以为类类型的对象定义“复制”的语义。 例如，考虑此代码：  
  
```cpp  
TextFile a, b;  
a.Open( "FILE1.DAT" );  
b.Open( "FILE2.DAT" );  
b = a;  
```  
  
 前面的代码可能表示“将 FILE1.DAT 的内容复制到 FILE2.DAT”，也可能表示“忽略 FILE2.DAT 并使 `b` 成为 FILE1.DAT 的另一个句柄”。 您必须将适当的复制语义附加到每个类，如下所示。  
  
-   通过将赋值运算符 `operator=` 与对类类型的引用一起用作返回类型和 `const` 引用所传递的参数（例如，`ClassName& operator=(const ClassName& x);`）。  
  
-   通过通过复制构造函数。   
  
 如果不声明复制构造函数，编译器将为你生成 member-wise 复制构造函数。  如果不声明复制赋值运算符，编译器将为你生成 member-wise 复制赋值运算符。 声明复制构造函数不会取消编译器生成的复制赋值运算符，反之亦然。 如果实现上述其中一项，建议你还实现另一项以使代码的含义变得明确。  
   
 复制构造函数采用类型的参数*类名***&**，其中*类名*是为其定义构造函数的类的名称。 例如:  
  
```cpp  
// spec1_copying_class_objects.cpp  
class Window  
{  
public:  
    Window( const Window& ); // Declare copy constructor.  
    // ...  
};  
  
int main()  
{  
}  
```  
  
> [!NOTE]
>  请复制构造函数的参数的类型*常量的类名称* **&** 尽可能。 这可防止复制构造函数意外更改从中复制它的对象。 它还允许将从复制**const**对象。  
  
## <a name="compiler-generated-copy-constructors"></a>编译器生成的构造函数  
 编译器生成的复制构造函数，如用户定义的复制构造函数具有单个参数的类型"引用*类名*。" 例外情况是当所有基类和成员类都具有声明为采用单个参数的类型的复制构造函数**const** *类名***&**。 在这种情况下，编译器生成的复制构造函数的参数也是**const**。  
  
 当复制构造函数参数类型不是**const**，通过复制初始化**const**对象生成一个错误。 反之则不然： 如果参数是**const**，可以通过复制不是一个对象初始化**const**。  
  
 编译器生成的赋值运算符遵循关于相同的模式**const。** 它们将采用类型的单个参数*类名* **&** 除非所有基类和成员类中的赋值运算符采用参数类型的**const** *类名称 （& a)。* 在这种情况下，类的生成的赋值运算符采用**const**参数。  
  
> [!NOTE]
>  当虚拟基类由复制构造函数（编译器生成或用户定义的）初始化时，将只初始化这些基类一次：在构造它们时。  
  
 含义类似于复制构造函数的含义。 当参数类型不是**const**，从分配**const**对象生成一个错误。 反之则不然： 如果**const**值分配给一个值，不是**const**，则赋值能成功。  
  
 有关重载的赋值运算符的详细信息，请参阅[分配](../cpp/assignment.md)。  
  
