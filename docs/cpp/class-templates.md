---
title: "类模板 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- classes [C++], operating on type
- class templates
- templates, class templates
ms.assetid: 633a53c8-24ee-4c23-8c88-e7c3cb0b7ac3
caps.latest.revision: "13"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: a53ed733bc710aa66061c40587e79f41e553b260
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="class-templates"></a>类模板
本主题介绍特定于 C++ 类模板的规则。  
  
## <a name="member-functions-of-class-templates"></a>类模板的成员函数  
 可以在类模板的内部或外部定义成员函数。 如果在类模板的外部定义成员函数，则会像定义函数模板一样定义它们。  
  
```cpp  
// member_function_templates1.cpp  
template<class T, int i> class MyStack  
{  
    T*  pStack;  
    T StackBuffer[i];  
    static const int cItems = i * sizeof(T);  
public:   
    MyStack( void );  
    void push( const T item );  
    T& pop( void );  
};  
  
template< class T, int i > MyStack< T, i >::MyStack( void )  
{  
};  
  
template< class T, int i > void MyStack< T, i >::push( const T item )  
{  
};  
  
template< class T, int i > T& MyStack< T, i >::pop( void )  
{  
};  
  
int main()  
{  
}  
```  
  
 请注意，就像任何模板类成员函数一样，类的构造函数成员函数的定义包含模板参数列表两次。  
  
 成员函数可以是函数模板，并指定附加参数，如下面的示例所示。  
  
```cpp  
// member_templates.cpp  
template<typename T>  
class X  
{  
public:  
   template<typename U>  
   void mf(const U &u);  
};  
  
template<typename T> template <typename U>  
void X<T>::mf(const U &u)  
{  
}  
  
int main()  
{  
}  
  
```  
  
## <a name="nested-class-templates"></a>嵌套的类模板  
 模板可以在类或类模板中定义，在这种情况下，它们被称为成员模板。 作为类的成员模板称为嵌套类模板。 是函数的成员模板中讨论了[成员函数模板](../cpp/member-function-templates.md)。  
  
 嵌套类模板被声明为外部类范围内的类模板。 可以在封闭类的内部或外部定义它们。  
  
 下面的代码演示普通类中的嵌套类模板。  
  
```cpp  
// nested_class_template1.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
class X  
{  
  
   template <class T>  
   struct Y  
   {  
      T m_t;  
      Y(T t): m_t(t) { }     
   };  
  
   Y<int> yInt;  
   Y<char> yChar;  
  
public:  
   X(int i, char c) : yInt(i), yChar(c) { }  
   void print()  
   {  
      cout << yInt.m_t << " " << yChar.m_t << endl;  
   }  
};  
  
int main()  
{  
   X x(1, 'a');  
   x.print();  
}  
```  
  
```cpp  
// nested_class_template2.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
template <class T>  
class X  
{  
   template <class U> class Y  
   {  
      U* u;  
   public:  
      Y();  
      U& Value();  
      void print();  
      ~Y();  
   };  
  
   Y<int> y;  
public:  
   X(T t) { y.Value() = t; }  
   void print() { y.print(); }  
};  
  
template <class T>   
template <class U>  
X<T>::Y<U>::Y()  
{  
   cout << "X<T>::Y<U>::Y()" << endl;  
   u = new U();  
}  
  
template <class T>   
template <class U>  
U& X<T>::Y<U>::Value()  
{  
   return *u;  
}  
  
template <class T>   
template <class U>  
void X<T>::Y<U>::print()  
{  
   cout << this->Value() << endl;  
}  
  
template <class T>   
template <class U>  
X<T>::Y<U>::~Y()  
{  
   cout << "X<T>::Y<U>::~Y()" << endl;  
   delete u;  
}  
  
int main()  
{  
   X<int>* xi = new X<int>(10);  
   X<char>* xc = new X<char>('c');  
   xi->print();  
   xc->print();  
   delete xi;  
   delete xc;  
}  
  
//Output:   
X<T>::Y<U>::Y()  
X<T>::Y<U>::Y()  
10  
99  
X<T>::Y<U>::~Y()  
X<T>::Y<U>::~Y()
```  
  
 局部类不允许具有成员模板。  
  
