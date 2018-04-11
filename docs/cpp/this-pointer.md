---
title: "此指针 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: this_cpp
dev_langs: C++
helpviewer_keywords:
- nonstatic member functions [C++]
- pointers, to class instance
- this pointer
ms.assetid: 92e3256a-4ad9-4d46-8be1-d77fad90791f
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 814e7518c6ed7052abc93b9e4705be93172b1e7f
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="this-pointer"></a>this 指针
**这**指针是只能在的非静态成员函数中访问指针**类**， `struct`，或**联合**类型。 它指向为其调用成员函数的对象。 静态成员函数没有**这**指针。  
  
## <a name="syntax"></a>语法  
  
```  
  
      this   
this->member-identifier  
```  
  
## <a name="remarks"></a>备注  
 对象的**这**指针不是对象本身; 的一部分不会反映在的结果`sizeof`对象上的语句。 相反，当对某个对象调用非静态成员函数时，该对象的地址将由编译器作为隐藏的参数传递给函数。 例如，以下函数调用：  
  
```  
myDate.setMonth( 3 );  
```  
  
 可以按以下方式解释：  
  
```  
setMonth( &myDate, 3 );  
```  
  
 对象的地址是可从成员函数作为**这**指针。 大部分使用**这**是隐式的。 它是合法的但没有必要显式使用**这**在引用类的成员时。 例如:  
  
```  
void Date::setMonth( int mn )  
{  
   month = mn;            // These three statements  
   this->month = mn;      // are equivalent  
   (*this).month = mn;  
}  
```  
  
 表达式 `*this` 通常用于从成员函数返回当前对象：  
  
```  
return *this;  
```  
  
 **这**指针还用于防止自引用：  
  
```  
if (&Object != this) {  
// do not execute in cases of self-reference  
```  
  
> [!NOTE]
>  因为**这**指针是不可修改，分配到**这**不允许。 C++ 的早期实现允许对分配**这**。  
  
 有时，**这**直接使用指针 — 例如，操作自引用数据结构，在要求的当前对象的地址的位置。  
  
## <a name="example"></a>示例  
  
```  
// this_pointer.cpp  
// compile with: /EHsc  
  
#include <iostream>  
#include <string.h>  
  
using namespace std;  
  
class Buf   
{  
public:  
    Buf( char* szBuffer, size_t sizeOfBuffer );  
    Buf& operator=( const Buf & );  
    void Display() { cout << buffer << endl; }  
  
private:  
    char*   buffer;  
    size_t  sizeOfBuffer;  
};  
  
Buf::Buf( char* szBuffer, size_t sizeOfBuffer )  
{  
    sizeOfBuffer++; // account for a NULL terminator  
  
    buffer = new char[ sizeOfBuffer ];  
    if (buffer)  
    {  
        strcpy_s( buffer, sizeOfBuffer, szBuffer );  
        sizeOfBuffer = sizeOfBuffer;  
    }  
}  
  
Buf& Buf::operator=( const Buf &otherbuf )   
{  
    if( &otherbuf != this )   
    {  
        if (buffer)  
            delete [] buffer;  
  
        sizeOfBuffer =  strlen( otherbuf.buffer ) + 1;   
        buffer = new char[sizeOfBuffer];  
        strcpy_s( buffer, sizeOfBuffer, otherbuf.buffer );  
    }  
    return *this;  
}  
  
int main()  
{  
    Buf myBuf( "my buffer", 10 );  
    Buf yourBuf( "your buffer", 12 );  
  
    // Display 'my buffer'  
    myBuf.Display();  
  
    // assignment opperator  
    myBuf = yourBuf;  
  
    // Display 'your buffer'  
    myBuf.Display();  
}  
```  
  
```Output  
my buffer  
your buffer  
```  
  
## <a name="type-of-the-this-pointer"></a>this 指针的类型  
 **这**指针的类型可以修改的函数声明中**const**和`volatile`关键字。 若要将函数声明为具有一个或多个关键字的特性，请将关键字添加到函数参数列表的后面。  
  
 请看以下示例：  
  
```  
// type_of_this_pointer1.cpp  
class Point  
{  
    unsigned X() const;  
};  
int main()  
{  
}  
```  
  
 上面的代码声明一个成员函数`X`，在其中**这**指针被视为**const**指向**const**对象。 组合*cv mod 列表*可以使用选项，但它们始终修改指向的对象**这**，而不**这**指针本身。 因此，以下声明将声明函数`X`;**这**指针**const**指向**const**对象：  
  
```  
// type_of_this_pointer2.cpp  
class Point  
{  
    unsigned X() const;  
};  
int main()  
{  
}  
```  
  
 一种**这**在成员函数中的以下语法，其中*cv 限定符列表*确定从成员函数声明符和可以为**const**或**易失性**（或两者），和*类类型*是类的名称：  
  
 *[cv 限定符列表] 类类型* **\* const 这**   
  
 换而言之，**这**始终是 const 指针; 无法重新指派它。  **Const**或`volatile`成员函数声明中使用的限定符应用于指向的类实例**这**该函数的作用域中。  
  
 下表介绍了有关这些修饰符的工作方式的更多信息。  
  
### <a name="semantics-of-this-modifiers"></a>this 修饰符的语义  
  
|修饰符|含义|  
|--------------|-------------|  
|**const**|不能更改数据成员;无法调用成员函数不是**const**。|  
|`volatile`|每次访问成员数据时，都会从内存中加载该数据；禁用某些优化。|  
  
 它是错误传递**const**指向不是成员函数的对象**const**。 同样，将 `volatile` 对象传递给不是 `volatile` 的成员函数也是错误的。  
  
 成员函数声明为**const**不能更改数据成员-在此类函数中，**这**指针是指向的**const**对象。  
  
> [!NOTE]
>  构造函数和析构函数不能声明为**const**或`volatile`。 它们，但是，可以调用**const**或`volatile`对象。  
  
## <a name="see-also"></a>请参阅  
 [关键字](../cpp/keywords-cpp.md)   
 