---
title: "新和 delete 运算符 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: article
f1_keywords:
- delete_cpp
- new
dev_langs: C++
helpviewer_keywords:
- new keyword [C++], dynamic allocation of objects
- nothrownew.obj
- delete keyword [C++], syntax
ms.assetid: fa721b9e-0374-4f04-bb87-032ea775bcc8
caps.latest.revision: "16"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 3af862988502ac0d1908c466aae5e62b753509c2
ms.sourcegitcommit: 9a0a287d6940591523af959ebdac5affa36220da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="new-and-delete-operators"></a>new 和 delete 运算符

C++ 支持动态分配和释放对象使用[新](../cpp/new-operator-cpp.md)和[删除](../cpp/delete-operator-cpp.md)运算符。 这些运算符为来自称为“自由存储”的池中的对象分配内存。 `new`运算符调用特殊函数[运算符 new](../cpp/new-operator-cpp.md)，和`delete`运算符调用特殊函数[运算符 delete](../cpp/delete-operator-cpp.md)。  
  
 `new` C++ 标准库中的函数支持 C++ 标准，则会引发 std:: bad_alloc 异常，如果内存分配失败中指定的行为。 如果您仍需要的非引发版本`new`，将 nothrownew.obj 程序显式链接。但是，当您链接到 nothrownew.obj 时，默认值`operator new`在 C++ 标准库中不再正常工作。  
  
 有关包含 C 运行库和 C++ 标准库的库文件的列表，请参阅[CRT 库功能](../c-runtime-library/crt-library-features.md)。  
  
##  <a id="new_operator"></a> New 运算符  
 在程序中遇到以下语句时，它将转换为对函数 `operator new` 的调用：  
  
```cpp  
char *pch = new char[BUFFER_SIZE];  
```  
  
如果请求针对零字节存储，**运算符 new**将指针返回到一个不同的对象 (即，重复调用**运算符 new**返回不同的指针)。 如果没有足够的内存分配请求，**运算符 new**会引发 std:: bad_alloc 异常，或返回**nullptr**如果你已链接中非引发`operator new`支持。  
  
你可以编写尝试释放内存，然后重试分配; 的例程请参阅[_set_new_handler](../c-runtime-library/reference/set-new-handler.md)有关详细信息。 有关恢复方案的更多详细信息，请参阅本主题的处理内存不足，部分。  

  
下表中描述了 `operator new` 函数的两个范围。  
  
### <a name="scope-for-operator-new-functions"></a>operator new 函数的范围  
  
|运算符|范围|  
|--------------|-----------|  
|**:: new 运算符**|Global|  
|*类名* **:: new 运算符**|类|  
  
 第一个参数**运算符 new**的类型必须为**size_t** (中定义的类型\<stddef.h >)，并且返回类型始终**void \*** .  
  
 全局**运算符 new**时，将调用函数**新**运算符用于分配内置类型的对象、 类类型的对象，其中不包含用户定义**运算符 new**函数和任何类型的数组。 当**新**运算符用于分配类类型的对象其中**运算符 new**定义，该类的**运算符 new**调用。  
  
 **运算符 new**为类的静态成员函数 （它不能因此，为虚拟） 会隐藏全局定义的函数**运算符 new**该类类型的对象的函数。 请考虑这种情况其中**新**用于分配和内存设置为给定的值：  
  
```cpp  
// spec1_the_operator_new_function1.cpp  
#include <malloc.h>  
#include <memory.h>  
  
class Blanks  
{  
public:  
    Blanks(){}  
    void *operator new( size_t stAllocateBlock, char chInit );  
};  
void *Blanks::operator new( size_t stAllocateBlock, char chInit )  
{  
    void *pvTemp = malloc( stAllocateBlock );  
    if( pvTemp != 0 )  
        memset( pvTemp, chInit, stAllocateBlock );  
    return pvTemp;  
}  
// For discrete objects of type Blanks, the global operator new function  
// is hidden. Therefore, the following code allocates an object of type  
// Blanks and initializes it to 0xa5  
int main()  
{  
   Blanks *a5 = new(0xa5) Blanks;  
   return a5 != 0;  
}  
```  
  
 提供到的括号中的参数**新**传递给`Blanks::operator new`作为`chInit`参数。 但是，全局**运算符 new**函数被隐藏，从而导致如下代码生成错误：  
  
```cpp  
Blanks *SomeBlanks = new Blanks;  
```  
  
 在 Visual C++ 5.0 和更早版本，非类类型和所有数组 (无论是否的**类**类型) 使用分配**新**运算符始终使用全局**运算符 new**函数。  
  
 从 Visual C++ 5.0 开始，编译器支持成员数组**新**和**删除**类声明中的运算符。 例如:  
  
