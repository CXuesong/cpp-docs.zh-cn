---
title: "本机 C++ 中的事件处理 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords: event handling [C++], Visual C++
ms.assetid: e4b9219a-15d8-42fb-83c8-6d2e4e087c8d
caps.latest.revision: "9"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 157b31f244ce5400aac5857f2473deb67938d8d0
ms.sourcegitcommit: 9a0a287d6940591523af959ebdac5affa36220da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="event-handling-in-native-c"></a>本机 C++ 中的事件处理

在本机 C++ 事件处理中，你将设置事件源和事件接收器使用[event_source](../windows/event-source.md)和[event_receiver](../windows/event-receiver.md)属性，分别指定`type` = `native`. 这些特性允许应用它们的类在本机的非 COM 上下文中激发和处理事件。

## <a name="declaring-events"></a>声明事件

在事件源类中，使用[__event](../cpp/event.md)方法声明来声明为事件的方法上的关键字。 请确保声明该方法，但不要定义它；这样做会产生编译器错误，因为将该方法转换为事件时编译器会隐式定义它。 本机事件可以是带有零个或多个参数的方法。 返回类型可以是 void 或任何整型。  
  
## <a name="defining-event-handlers"></a>定义事件处理程序

在事件接收器类中，可定义事件处理程序，这些处理程序是具有与它们将处理的事件匹配的签名（返回类型、调用约定和参数）的方法。  
  
## <a name="hooking-event-handlers-to-events"></a>将事件处理程序挂钩到事件  

此外在事件接收器类中，使用内部函数[__hook](../cpp/hook.md)若要将事件与事件处理程序关联和[__unhook](../cpp/unhook.md)取消事件与事件处理程序。 您可将多个事件挂钩到一个事件处理程序，或将多个事件处理程序挂钩到一个事件。  
  
## <a name="firing-events"></a>激发事件  

若要激发事件，只需调用声明为事件源类中的事件的方法即可。 如果处理程序已挂钩到事件，则将调用处理程序。  
  
### <a name="native-c-event-code"></a>本机 C++ 事件代码  

以下示例演示如何在本机 C++ 中激发事件。 若要编译并运行此示例，请参考代码中的注释。  
  
## <a name="example"></a>示例  
  
### <a name="code"></a>代码  
  
```cpp  
// evh_native.cpp  
#include <stdio.h>  
  
[event_source(native)]  
class CSource {  
public:  
   __event void MyEvent(int nValue);  
};  
  
[event_receiver(native)]  
class CReceiver {  
public:  
   void MyHandler1(int nValue) {  
      printf_s("MyHandler1 was called with value %d.\n", nValue);  
   }  
  
   void MyHandler2(int nValue) {  
      printf_s("MyHandler2 was called with value %d.\n", nValue);  
   }  
  
   void hookEvent(CSource* pSource) {  
      __hook(&CSource::MyEvent, pSource, &CReceiver::MyHandler1);  
      __hook(&CSource::MyEvent, pSource, &CReceiver::MyHandler2);  
   }  
  
   void unhookEvent(CSource* pSource) {  
      __unhook(&CSource::MyEvent, pSource, &CReceiver::MyHandler1);  
      __unhook(&CSource::MyEvent, pSource, &CReceiver::MyHandler2);  
   }  
};  
  
int main() {  
   CSource source;  
   CReceiver receiver;  
  
   receiver.hookEvent(&source);  
   __raise source.MyEvent(123);  
   receiver.unhookEvent(&source);  
}  
```  
  
### <a name="output"></a>输出  
  
```Output
MyHandler2 was called with value 123.  
MyHandler1 was called with value 123.  
```  
  
## <a name="see-also"></a>请参阅

[事件处理](../cpp/event-handling.md)  

