---
title: "构造函数 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- constructors [C++]
- objects [C++], creating
- instance constructors
ms.assetid: 3e9f7211-313a-4a92-9584-337452e061a9
caps.latest.revision: "17"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 57854ec15d3104d80e8dbba68ebc33937222172f
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="constructors-c"></a>构造函数 (C++)
构造函数是一种可初始化其类的实例的成员函数。 构造函数具有与类相同的名称，没有返回值。 构造函数可以具有任意数量的参数，类可以具有任意数量的重载构造函数。 构造函数可以具有任何可访问性（公共、受保护或私有）。 如果未定义任何构造函数，则编译器会生成不采用任何参数的默认构造函数；可以通过将默认构造函数声明为已删除来重写此行为。  
  
## <a name="in-this-topic"></a>在本主题中  
  
-   [构造函数顺序](#order_of_construction)  
  
-   [成员列表](#member_lists)  
  
-   [显式构造函数](#explicit_constructors)  
  
-   [默认构造函数](#default_constructors)  
  
-   [复制和移动构造函数](#copy_and_move_constructors)  
  
-   [显式默认和已删除构造函数](#explicitly_defaulted_and_deleted_constructors)  
  
-   [在派生类中的构造函数](#constructors_in_derived_classes)  
  
-   [在构造函数中的虚函数](#virtual_functions_in_constructors)  
  
-   [构造函数和复合类](#constructors_in_composite_classes)  
  
-   [委托构造函数](#delegating_constructors)  
  
-   [继承构造函数 (C++ 11)](#inheriting_constructors)  
  
-   [声明构造函数的规则](#rules_for_declaring_constructors)  
  
-   [显式调用构造函数](#explicitly_invoking_constructors)  
  
##  <a name="order_of_construction"></a>构造函数顺序  
 构造函数按此顺序执行工作：  
  
1.  按声明顺序调用基类和成员构造函数。  
  
2.  如果类派生自虚拟基类，则会将对象的虚拟基指针初始化。  
  
3.  如果类具有或继承了虚函数，则会将对象的虚函数指针初始化。 虚函数指针指向类中的虚函数表，确保虚函数正确地调用绑定代码。  
  
4.  它执行自己函数体中的所有代码。  
  
 下面的示例显示，在派生类的构造函数中，基类和成员构造函数的调用顺序。 首先，调用基构造函数，然后按照基类成员在类声明中出现的顺序对这些成员进行初始化，然后，调用派生构造函数。  
  
```cpp  
#include <iostream>  
using namespace std;  
  
class Contained1 {  
public:  
    Contained1() {  
        cout << "Contained1 constructor." << endl;  
    }  
};  
  
class Contained2 {  
public:  
    Contained2() {  
        cout << "Contained2 constructor." << endl;  
    }  
};  
  
class Contained3 {  
public:  
    Contained3() {  
        cout << "Contained3 constructor." << endl;  
    }  
};  
  
class BaseContainer {  
public:  
    BaseContainer() {  
        cout << "BaseContainer constructor." << endl;  
    }  
private:  
    Contained1 c1;  
    Contained2 c2;  
};  
  
class DerivedContainer : public BaseContainer {  
public:  
    DerivedContainer() : BaseContainer() {  
        cout << "DerivedContainer constructor." << endl;  
    }  
private:  
    Contained3 c3;  
};  
  
int main() {  
    DerivedContainer dc;  
    int x = 3;  
}  
  
```  
  
 这是输出：  
  
```  
Contained1 constructor.  
Contained2 constructor.  
BaseContainer constructor.  
Contained3 constructor.  
DerivedContainer constructor.  
```  
  
 如果构造函数引发异常，析构的顺序与构造的顺序相反：  
  
1.  构造函数主体中的代码将展开。  
  
2.  基类和成员对象将被销毁，顺序与声明顺序相反。  
  
3.  如果是非委托构造函数，所有完全构造的基类对象和成员均将被销毁。 但是，对象本身不是完全构造的，因此析构函数不会运行。  
  
##  <a name="member_lists"></a>成员列表  
 使用成员初始值设定项列表从构造函数参数初始化类成员。 此方法使用*直接初始化*，这是比使用构造函数体内的赋值运算符更高效。  
  
```cpp  
class Box {  
public:  
    Box(int width, int length, int height)   
        : m_width(width), m_length(length), m_height(height) // member init list  
    {}  
    int Volume() {return m_width * m_length * m_height; }  
private:  
    int m_width;  
    int m_length;  
    int m_height;  
  
};  
  
```  
  
 创建 Box 对象：  
  
```  
Box b(42, 21, 12);  
cout << "The volume is " << b.Volume();  
```  
  
##  <a name="explicit_constructors"></a>显式构造函数  
 如果类具有带一个参数的构造函数，或是如果除了一个参数之外的所有参数都具有默认值，则参数类型可以隐式转换为类类型。 例如，如果 `Box` 类具有一个类似于下面这样的构造函数：  
  
```  
Box(int size): m_width(size), m_length(size), m_height(size){}  
```  
  
 可以初始化 Box，如下所示：  
  
```  
Box b = 42;  
```  
  
 或将一个 int 传递给采用 Box 的函数：  
  
```  
class ShippingOrder  
{  
public:  
    ShippingOrder(Box b, double postage) : m_box(b), m_postage(postage){}  
  
private:  
    Box m_box;  
    double m_postage;  
}  
//elsewhere...  
    ShippingOrder so(42, 10.8);  
  
```  
  
 这类转换可能在某些情况下很有用，但更常见的是，它们可能会导致代码中发生细微但严重的错误。 作为一般规则，应对构造函数使用 `explicit` 关键字（和用户定义的运算符）以防止出现这种隐式类型转换：  
  
```  
  
explicit Box(int size): m_width(size), m_length(size), m_height(size){}  
```  
  
 构造函数是显式函数时，此行会导致编译器错误：`ShippingOrder so(42, 10.8);`。  有关详细信息，请参阅[用户定义类型转换](../cpp/user-defined-type-conversions-cpp.md)。  
  
##  <a name="default_constructors"></a>默认构造函数  
 *默认构造函数*没有参数; 它们遵循略有不同的规则：  
  
 默认构造函数是之一*特殊成员函数*; 如果没有构造函数中声明在类中，则编译器会提供默认构造函数：  
  
```cpp  
class Box {  
    Box(int width, int length, int height)   
        : m_width(width), m_length(length), m_height(height){}  
};  
  
int main(){  
  
    Box box1{}; // call compiler-generated default ctor  
    Box box2;   // call compiler-generated default ctor  
}  
```  
  
 当你调用默认构造函数并尝试使用括号时，系统将发出警告：  
  
```cpp  
class myclass{};  
int main(){  
myclass mc();     // warning C4930: prototyped function not called (was a variable definition intended?)  
}  
```  
  
 这是“最棘手的解析”问题的示例。 这种示例表达式既可以解释为函数的声明，也可以解释为对默认构造函数的调用，而且 C++ 分析器更偏向于声明，因此表达式会被视为函数声明。 有关详细信息，请参阅[最棘手的解析](http://en.wikipedia.org/wiki/Most_vexing_parse)。  
  
 如果声明了任何非默认构造函数，编译器不会提供默认构造函数：  
  
```cpp  
class Box {  
    Box(int width, int length, int height)   
        : m_width(width), m_length(length), m_height(height){}  
};  
private:  
    int m_width;  
    int m_length;  
    int m_height;  
  
};  
  
int main(){  
  
    Box box1(1, 2, 3);  
    Box box2{ 2, 3, 4 };  
    Box box4;     // compiler error C2512: no appropriate default constructor available  
}  
  
```  
  
 如果类没有默认构造函数，将无法通过单独使用方括号语法来构造该类的对象数组。 例如，在前面提到的代码块中，框的数组无法进行如下声明：  
  
```cpp  
Box boxes[3];    // compiler error C2512: no appropriate default constructor available  
  
```  
  
 但是，你可以使用初始值设定项列表将框的数组初始化：  
  
```cpp  
Box boxes[3]{ { 1, 2, 3 }, { 4, 5, 6 }, { 7, 8, 9 } };  
```  
  
##  <a name="copy_and_move_constructors"></a>复制和移动构造函数  
 A*复制构造函数*是特殊成员函数，将作为输入的相同对象的引用类型，并使它的一个副本。 有关详细信息，请参阅[复制构造函数和复制赋值运算符 （C++）](../cpp/copy-constructors-and-copy-assignment-operators-cpp.md)。 A*移动构造函数*也是将现有对象的数据的所有权移动到新变量，而不复制原始数据的特殊成员函数。 有关详细信息，请参阅[移动构造函数和移动赋值运算符 （C++）](../cpp/move-constructors-and-move-assignment-operators-cpp.md)。  
  
##  <a name="explicitly_defaulted_and_deleted_constructors"></a>显式默认和已删除构造函数  
 你可以显式*默认*复制构造函数、 默认构造函数、 移动构造函数、 复制赋值运算符、 移动赋值运算符和析构函数。 你可以显式*删除*所有特殊成员函数。 有关详细信息，请参阅[显式默认函数和已删除的函数](../cpp/explicitly-defaulted-and-deleted-functions.md)。  
  
##  <a name="constructors_in_derived_classes"></a>在派生类中的构造函数  
 派生类构造函数始终调用基类构造函数，因此，在完成任何额外任务之前，它可以依赖于完全构造的基类。 调用基类构造函数进行派生，例如，如果 ClassA 派生自 ClassB，ClassB 派生自 ClassC，那么首先调用 ClassC 构造函数，然后调用 ClassB 构造函数，最后调用 ClassA 构造函数。  
  
 如果基类没有默认构造函数，则必须在派生类构造函数中提供基类构造函数参数：  
  
```cpp  
class Box {  
public:  
    Box(int width, int length, int height){  
       m_width = width;  
       m_length = length;  
       m_height = height;  
    }  
  
private:  
    int m_width;  
    int m_length;  
    int m_height;  
};  
  
class StorageBox : public Box {  
public:  
    StorageBox(int width, int length, int height, const string label&) : Box(width, length, height){  
        m_label = label;  
    }  
private:  
    string m_label;  
};  
  
int main(){  
  
    const string aLabel = "aLabel";  
    StorageBox sb(1, 2, 3, aLabel);  
}   
```  
  
### <a name="constructors-for-classes-that-have-multiple-inheritance"></a>具有多重继承的类的构造函数  
 如果类从多个基类派生，那么将按照派生类声明中列出的顺序调用基类构造函数：  
  
```cpp  
#include <iostream>  
using namespace std;  
  
class BaseClass1 {  
public:  
    BaseClass1() {  
        cout << "BaseClass1 constructor." << endl;  
    }  
};  
class BaseClass2 {  
public:  
    BaseClass2() {  
        cout << "BaseClass2 constructor." << endl;  
    }  
};  
class BaseClass3{  
public:  
    BaseClass3() {  
        cout << "BaseClass3 constructor." << endl;  
    }  
};  
class DerivedClass : public BaseClass1, public BaseClass2, public BaseClass3  {  
public:  
    DerivedClass() {  
        cout << "DerivedClass constructor." << endl;  
    }  
};  
  
int main() {  
    DerivedClass dc;  
}  
  
```  
  
 你应看到以下输出：  
  
```  
BaseClass1 constructor.  
BaseClass2 constructor.  
BaseClass3 constructor.  
DerivedClass constructor.  
```  
  
##  <a name="virtual_functions_in_constructors"></a>在构造函数中的虚函数  
 我们建议你谨慎调用构造函数中的虚函数。 基类构造函数始终在派生类构造函数之前调用，因此基构造函数中调用的函数是基类版本，而非派生类版本。 在下面的示例中，构造 `DerivedClass` 会导致执行 `BaseClass` 的 `print_it()` 实现早于 `DerivedClass` 构造函数导致执行 `DerivedClass` 的 `print_it()` 实现：  
  
```cpp  
#include <iostream>  
using namespace std;  
  
class BaseClass{  
public:  
    BaseClass(){  
        print_it();  
    }  
    virtual void print_it() {  
        cout << "BaseClass print_it" << endl;  
    }  
};  
  
class DerivedClass : public BaseClass {  
public:  
    DerivedClass() {  
        print_it();  
    }  
    virtual void print_it(){  
        cout << "Derived Class print_it" << endl;  
    }  
};  
  
int main() {  
  
    DerivedClass dc;  
}  
```  
  
 这是输出：  
  
```  
BaseClass print_it  
Derived Class print_it  
```  
  
##  <a name="constructors_in_composite_classes"></a>构造函数和复合类  
 包含类类型成员的类称为*复合类*。 创建复合类的类类型成员时，调用类自己的构造函数之前，先调用构造函数。 当包含的类没有默认构造函数是，必须使用复合类构造函数中的初始化列表。 在之前的 `StorageBox` 示例中，如果将 `m_label` 成员变量的类型更改为新的 `Label` 类，则必须调用基类构造函数，并且将 `m_label` 变量（位于 `StorageBox` 构造函数中）初始化：  
  
```cpp  
class Label {  
public:  
    Label(const string& name, const string& address) { m_name = name; m_address = address; }  
    string m_name;  
    string m_address;  
};  
  
class StorageBox : public Box {  
public:  
    StorageBox(int width, int length, int height, Label label)   
        : Box(width, length, height), m_label(label){}  
private:  
    Label m_label;  
};  
  
int main(){  
// passing a named Label  
    Label label1{ "some_name", "some_address" };  
    StorageBox sb1(1, 2, 3, label1);  
  
    // passing a temporary label  
    StorageBox sb2(3, 4, 5, Label{ "another name", "another address" });  
  
    // passing a temporary label as an initializer list  
    StorageBox sb3(1, 2, 3, {"myname", "myaddress"});  
}  
```  
  
##  <a name="delegating_constructors"></a>委托构造函数  
 A*委托构造函数*完成部分初始化工作的同一个类中调用的其他构造函数。 在下面的示例中，派生类具有三个构造函数，第二个构造函数委托第一个，第三个构造函数委托第二个：  
  
```cpp  
#include <iostream>  
using namespace std;  
  
class ConstructorDestructor {  
public:  
    ConstructorDestructor() {  
        cout << "ConstructorDestructor default constructor." << endl;  
    }  
    ConstructorDestructor(int int1) {  
        cout << "ConstructorDestructor constructor with 1 int." << endl;  
    }  
    ConstructorDestructor(int int1, int int2) : ConstructorDestructor(int1) {  
        cout << "ConstructorDestructor constructor with 2 ints." << endl;  
  
        throw exception();  
    }  
    ConstructorDestructor(int int1, int int2, int int3) : ConstructorDestructor(int1, int2) {  
        cout << "ConstructorDestructor constructor with 3 ints." << endl;  
    }  
    ~ConstructorDestructor() {  
        cout << "ConstructorDestructor destructor." << endl;  
    }  
};  
  
int main() {  
    ConstructorDestructor dc(1, 2, 3);  
}  
  
```  
  
 这是输出：  
  
```  
ConstructorDestructor constructor with 1 int.  
ConstructorDestructor constructor with 2 ints.  
ConstructorDestructor constructor with 3 ints.  
```  
  
 所有构造函数完成后，完全初始化的构造函数将立即创建对象。 `DerivedContainer(int int1)` 成功，但是 `DerivedContainer(int int1, int int2)` 失败，并调用析构函数。  
  
```cpp  
class ConstructorDestructor {  
public:  
    ConstructorDestructor() {  
        cout << "ConstructorDestructor default constructor." << endl;  
    }  
    ConstructorDestructor(int int1) {  
        cout << "ConstructorDestructor constructor with 1 int." << endl;  
    }  
    ConstructorDestructor(int int1, int int2) : ConstructorDestructor(int1) {  
        cout << "ConstructorDestructor constructor with 2 ints." << endl;  
        throw exception();  
    }  
    ConstructorDestructor(int int1, int int2, int int3) : ConstructorDestructor(int1, int2) {  
        cout << "ConstructorDestructor constructor with 3 ints." << endl;  
    }  
  
    ~ConstructorDestructor() {  
        cout << "ConstructorDestructor destructor." << endl;  
    }  
};  
  
int main() {  
  
    try {  
        ConstructorDestructor cd{ 1, 2, 3 };  
    }  
    catch (const exception& ex){  
    }  
}  
  
```  
  
 输出：  
  
```  
ConstructorDestructor constructor with 1 int.  
ConstructorDestructor constructor with 2 ints.  
ConstructorDestructor destructor.  
```  
  
 有关详细信息，请参阅[统一初始化和委托构造函数](../cpp/uniform-initialization-and-delegating-constructors.md)。  
  
##  <a name="inheriting_constructors"></a>继承构造函数 (C++ 11)  
 派生类可以使用 using 声明从直接基类继承构造函数，如下面的示例所示：  
  
```  
#include <iostream>  
using namespace std;  
  
class Base  
{  
public:      
    Base() { cout << "Base()" << endl; }  
    Base(const Base& other) { cout << "Base(Base&)" << endl; }  
    explicit Base(int i) : num(i) { cout << "Base(int)" << endl; }  
    explicit Base(char c) : letter(c) { cout << "Base(char)" << endl; }  
  
private:  
    int num;  
    char letter;  
};  
  
class Derived : Base  
{  
public:  
    // Inherit all constructors from Base  
    using Base::Base;  
  
private:  
    // Can't initialize newMember from Base constructors.  
    int newMember{ 0 };  
};  
  
int main(int argc, char argv[])  
{  
    cout << "Derived d1(5) calls: ";   
    Derived d1(5);  
    cout << "Derived d1('c') calls: ";  
    Derived d2('c');  
    cout << "Derived d3 = d2 calls: " ;  
    Derived d3 = d2;  
    cout << "Derived d4 calls: ";  
    Derived d4;   
  
    // Keep console open in debug mode:  
    cout << endl << "Press Enter to exit.";  
    char in[1];  
    cin.getline(in, 1);  
    return 0;  
}  
  
/* Output:  
Derived d1(5) calls: Base(int)  
Derived d1('c') calls: Base(char)  
Derived d3 = d2 calls: Base(Base&)  
Derived d4 calls: Base()  
  
Press Enter to exit.  
  
```  
  
 using 语句可将来自基类的所有构造函数引入范围（除了签名与派生类中的构造函数相同的构造函数）。 一般而言，当派生类未声明新数据成员或构造函数时，最好使用继承构造函数。  
  
 如果类型指定基类，则类模板可以从类型参数继承所有构造函数：  
  
```  
template< typename T >  
class Derived : T {  
    using T::T;   // declare the constructors from T  
    // ...  
};  
  
```  
  
 如果基类的构造函数具有相同签名，则派生类无法从多个基类继承。  
  
##  <a name="rules_for_declaring_constructors"></a>声明构造函数的规则  
 构造函数与它的类的名称相同。 可以声明任意数量的构造函数，这取决于重载函数的规则。  
  
 `argument-declaration-list` 可能为空。  
  
 C++ 定义两种特殊的构造函数（默认构造函数和复制构造函数），如下表所述。  
  
### <a name="default-and-copy-constructors"></a>默认构造函数和复制构造函数  
  
|构造种类|参数|目标|  
|--------------------------|---------------|-------------|  
|默认构造函数|可以在没有参数的情况下调用|构造类类型的默认对象|  
|复制构造函数|可以接受对相同类类型的引用的单一参数|复制类类型的对象|  
  
 默认构造函数可在没有参数的情况下调用。 但是，如果所有参数都有默认值，则可以用参数列表声明默认构造函数。 同样，复制构造函数必须接受对相同类类型的引用的单一参数。 可以提供多个参数，前提是所有后续参数都有默认值。  
  
 如果未提供任何构造函数，则编译器将尝试生成默认构造函数。 如果未提供复制构造函数，则编译器将尝试生成一个。 这些编译器生成的构造函数被视为公共成员函数。 如果使用属于对象但不属于引用的第一个参数指定复制构造函数，则将生成错误。  
  
 编译器生成的默认构造函数将设置对象（如上文所述，初始化 vftables 和 vbtables），并调用基类和成员的默认构造函数，但是它不执行任何其他操作。 仅当基类和成员构造函数存在、可访问并且无歧义时才会调用它们。  
  
 编译器生成的复制构造函数将设置新的对象，并对要复制的对象的内容按成员复制。 如果基类或成员构造函数存在，则将调用它们；否则将执行按位复制。  
  
 如果类的所有基类和成员类`type`具有复制构造函数接受**const**参数，则编译器生成的复制构造函数接受单个参数的类型**const** `type`**&**. 否则，编译器生成的复制构造函数将接受类型的单个参数`type`  **&** 。  
  
 你可以使用构造函数初始化**const**或`volatile`对象，但构造函数本身不能声明为**const**或`volatile`。 构造函数的唯一合法存储类是**内联**; 使用任何其他存储类修饰符，包括`__declspec`关键字，具有一个构造函数会导致编译器错误。  
  
 Stdcall 调用约定用于静态成员函数和全局函数声明与**__stdcall**关键字，并且不使用变量参数列表。 当你使用**__stdcall**关键字对非静态成员函数，如构造函数时，编译器将使用 thiscall 调用约定。"  
  
 基类的构造函数不由派生类继承。 创建派生类类型的对象时，该对象将从基类组件开始进行构造；然后移到派生类组件。 编译器使用每个基类的构造函数，为完整的对象的该部分已初始化 （虚拟派生的情况除外。  
  
##  <a name="explicitly_invoking_constructors"></a>显式调用构造函数  
 可以在程序中显式调用构造函数来创建给定类型的对象。 例如，若要创建描述某行末尾的两个 `Point` 对象，请编写以下代码：  
  
```  
DrawLine( Point( 13, 22 ), Point( 87, 91 ) );  
```  
  
 创建类型 `Point` 的两个对象，将其传递给函数 `DrawLine`，并在表达式（函数调用）的末尾将其销毁。  
  
 在其中显式调用构造函数的另一个上下文正在进行初始化：  
  
```  
Point pt = Point( 7, 11 );  
```  
  
 使用接受类型为 `Point` 的两个参数的构造函数来创建和初始化类型为 `int` 的对象。  
  
 通过显式调用构造函数创建的对象（如上面的两个示例）未进行命名，并且该对象具有在其中创建它们的表达式的生存期。 更详细地介绍对此进行讨论[临时对象](../cpp/temporary-objects.md)。  
  
 通常，从构造函数的内部调用所有成员函数是安全的，因为该对象在用户代码的第一行执行之前已完全设置（已初始化虚拟表等）。 但是，在构造或析构期间，成员函数调用抽象基类的虚拟成员函数可能是不安全的。  
  
 构造函数可以调用虚函数。 调用虚函数时，调用的函数将是为构造函数自己的类定义的函数（或从其基类继承）。 以下示例演示从构造函数的内部调用虚函数时发生的情况：  
  
```  
// specl_calling_virtual_functions.cpp  
// compile with: /EHsc  
#include <iostream>  
  
using namespace std;  
class Base  
{  
public:  
    Base();             // Default constructor.  
    virtual void f();   // Virtual member function.  
};  
  
Base::Base()  
{  
    cout << "Constructing Base sub-object\n";  
    f();                // Call virtual member function  
}                       //  from inside constructor.  
  
void Base::f()  
{  
    cout << "Called Base::f()\n";  
}  
  
class Derived : public Base  
{  
public:  
    Derived();          // Default constructor.  
    void f();           // Implementation of virtual  
};                      //  function f for this class.  
  
Derived::Derived()  
{  
    cout << "Constructing Derived object\n";  
}  
  
void Derived::f()  
{  
    cout << "Called Derived::f()\n";  
}  
  
int main()  
{  
    Derived d;  
}  
```  
  
 在运行前面的程序时，声明 `Derived d` 将产生以下事件序列：  
  
1.  调用类 `Derived` (`Derived::Derived`) 的构造函数。  
  
2.  在输入 `Derived` 类的构造函数的主体之前，调用类 `Base` (`Base::Base`) 的构造函数。  
  
 `Base::Base` 调用函数 `f`，该函数是一个虚函数。 通常，将调用 `Derived::f`，因为对象 `d` 属于类型 `Derived`。 由于 `Base::Base` 函数是构造函数，因此该对象不属于 `Derived` 类型，并且将调用 `Base::f`。  
  