```cpp  
// spec1_the_operator_new_function2.cpp  
class MyClass  
{  
public:  
   void * operator new[] (size_t)  
   {  
      return 0;  
   }  
   void   operator delete[] (void*)  
   {  
   }  
};  
  
int main()   
{  
   MyClass *pMyClass = new MyClass[5];  
   delete [] pMyClass;  
}  
```  
  
### <a name="handling-insufficient-memory"></a>处理内存不足  
 对失败的内存分配进行测试可以通过如下代码实现：  
  
```cpp  
// insufficient_memory_conditions.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
#define BIG_NUMBER 100000000  
int main() {  
   int *pI = new int[BIG_NUMBER];  
   if( pI == 0x0 ) {  
      cout << "Insufficient memory" << endl;  
      return -1;  
   }  
}  
```  
  
 若要处理失败的内存分配请求的其他方法： 编写自定义恢复例程来处理此类故障，然后通过调用注册您的函数[_set_new_handler](../c-runtime-library/reference/set-new-handler.md)运行时函数。  
  
##  <a id="delete_operator"></a> Delete 运算符  
 使用动态分配的内存**新**运算符可以释放使用**删除**运算符。 Delete 运算符调用**运算符 delete**函数，将内存释放回可用池。 使用**删除**运算符也会导致类析构函数 （如果存在） 调用。  
  
 有全局和类范围**运算符 delete**函数。 只有一个**运算符 delete**可以为给定类定义函数; 如果定义，它会隐藏全局**运算符 delete**函数。 全局**运算符 delete**函数始终调用的任何类型的数组。  
  
 全局**运算符 delete**函数。 有两种形式全局**运算符 delete**和类成员**运算符 delete**函数：  
  
```cpp  
void operator delete( void * );  
void operator delete( void *, size_t );  
```  
  
 前面的两种形式中只有一个可用于给定的类。 第一种形式采用单个参数的类型**void \*** ，其中包含指向要释放的对象的指针。 第二种形式-大小的释放-采用两个参数，其中第一个是指向要释放的内存块的指针，第二个是要释放的字节的数量。 这两种形式的返回类型是`void`(**运算符 delete**无法返回值)。  
  
 第二种形式的意图是对象的加快搜索要删除的正确大小类别的速度，这通常不是对象的存储在分配本身附近并且可能未高速缓存;时，第二种形式是特别有用**运算符 delete**从基类函数用于删除派生类的对象。  
  
 **运算符 delete**函数是静态的; 因此，不能是虚。 `operator delete`函数服从访问控制中所述[成员访问控制](../cpp/member-access-control-cpp.md)。  
  
 下面的示例演示用户定义**运算符 new**和**运算符 delete**函数旨在记录分配和释放的内存：  
  
```cpp  
// spec1_the_operator_delete_function1.cpp  
// compile with: /EHsc  
// arguments: 3  
#include <iostream>  
using namespace std;  
  
int fLogMemory = 0;      // Perform logging (0=no; nonzero=yes)?  
int cBlocksAllocated = 0;  // Count of blocks allocated.  
  
// User-defined operator new.  
void *operator new( size_t stAllocateBlock ) {  
   static int fInOpNew = 0;   // Guard flag.  
  
   if ( fLogMemory && !fInOpNew ) {  
      fInOpNew = 1;  
      clog << "Memory block " << ++cBlocksAllocated  
          << " allocated for " << stAllocateBlock  
          << " bytes\n";  
      fInOpNew = 0;  
   }  
   return malloc( stAllocateBlock );  
}  
  
// User-defined operator delete.  
void operator delete( void *pvMem ) {  
   static int fInOpDelete = 0;   // Guard flag.  
   if ( fLogMemory && !fInOpDelete ) {  
      fInOpDelete = 1;  
      clog << "Memory block " << cBlocksAllocated--  
          << " deallocated\n";  
      fInOpDelete = 0;  
   }  
  
   free( pvMem );  
}  
  
int main( int argc, char *argv[] ) {  
   fLogMemory = 1;   // Turn logging on  
   if( argc > 1 )  
      for( int i = 0; i < atoi( argv[1] ); ++i ) {  
         char *pMem = new char[10];  
         delete[] pMem;  
      }  
   fLogMemory = 0;  // Turn logging off.  
   return cBlocksAllocated;  
}  
```  
  
 前面的代码可用于检测“内存溢出”，即在自由储存中分配但从未释放过的内存。 若要执行此检测，全局**新**和**删除**运算符会重新定义为计数分配和解除分配的内存。  
  
 从 Visual C++ 5.0 开始，编译器支持成员数组**新**和**删除**类声明中的运算符。 例如:  
  
```cpp  
// spec1_the_operator_delete_function2.cpp  
// compile with: /c  
class X  {  
public:  
   void * operator new[] (size_t) {  
      return 0;  
   }  
   void operator delete[] (void*) {}  
};  
  
void f() {  
   X *pX = new X[5];  
   delete [] pX;  
}  
```  

