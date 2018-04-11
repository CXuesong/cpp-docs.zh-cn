---
title: "const （C++） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: const_cpp
dev_langs: C++
helpviewer_keywords: const keyword [C++]
ms.assetid: b21c0271-1ad0-40a0-b21c-5e812bba0318
caps.latest.revision: "7"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: c296f473f9128907e69437edf66734a7566242ef
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="const-c"></a>const (C++)
在修改数据声明， **const**关键字指定对象或变量不是可修改。  
  
## <a name="syntax"></a>语法  
  
```  
  
      const declaration ;  
member-function const ;  
```  
  
## <a name="const-values"></a>常量值  
 **Const**关键字指定变量的值是常量并通知编译器防止程序员修改它。  
  
```  
// constant_values1.cpp  
int main() {  
   const int i = 5;  
   i = 10;   // C3892  
   i++;   // C2105  
}  
```  
  
 在 C++，你可以使用**const**关键字而不是[#define](../preprocessor/hash-define-directive-c-cpp.md)预处理器指令定义常量值。 使用定义的值**const**需要接受类型检查，并可用于代替常量表达式。 C++ 中，你可以指定数组的大小**const**变量，如下所示：  
  
```  
// constant_values2.cpp  
// compile with: /c  
const int maxarray = 255;  
char store_char[maxarray];  // allowed in C++; not allowed in C  
```  
  
 在 C 中，常量值默认为外部链接，因此它们只能出现在源文件中。 在 C++ 中，常量值默认为内部链接，这使它们可以出现在标头文件中。  
  
 **Const**关键字还可在指针声明中。  
  
```  
// constant_values3.cpp  
int main() {  
   char *mybuf = 0, *yourbuf;  
   char *const aptr = mybuf;  
   *aptr = 'a';   // OK  
   aptr = yourbuf;   // C3892  
}  
```  
  
 指向声明为的变量的指针**const**可以只分配给也声明为指针**const**。  
  
```  
// constant_values4.cpp  
#include <stdio.h>  
int main() {  
   const char *mybuf = "test";  
   char *yourbuf = "test2";  
   printf_s("%s\n", mybuf);  
  
   const char *bptr = mybuf;   // Pointer to constant data  
   printf_s("%s\n", bptr);  
  
   // *bptr = 'a';   // Error  
}  
```  
  
 可以将指向常量数据的指针用作函数参数，以防止函数修改通过指针传递的参数。  
  
 对象声明为**const**，你可以仅调用常量成员函数。 这将确保从不修改常量对象。  
  
```  
birthday.getMonth();    // Okay  
birthday.setMonth( 4 ); // Error  
```  
  
 可以为非常量对象调用常量或非常量成员函数。 您还可以重载成员函数使用**const**关键字; 这使得可以对常量和非常量对象调用的函数的不同版本。  
  
 你不能声明构造函数或析构函数使用**const**关键字。  
  
## <a name="const-member-functions"></a>常量成员函数  
 声明包含的成员函数**const**关键字指定函数是"只读"函数，不会修改为其调用的对象。 常量成员函数不能修改任何非静态数据成员或调用不是常量的函数的任何成员。若要声明常量成员函数时，将放置**const**参数列表的右括号后的关键字。 **Const**关键字需要声明和定义中。  
  
```  
// constant_member_function.cpp  
class Date  
{  
public:  
   Date( int mn, int dy, int yr );  
   int getMonth() const;     // A read-only function  
   void setMonth( int mn );   // A write function; can't be const  
private:  
   int month;  
};  
  
int Date::getMonth() const  
{  
   return month;        // Doesn't modify anything  
}  
void Date::setMonth( int mn )  
{  
   month = mn;          // Modifies data member  
}  
int main()  
{  
   Date MyDate( 7, 4, 1998 );  
   const Date BirthDate( 1, 18, 1953 );  
   MyDate.setMonth( 4 );    // Okay  
   BirthDate.getMonth();    // Okay  
   BirthDate.setMonth( 4 ); // C2662 Error  
}  
```  
  
## <a name="c-and-c-const-differences"></a>C 和 C++ const 的差异  
 当你声明一个变量，作为**const** C 源代码文件，在您执行方式：  
  
```  
const int i = 2;  
```  
  
 您随后可以在另一个模块中使用此变量，如下所示：  
  
```  
extern const int i;  
```  
  
 若要在 C++ 中获得相同的行为，您必须声明，但你**const**变量作为：  
  
```  
extern const int i = 2;  
```  
  
 如果希望在 C++ 源代码文件声明用于 C 源代码文件的 `extern` 变量，请使用：  
  
```  
extern "C" const int x=10;  
```  
  
 以防止 C++ 编译器进行名称重整。  
  
## <a name="remarks"></a>备注  
 当成员函数的参数列表，后面的**const**关键字指定函数不会修改为其被调用的对象。  
  
 有关详细信息**const**，请参阅以下主题：  
    
-   [固定和可变指针](../cpp/const-and-volatile-pointers.md)  
  
-   [类型限定符 （C 语言参考）](../c-language/type-qualifiers.md)  
  
-   [volatile](../cpp/volatile-cpp.md)  
  
-   [#define](../preprocessor/hash-define-directive-c-cpp.md)。  
  
## <a name="see-also"></a>请参阅  
 [关键字](../cpp/keywords-cpp.md)