---
title: "用户定义的类型转换 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: explicit_cpp
dev_langs: C++
helpviewer_keywords:
- constructors [C++], and constants
- conversion functions [C++]
- explicit keyword [C++]
- type conversion
- constructors [C++], drawbacks
- conversion constructors
- type conversion [C++], explicit conversion
- coercion [C++]
- conversions [C++], explicit
- objects [C++], converting
- conversion functions [C++], rules for declaring
- declaring functions [C++], conversion functions
- functions [C++], conversion
- converting objects
- constructors [C++], conversion
- conversions [C++], by constructors
- data type conversion [C++], explicit
ms.assetid: d40e4310-a190-4e95-a34c-22c5c20aa0b9
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 561730527a215d5314f7239affc764d9f5925f67
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="user-defined-type-conversions-c"></a>用户定义的类型转换 (C++)
A*转换*生成一个值从某种类型的不同类型的新值。 *标准转换*内置于其内置类型，你可以创建的 C++ 语言和支持*用户定义的转换*来执行到、 从，或用户定义类型之间的转换。  
  
 标准转换执行内置类型之间的转换、通过继承相关联的类型的指针或引用之间的转换、与 void 指针的双向转换以及到 null 指针的转换。 有关详细信息，请参阅[标准转换](../cpp/standard-conversions.md)。 用户定义的转换执行用户定义的类型之间的转换，或者执行用户定义的类型和内置类型之间的转换。 你可以实现它们作为[转换构造函数](#ConvCTOR)或[转换函数](#ConvFunc)。  
  
 转换可以是显式（当程序员通过调用从一个类型转换为另一个类型时，例如强制转换或直接初始化的情况），也可以是隐式（当语言或程序调用其他类型而非程序员给定的类型时）。  
  
 在以下情况下尝试隐式转换：  
  
-   提供给函数的参数与匹配参数的类型不同。  
  
-   从函数返回的值与函数返回类型的类型不同。  
  
-   初始值表达式与其初始化的对象的类型不同。  
  
-   用于控制条件语句、循环构造或切换的表达式不具有对其进行控制时所需的结果类型。  
  
-   提供给运算符的操作数与匹配的操作数参数的类型不同。 对于内置运算符，这两个操作数的类型必须相同，并且要转换为可表示它们的常规类型。 有关详细信息，请参阅[标准转换](standard-conversions.md)。 对于用户定义的运算符，每个操作数都必须与匹配的操作数参数的类型相同。  
  
 当一个标准转换无法完成隐式转换时，编译器可以使用用户定义的转换（可选择随后使用其他标准转换）来完成此操作。  
  
 当转换站点提供两个或多个用户定义的用于执行相同转换的转换时，该转换将被视为不明确。 这种不明确性是一个错误，因为编译器无法确定应选择哪一个可用转换。 但若只是定义执行相同转换的多种方式，则它不是一个错误，因为可用的转换集在源代码中的不同位置可能不同，例如，可取决于源文件中所包含的头文件。 只要转换站点只提供一个转换，就不会出现不明确性。 多种方式都会导致出现不明确转换，但最常见的方式如下：  
  
-   多重继承。 该转换在多个基类中定义。 
  
-   不明确的函数调用。 该转换定义为目标类型的转换构造函数以及源类型的转换函数。 有关详细信息，请参阅[转换函数](#ConvFunc)。  
  
 通常只需通过更全面地限定涉及类型的名称或执行显式强制转换来阐明你的意图，即可解决不明确性。  
  
 转换构造函数和转换函数都遵循成员访问控制规则，但仅当可以确定明确的转换时才考虑这些转换的可访问性。 这意味着，即使竞争的转换的访问级别会阻止使用该转换，该转换也可能具有不明确性。 有关成员可访问性的详细信息，请参阅[成员访问控制](../cpp/member-access-control-cpp.md)。  
  
## <a name="the-explicit-keyword-and-problems-with-implicit-conversion"></a>explicit 关键字和有关隐式转换的问题  
 默认情况下，当你创建用户定义的转换时，编译器可使用它来执行隐式转换。 有时这是你需要进行的操作，但另一些时候用于指导编译器进行隐式转换的简单规则会使其接受你不希望接受的代码。  
  
 会导致问题的隐式转换的一个已知示例为：向 `bool` 的转换。 你可能出于很多原因要创建可用于布尔上下文的类的类型（例如，为了使其可用于控制 `if` 语句或循环），但在编译器执行向内置类型的用户定义的转换时，该编译器在之后可以应用其他标准转换。 此附加标准转换的目的是允许执行类似于从 `short` 提升到 `int` 的操作，但它也允许进行不太明显的转换（例如，从 `bool` 到 `int` 的转换），这使类的类型可用于你预期之外的整数上下文。 此特定问题称为*安全 Bool 问题*。 `explicit` 关键字有助于解决这种问题。  
  
 `explicit` 关键字会通知编译器指定的转换不能用于执行隐式转换。 如果要在引入 `explicit` 关键字之前享受隐式转换在语法方面带来的便利，则必须接受隐式转换有时候造成的意外后果，或使用较不为方便的已命名转换函数作为解决方法。 现在使用 `explicit` 关键字，你可以创建简便的转换，它们只能用于执行显式强制转换或直接初始化，并且不会导致“安全 Bool 问题”中举例说明的问题类型。  
  
 `explicit` 关键字可应用于自 C++98 后的转换构造函数和自 C++11 后的转换函数。 以下部分包含有关如何使用 `explicit` 关键字的详细信息。  
  
##  <a name="ConvCTOR"></a>转换构造函数  
 转换构造函数定义了从用户定义的类型或内置类型到用户定义的类型的转换。 以下示例演示了从内置类型 `Money``double` 转换为用户定义的类型  的转换构造函数。  
  
```  
#include <iostream>  
  
class Money  
{  
public:  
    Money() : amount{ 0.0 } {};  
    Money(double _amount) : amount{ _amount } {};  
  
    double amount;  
};  
  
void display_balance(const Money balance)  
{  
    std::cout << "The balance is: " << balance.amount << std::endl;  
}  
  
int main(int argc, char* argv[])  
{  
    Money payable{ 79.99 };  
  
    display_balance(payable);  
    display_balance(49.95);  
    display_balance(9.99f);  
  
    return 0;  
}  
```  
  
 请注意，对函数 `display_balance` 的首次调用（它将采用类型 `Money` 的参数）不需要转换，因为它的参数类型正确。 但是，在第二次调用`display_balance`，需要使用转换，因为参数类型`double`值为`49.95`，是不什么函数的要求。 该函数不能直接使用此值，但是由于存在从参数类型 (`double`) 到匹配的参数类型 (`Money`) 的转换，因此类型 `Money` 的临时值将通过该参数进行构造并将用于完成该函数调用。 在第三个调用`display_balance`，请注意，该参数不`double`，而是`float`值为`9.99`-尚未函数调用可以仍完成，因为编译器可以执行的标准转换和 — 在这种情况下从`float`到`double`-然后执行从用户定义的转换`double`到`Money`完成必要的转换。  
  
### <a name="declaring-conversion-constructors"></a>声明转换构造函数  
 以下规则适用于声明转换构造函数：  
  
-   转换的目标类型是要构造的用户定义的类型。  
  
-   转换构造函数通常仅采用一个参数，它为源类型。 但是，当每个附加参数都具有默认值时，转换构造函数可指定这些附加参数。 源类型保留了第一个参数的类型。  
  
-   与所有构造函数一样，转换构造函数不指定返回类型。 在声明中指定返回类型是一个错误。  
  
-   转换构造函数可以为显式。  
  
### <a name="explicit-conversion-constructors"></a>显式转换构造函数  
 通过将转换构造函数声明为 `explicit`，它只能用于执行对象的直接初始化或执行显式强制转换。 这将阻止接受类类型的参数的函数同样隐式接受转换构造函数的源类型的参数，并且将阻止从该源类型的值复制初始化类的类型。 以下示例演示了如何定义显式转换构造函数，以及它对哪个代码格式正确所产生的影响。  
  
```  
#include <iostream>  
  
class Money  
{  
public:  
    Money() : amount{ 0.0 } {};  
    explicit Money(double _amount) : amount{ _amount } {};  
  
    double amount;  
};  
  
void display_balance(const Money balance)  
{  
    std::cout << "The balance is: " << balance.amount << std::endl;  
}  
  
int main(int argc, char* argv[])  
{  
    Money payable{ 79.99 };        // Legal: direct initialization is explicit.  
  
    display_balance(payable);      // Legal: no conversion required  
    display_balance(49.95);        // Error: no suitable conversion exists to convert from double to Money.  
    display_balance((Money)9.99f); // Legal: explicit cast to Money  
  
    return 0;  
}  
```  
  
 在此示例中，请注意，你可以继续使用显示转换构造函数来执行 `payable` 的直接初始化。 如果你要改为复制初始化 `Money payable = 79.99;`，这将是一个错误操作。 对 `display_balance` 的首次调用不受影响，因为参数的类型正确。 对 `display_balance` 的第二次调用是一个错误，因为转换构造函数无法用于执行隐式转换。 由于到 `display_balance` 的显式强制转换，因此对 `Money` 的第三次调用合法，但请注意，编译器仍然已通过插入从 `float` 到 `double` 的隐式强制转换来帮助完成该强制转换。  
  
 尽管允许隐式转换带来的便利很具吸引力，但执行此操作会引入难以发现的 Bug。 根据经验，应该让所有转换构造函数变为显式，除非你确定希望隐式执行特定转换。  
  
##  <a name="ConvFunc"></a>转换函数  
 转换函数定义了从用户定义的类型到其他类型的转换。 这些函数有时称为“强制转换运算符”，因为在值强制转换为其他类型时将随转换构造函数一起调用它们。 以下示例演示了从用户定义的类型 `Money` 转换为内置类型 `double` 的转换函数：  
  
```  
#include <iostream>  
  
class Money  
{  
public:  
    Money() : amount{ 0.0 } {};  
    Money(double _amount) : amount{ _amount } {};  
  
    operator double() const { return amount; }  
private:  
    double amount;  
};  
  
void display_balance(const Money balance)  
{  
    std::cout << "The balance is: " << balance << std::endl;  
}  
  
```  
  
 请注意，成员变量 `amount` 设置为 private，并且到类型 `double` 的公共转换函数的引入目的只是为了返回 `amount` 的值。 在函数 `display_balance` 中，当 `balance` 的值通过使用流插入运算符 `<<` 流入标准输出时，将执行隐式转换。 由于没有为用户定义的类型 `Money` 定义任何流插入运算符，但存在一个适用于内置类型 `double` 的流插入运算符，因此编译器可以使用从 `Money` 到 `double` 的转换函数来满足此流插入运算符。  
  
 转换函数通过派生类继承。 派生类中的转换函数仅在其转换为完全相同的类型时覆盖继承的转换函数。 例如，派生类的用户定义的转换函数 `operator int` 不会覆盖（甚至不会影响）基类的用户定义的转换函数 `operator short`，即使标准转换定义了 `int` 和 `short` 之间的转换关系也是如此。  
  
### <a name="declaring-conversion-functions"></a>声明转换函数  
 以下规则适用于声明转换函数：  
  
-   必须在声明转换函数之前先声明转换的目标类型。 无法在转换函数的声明中声明类、结构、枚举和 typedef。  
  
    ```  
    operator struct String { char string_storage; }() // illegal  
    ```  
  
-   转换函数不采用参数。 在声明中指定任何参数是一个错误操作。  
  
-   转换函数具有由转换函数名称（它也是转换的目标类型的名称）指定的返回类型。 在声明中指定返回类型是一个错误。  
  
-   转换函数可以是虚函数。  
  
-   转换函数可以是显式函数。  
  
### <a name="explicit-conversion-functions"></a>显式转换函数  
 当转换函数声明为显式函数时，它只能用于执行显式强制转换。 这还将阻止接受转换函数目标类型的参数的函数同样隐式接受类类型的参数，并且将阻止从该类类型的值复制初始化目标类型的实例。 以下示例演示了如何定义显式转换函数，以及它对哪个代码格式正确所产生的影响。  
  
```  
#include <iostream>  
  
class Money  
{  
public:  
    Money() : amount{ 0.0 } {};  
    Money(double _amount) : amount{ _amount } {};  
  
    explicit operator double() const { return amount; }  
private:  
    double amount;  
};  
  
void display_balance(const Money balance)  
{  
    std::cout << "The balance is: " << (double)balance << std::endl;  
}  
  
```  
  
 此处，转换函数 `operator double` 已声明为显式，并且已在函数 `double` 中引入对类型 `display_balance` 的显式强制转换以执行转换。 若已省略此强制转换，则编译器将无法定位适用于类型 `<<` 的相应流插入运算符 `Money`，并且将发生错误。  
  
