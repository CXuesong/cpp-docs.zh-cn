---
title: "new 运算符 （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords: new keyword [C++]
ms.assetid: 69fee812-1c28-4882-8fda-d1ad17860004
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 68843f0619b5ebc057f83bdb4f49807a15fb86a1
ms.sourcegitcommit: 9a0a287d6940591523af959ebdac5affa36220da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="new-operator-c"></a>new 运算符 (C++)
对象或对象数组分配内存*类型名称*从自由存储和返回非零、 适当类型化指针的对象。  
  
> [!NOTE]
>  Microsoft C++ 组件扩展为 `new` 关键字提供支持以添加 vtable 槽条目。 有关详细信息，请参阅[新 (新 vtable 中的槽）](../windows/new-new-slot-in-vtable-cpp-component-extensions.md)  
  
## <a name="syntax"></a>语法  
  
```  
[::] new [placement] new-type-name [new-initializer]  
[::] new [placement] ( type-name ) [new-initializer]  
```  
  
## <a name="remarks"></a>备注  
 如果不成功，**新**返回零或引发异常; 请参阅[新和 delete 运算符](../cpp/new-and-delete-operators.md)有关详细信息。 你可以更改此默认行为： 编写自定义异常处理例程并调用[_set_new_handler](../c-runtime-library/reference/set-new-handler.md)带有函数名称作为其参数的运行时库函数。  
  
 有关如何在托管堆上创建对象的信息，请参阅[gcnew](../windows/ref-new-gcnew-cpp-component-extensions.md)。  
  
 当**新**是用于为 C++ 类对象分配内存，该对象的构造函数中调用后分配内存。  
  
 使用[删除](../cpp/delete-operator-cpp.md)运算符解除分配的内存分配与**新**运算符。  
  
 以下示例先分配然后释放一个二维字符数组，数组的大小为 `dim` x 10。 在分配多维数组时，除第一个维度之外的所有维度必须是计算结果为正值的常量表达式；最左侧的数组维度可以是计算结果为正值的任何表达式。 分配数组使用时**新**运算符，第一个维度可以是零-**新**运算符将返回一个唯一指针。  
  
```  
char (*pchar)[10] = new char[dim][10];  
delete [] pchar;  
```  
  
 *类型名称*不能包含**const**， `volatile`，类声明或枚举声明。 因此，以下表达式是非法的：  
  
```  
volatile char *vch = new volatile char[20];  
```  
  
 **新**运算符不会分配引用类型，因为它们不是对象。  
  
 **新**运算符不能用于分配函数，但它可以用于分配指向函数的指针。 下面的示例为返回整数的函数分配然后释放一个包含 7 个指针的数组。  
  
```  
int (**p) () = new (int (*[7]) ());  
delete *p;  
```  
  
 如果使用运算符**新**而无需任何额外参数，且与编译[/GX](../build/reference/gx-enable-exception-handling.md)， [/EHa](../build/reference/eh-exception-handling-model.md)，或[/EHs](../build/reference/eh-exception-handling-model.md)选项，编译器会将生成代码来调用运算符**删除**在构造函数引发异常。  
  
 下面的列表描述的语法元素**新**:  
  
 *placement*  
 提供一种传递附加参数，如果重载**新**。  
  
 *type-name*  
 指定要分配的类型；它可以是内置类型，也可以是用户定义的类型。 如果类型规范非常复杂，则可用括号将其括起来以强制实施绑定顺序。  
  
 *initializer*  
 为初始化对象提供值。 不能为数组指定初始值设定项。 **新**运算符将创建对象的数组，仅当类具有一个默认构造函数。  
  
## <a name="example"></a>示例  
 下面的代码示例分配类 `CName` 的一个字符数组和一个对象，然后释放它们。  
  
```  
// expre_new_Operator.cpp  
// compile with: /EHsc  
#include <string.h>  
  
class CName {  
public:  
   enum {  
      sizeOfBuffer = 256  
   };  
  
   char m_szFirst[sizeOfBuffer];  
   char m_szLast[sizeOfBuffer];  
  
public:  
   void SetName(char* pszFirst, char* pszLast) {  
     strcpy_s(m_szFirst, sizeOfBuffer, pszFirst);  
     strcpy_s(m_szLast, sizeOfBuffer, pszLast);  
   }  
  
};  
  
int main() {  
   // Allocate memory for the array  
   char* pCharArray = new char[CName::sizeOfBuffer];  
   strcpy_s(pCharArray, CName::sizeOfBuffer, "Array of characters");  
  
   // Deallocate memory for the array  
   delete [] pCharArray;             
   pCharArray = NULL;  
  
   // Allocate memory for the object  
   CName* pName = new CName;  
   pName->SetName("Firstname", "Lastname");  
  
   // Deallocate memory for the object  
   delete pName;  
   pName = NULL;  
}  
```  
  
## <a name="example"></a>示例  
 如果你使用的放置新形式**新**运算符，具有除了大小参数的分配，编译器的窗体不支持的放置形式**删除**运算符如果构造函数引发异常。 例如:  
  
```  
// expre_new_Operator2.cpp  
// C2660 expected  
class A {  
public:  
   A(int) { throw "Fail!"; }  
};  
void F(void) {  
   try {  
      // heap memory pointed to by pa1 will be deallocated  
      // by calling ::operator delete(void*).  
      A* pa1 = new A(10);  
   } catch (...) {  
   }  
   try {  
      // This will call ::operator new(size_t, char*, int).  
      // When A::A(int) does a throw, we should call  
      // ::operator delete(void*, char*, int) to deallocate  
      // the memory pointed to by pa2.  Since  
      // ::operator delete(void*, char*, int) has not been implemented,  
      // memory will be leaked when the deallocation cannot occur.  
  
      A* pa2 = new(__FILE__, __LINE__) A(20);  
   } catch (...) {  
   }  
}  
  
int main() {  
   A a;  
}  
```  
  
## <a name="initializing-object-allocated-with-new"></a>初始化使用 new 运算符分配的对象  
 一个可选*初始值设定项*字段包含的语法在**新**运算符。 这样就可以使用用户定义的构造函数来初始化新对象。 有关如何执行初始化的详细信息，请参阅[初始值设定项](../cpp/initializers.md)。 下面的示例演示如何使用具有的初始化表达式**新**运算符：  
  
```  
// expre_Initializing_Objects_Allocated_with_new.cpp  
class Acct  
{  
public:  
    // Define default constructor and a constructor that accepts  
    //  an initial balance.  
    Acct() { balance = 0.0; }  
    Acct( double init_balance ) { balance = init_balance; }  
private:  
    double balance;  
};  
  
int main()  
{  
    Acct *CheckingAcct = new Acct;  
    Acct *SavingsAcct = new Acct ( 34.98 );  
    double *HowMuch = new double ( 43.0 );  
    // ...  
}  
```  
  
 在此示例中，该对象`CheckingAcct`使用分配**新**指定运算符，但没有默认初始化。 因此，调用了类的默认构造函数 `Acct()`。 然后，以相同的方式分配了对象 `SavingsAcct`，只不过将它显式初始化为 34.98。 由于 34.98 是类型**double**，调用采用该类型的参数的构造函数来处理初始化。 最后，将非类类型 `HowMuch` 初始化为 43.0。  
  
 如果对象是类类型的该类具有构造函数 （如同前面的示例中），可以通过初始化了对象**新**运算符仅当满足以下条件之一时：  
  
-   初始值设定项中提供的参数与构造函数的参数一致。  
  
-   该类有一个默认构造函数（可在没有参数的情况下调用的构造函数）。  
  
 使用分配数组时，没有显式的每个元素初始化可以进行**新**运算符; 仅默认构造函数，如果存在，则调用。 请参阅[默认参数](../cpp/default-arguments.md)有关详细信息。  
  
 如果内存分配失败（`operator new` 的返回值为 0），则不执行初始化。 这可防止尝试初始化不存在的数据。  
  
 与函数调用一样，未定义初始化表达式的计算顺序。 此外，您不应指望这些表达式能在执行内存分配前完全计算。 如果内存分配失败和**新**运算符将返回零，可能无法完全计算初始值设定项中的某些表达式。  
  
## <a name="lifetime-of-objects-allocated-with-new"></a>使用 new 运算符分配的对象的生存期  
 对象分配与**新**退出在其中定义的作用域时，将不会销毁运算符。 因为**新**运算符将指针返回到它所分配的对象，因此程序必须定义使用合适的范围，才能访问这些对象的指针。 例如:  
  
```  
// expre_Lifetime_of_Objects_Allocated_with_new.cpp  
// C2541 expected  
int main()  
{  
    // Use new operator to allocate an array of 20 characters.  
    char *AnArray = new char[20];  
  
    for( int i = 0; i < 20; ++i )  
    {  
        // On the first iteration of the loop, allocate  
        //  another array of 20 characters.  
        if( i == 0 )  
        {  
            char *AnotherArray = new char[20];  
        }  
    }  
  
    delete [] AnotherArray; // Error: pointer out of scope.  
    delete [] AnArray;      // OK: pointer still in scope.  
}  
```  
  
 在上面的示例中，指针 `AnotherArray` 一旦超出范围，将无法再删除对象。  
  
## <a name="how-new-works"></a>new 的工作方式  
 *分配表达式*-表达式包含**新**运算符-执行三项操作：  
  
-   定位并保留要分配的对象的存储。 此阶段完成后，将分配正确的存储量，但它还不是对象。  
  
-   初始化对象。 初始化完成后，将为成为对象的已分配存储显示足够的信息。  
  
-   返回指向指针类型的对象的指针派生自*新类型名称*或*类型名称*。 程序使用此指针来访问最近分配的对象。  
  
 **新**运算符调用的函数`operator new`。 对于数组的任何类型，以及的不是对象**类**， `struct`，或**联合**类型、 全局函数， **:: 运算符 new**，调用来分配存储空间。 类类型对象可基于每个类定义其自己的 `operator new` 静态成员函数。  
  
 当编译器遇到**新**运算符分配类型的对象`type`，它会发出调用`type` **:: 运算符 new (sizeof (** `type` **))**或如果不是用户定义`operator new`定义， **:: 运算符 new (sizeof (** `type` **))**。 因此，**新**运算符可以为对象分配正确的内存量。  
  
> [!NOTE]
>  参数`operator new`属于类型**size_t**。 此类型定义中\<.h >， \<l l >， \<y.h >， \<h.h >， \<stddef.h >， \<.h >， \<stdlib.h >， \<string.h >，和\<.h >。  
  
 在语法中的选项允许的规范*放置*(请参阅有关语法[new 运算符](../cpp/new-operator-cpp.md))。 *放置*参数可以仅用于用户定义的实现`operator new`; 它允许将额外信息传递给`operator new`。 一个具有表达式*放置*字段如`T *TObject = new ( 0x0040 ) T;`转换为`T *TObject = T::operator new( sizeof( T ), 0x0040 );`如果类 T 具有成员运算符 new，否则到`T *TObject = ::operator new( sizeof( T ), 0x0040 );`。  
  
 原始用途*放置*字段是允许在用户指定的地址分配硬件相关对象。  
  
> [!NOTE]
>  尽管前面的示例说明中的只有一个参数*放置*字段，可以将多少多余的参数传递给不受限制`operator new`这种方式。  
  
 即使已为类类型定义 `operator new`，也可以采用此示例的形式来使用全局运算符：  
  
```  
T *TObject =::new TObject;  
```  
  
 范围解析运算符 (`::`) 强制使用全局**新**运算符。  
  
## <a name="see-also"></a>请参阅  
 [使用一元运算符的表达式](../cpp/expressions-with-unary-operators.md)   
 [关键字](../cpp/keywords-cpp.md)   
 [新和 delete 运算符](../cpp/new-and-delete-operators.md)