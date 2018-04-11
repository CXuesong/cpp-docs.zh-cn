---
title: "特殊成员函数 |Microsoft 文档"
ms.custom: 
ms.date: 12/06/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- special member functions [C++]
- constructors [C++]
- destructors [C++]
- copy operators [C++]
- move operators [C++]
- assignment operators [C++]
ms.assetid: 017d6817-b012-44f0-b153-f3076c251ea7
caps.latest.revision: "17"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: ff4fc72d2a40cc52ec614cbd5b470738ad1aa391
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="special-member-functions"></a>特殊成员函数  
  
*特殊成员函数*是类 （或结构），在某些情况下，编译器自动为您生成的成员函数。 这些函数是[默认构造函数](constructors-cpp.md#default_constructors)、[析构函数](destructors-cpp.md)、[复制构造函数和复制赋值运算符](copy-constructors-and-copy-assignment-operators-cpp.md)，和[移动构造函数和移动赋值运算符](move-constructors-and-move-assignment-operators-cpp.md)。 如果你的类未定义一个或多个特殊成员函数，然后编译器可隐式声明和定义使用的函数。 编译器生成实现调用*默认*特殊成员函数。 如果不需要编译器不生成函数。  
  
你可以显式声明的默认特殊成员函数，通过使用`= default`关键字。 这将导致编译器仅在需要时，相同的方式就像根本未声明该函数将函数定义。 

在某些情况下，编译器也会生成*删除*特殊成员函数，并不定义，因此不可调用。 这可能发生在情况下到上一个类的特定的特殊成员函数的调用毫无意义，给定类的其他属性。 若要显式防止自动生成特殊成员函数，你可以将其声明为已删除通过使用`= delete`关键字。  
  
编译器将生成*默认构造函数*，仅当你未声明任何其他构造函数时，才使用没有参数的构造函数。 如果你具有声明采用参数的构造函数，尝试调用默认构造函数的代码会导致编译器生成一条错误消息。 编译器生成的默认构造函数执行简单 member-wise[默认初始化](initializers.md#default_initialization)的对象。 默认初始化使处于不确定状态的所有成员变量。  
  
在默认析构函数执行的对象的 member-wise 析构。 仅当为虚拟基类析构函数，它是虚拟的。  
  
默认复制和移动构造和赋值操作执行识别成员的位模式复制或移动的非静态数据成员。 移动声明没有析构函数或移动或复制操作时，才会生成操作。 声明没有复制构造函数时，才会生成默认复制构造函数。 如果声明移动操作，隐式将删除它。 仅当显式声明没有复制赋值运算符时生成默认复制赋值运算符。 如果声明移动操作，隐式将删除它。  
  
## <a name="see-also"></a>请参阅  
[C++ 语言参考](cpp-language-reference.md)  



 