## <a name="template-friends"></a>模板友元  
 类模板可以具有[好友](http://msdn.microsoft.com/en-us/bf412640-d857-4acb-b2b5-513131cb9681)。 类或类模板、函数或函数模板可以是模板类的友元。 友元也可以是类模板或函数模板的专用化，但不是部分专用化。  
  
 在以下示例中，友元函数将定义为类模板中的函数模板。 此代码为模板的每个实例化生成一个友元函数版本。 如果您的友元函数与类依赖于相同的模板参数，则此构造很有用。  
  
```cpp  
// template_friend1.cpp  
// compile with: /EHsc  
  
#include <iostream>  
using namespace std;  
  
template <class T> class Array {  
   T* array;  
   int size;  
  
public:  
   Array(int sz): size(sz) {  
      array = new T[size];  
      memset(array, 0, size * sizeof(T));  
   }  
  
   Array(const Array& a) {  
      size = a.size;  
      array = new T[size];  
      memcpy_s(array, a.array, sizeof(T));  
   }  
  
   T& operator[](int i) {  
      return *(array + i);  
   }  
  
   int Length() { return size; }  
  
   void print() {  
      for (int i = 0; i < size; i++)        
         cout << *(array + i) << " ";  
  
      cout << endl;  
   }  
  
   template<class T>  
   friend Array<T>* combine(Array<T>& a1, Array<T>& a2);  
};  
  
template<class T>  
Array<T>* combine(Array<T>& a1, Array<T>& a2) {  
   Array<T>* a = new Array<T>(a1.size + a2.size);  
   for (int i = 0; i < a1.size; i++)  
      (*a)[i] = *(a1.array + i);  
  
   for (int i = 0; i < a2.size; i++)  
      (*a)[i + a1.size] = *(a2.array + i);  
  
   return a;  
}  
  
int main() {  
   Array<char> alpha1(26);  
   for (int i = 0 ; i < alpha1.Length() ; i++)  
      alpha1[i] = 'A' + i;  
  
   alpha1.print();  
  
   Array<char> alpha2(26);  
   for (int i = 0 ; i < alpha2.Length() ; i++)  
      alpha2[i] = 'a' + i;  
  
   alpha2.print();  
   Array<char>*alpha3 = combine(alpha1, alpha2);  
   alpha3->print();  
   delete alpha3;  
}  
//Output:   
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z   
a b c d e f g h i j k l m n o p q r s t u v w x y z   
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z a b c d e f g h i j k l m n o p q r s t u v w x y z   
```  
  
 下一个示例涉及具有模板专用化的友元。 如果原始函数模板是友元，则函数模板专用化将自动为友元。  
  
 也可以只将模板的专用版本声明为友元，如以下代码中的友元声明前面的注释所示。 如果这样做，则必须将友元模板专用化的定义放在模板类之外。  
  
```cpp  
// template_friend2.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
template <class T>  
class Array;  
  
template <class T>  
void f(Array<T>& a);  
  
template <class T> class Array  
{  
    T* array;  
    int size;  
  
public:  
    Array(int sz): size(sz)  
    {  
        array = new T[size];  
        memset(array, 0, size * sizeof(T));  
    }  
    Array(const Array& a)  
    {  
        size = a.size;  
        array = new T[size];  
        memcpy_s(array, a.array, sizeof(T));  
    }  
    T& operator[](int i)  
    {  
        return *(array + i);  
    }  
    int Length()  
    {   
        return size;  
    }  
    void print()  
    {  
        for (int i = 0; i < size; i++)  
        {  
            cout << *(array + i) << " ";  
        }  
        cout << endl;  
    }  
    // If you replace the friend declaration with the int-specific  
    // version, only the int specialization will be a friend.  
    // The code in the generic f will fail  
    // with C2248: 'Array<T>::size' :  
    // cannot access private member declared in class 'Array<T>'.  
    //friend void f<int>(Array<int>& a);  
  
    friend void f<>(Array<T>& a);  
};  
  
// f function template, friend of Array<T>  
template <class T>  
void f(Array<T>& a)  
{  
    cout << a.size << " generic" << endl;  
}  
  
// Specialization of f for int arrays  
// will be a friend because the template f is a friend.  
template<> void f(Array<int>& a)  
{  
    cout << a.size << " int" << endl;  
}  
  
int main()  
{  
    Array<char> ac(10);  
    f(ac);  
  
    Array<int> a(10);  
    f(a);  
}  
//Output:  
10 generic  
10 int  
```  
  
 下一个示例显示在类模板中声明的友元类模板。 该类模板随后用作友元类的模板参数。 友元类模板必须在声明它们的类模板外面定义。 友元模板的所有专用化或部分专用化也是原始类模板的友元。  
  
```cpp  
// template_friend3.cpp  
// compile with: /EHsc  
#include <iostream>  
using namespace std;  
  
template <class T>  
class X  
{  
private:  
   T* data;  
   void InitData(int seed) { data = new T(seed); }  
public:  
   void print() { cout << *data << endl; }  
   template <class U> friend class Factory;  
};  
  
template <class U>  
class Factory  
{  
public:  
   U* GetNewObject(int seed)  
   {  
      U* pu = new U;  
      pu->InitData(seed);  
      return pu;  
   }  
};  
  
int main()  
{  
   Factory< X<int> > XintFactory;  
   X<int>* x1 = XintFactory.GetNewObject(65);  
   X<int>* x2 = XintFactory.GetNewObject(97);  
  
   Factory< X<char> > XcharFactory;  
   X<char>* x3 = XcharFactory.GetNewObject(65);  
   X<char>* x4 = XcharFactory.GetNewObject(97);  
   x1->print();  
   x2->print();  
   x3->print();  
   x4->print();  
}  
//Output:   
65  
97  
A  
a  
```  
  
## <a name="reuse-of-template-parameters"></a>重复使用的模板参数  
 模板参数列表中，可以重复使用模板参数。 例如，以下代码是允许的：  
  
```cpp  
// template_specifications2.cpp  
  
class Y   
{  
};  
template<class T, T* pT> class X1   
{  
};  
template<class T1, class T2 = T1> class X2   
{  
};  
  
Y aY;  
  
X1<Y, &aY> x1;  
X2<int> x2;  
  
int main()  
{  
}  
```  
  
## <a name="see-also"></a>请参阅  
 [模板](../cpp/templates-cpp.md)
