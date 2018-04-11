---
title: "COM 中的事件处理 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- event handling [C++], COM
- event handling [C++], about event handling
- declaring events
- event handlers [C++], COM
- event handlers
- COM, events
- event receivers, in event handling
- event handling [C++]
- hooking events
- event receivers, name and signature matching
- event sources, in event handling
- declaring events, in COM
- declaring events, event handling in COM
ms.assetid: 6b4617d4-a58e-440c-a8a6-1ad1c715b2bb
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 1c57b8429a05ab3989dce318f4c16a58475560a1
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="event-handling-in-com"></a>COM 中的事件处理
在 COM 事件处理中，你将设置事件源和事件接收器使用[event_source](../windows/event-source.md)和[event_receiver](../windows/event-receiver.md)属性，分别指定`type` = **com**。这些特性为自定义接口、调度接口和双重接口注入相应的代码，从而使这些接口能够应用到的类激发事件并通过 COM 连接点处理事件。  
  
## <a name="declaring-events"></a>声明事件  
 在事件源类中，使用[__event](../cpp/event.md)在接口声明以该接口将方法声明为事件的关键字。 当您将该接口的事件作为接口方法调用时，将激发这些事件。 事件接口上的方法可以具有零个或多个参数 (应全是**中**参数)。 返回类型可以是 void 或任何整型。  
  
## <a name="defining-event-handlers"></a>定义事件处理程序  
 在事件接收器类中，可定义事件处理程序，这些处理程序是具有与它们将处理的事件匹配的签名（返回类型、调用约定和参数）的方法。 对于 COM 事件，调用约定不必匹配;请参阅[布局依赖于 COM 事件](#vcconeventhandlingincomanchorlayoutdependentcomevents)下面有关详细信息。  
  
## <a name="hooking-event-handlers-to-events"></a>将事件处理程序挂钩到事件  
 此外在事件接收器类中，使用内部函数[__hook](../cpp/hook.md)若要将事件与事件处理程序关联和[__unhook](../cpp/unhook.md)取消事件与事件处理程序。 您可将多个事件挂钩到一个事件处理程序，或将多个事件处理程序挂钩到一个事件。  
  
> [!NOTE]
>  通常，有两种方法使 COM 事件接收器能够访问事件源接口定义。 第一种是共享公共头文件，如下所示。 第二个是使用[#import](../preprocessor/hash-import-directive-cpp.md)与`embedded_idl`导入限定符结合，以便让事件源类型库写入保留了特性生成的代码的.tlh 文件。  
  
## <a name="firing-events"></a>激发事件  
 若要激发事件，只需调用在事件源类中使用 `__event` 关键字声明的接口中的方法。 如果处理程序已挂钩到事件，则将调用处理程序。  
  
### <a name="com-event-code"></a>COM 事件代码  
 下面的示例演示如何在 COM 类中激发事件。 若要编译并运行此示例，请参考代码中的注释。  
  
```  
// evh_server.h  
#pragma once  
  
[ dual, uuid("00000000-0000-0000-0000-000000000001") ]  
__interface IEvents {  
   [id(1)] HRESULT MyEvent([in] int value);  
};  
  
[ dual, uuid("00000000-0000-0000-0000-000000000002") ]  
__interface IEventSource {  
   [id(1)] HRESULT FireEvent();  
};  
  
class DECLSPEC_UUID("530DF3AD-6936-3214-A83B-27B63C7997C4") CSource;  
```  
  
 接着是服务器：  
  
```  
// evh_server.cpp  
// compile with: /LD  
// post-build command: Regsvr32.exe /s evh_server.dll  
#define _ATL_ATTRIBUTES 1  
#include <atlbase.h>  
#include <atlcom.h>  
#include "evh_server.h"  
  
[ module(dll, name="EventSource", uuid="6E46B59E-89C3-4c15-A6D8-B8A1CEC98830") ];  
  
[coclass, event_source(com), uuid("530DF3AD-6936-3214-A83B-27B63C7997C4")]  
class CSource : public IEventSource {  
public:  
   __event __interface IEvents;   
  
   HRESULT FireEvent() {  
      __raise MyEvent(123);  
      return S_OK;  
   }  
};  
```  
  
 再然后是客户端：  
  
```  
// evh_client.cpp  
// compile with: /link /OPT:NOREF  
#define _ATL_ATTRIBUTES 1  
#include <atlbase.h>  
#include <atlcom.h>  
#include <stdio.h>  
#include "evh_server.h"  
  
[ module(name="EventReceiver") ];  
  
[ event_receiver(com) ]  
class CReceiver {  
public:  
   HRESULT MyHandler1(int nValue) {  
      printf_s("MyHandler1 was called with value %d.\n", nValue);  
      return S_OK;  
   }  
  
   HRESULT MyHandler2(int nValue) {  
      printf_s("MyHandler2 was called with value %d.\n", nValue);  
      return S_OK;  
   }  
  
   void HookEvent(IEventSource* pSource) {  
      __hook(&IEvents::MyEvent, pSource, &CReceiver::MyHandler1);  
      __hook(&IEvents::MyEvent, pSource, &CReceiver::MyHandler2);  
   }  
  
   void UnhookEvent(IEventSource* pSource) {  
      __unhook(&IEvents::MyEvent, pSource, &CReceiver::MyHandler1);  
      __unhook(&IEvents::MyEvent, pSource, &CReceiver::MyHandler2);  
   }  
};  
  
int main() {  
   // Create COM object  
   CoInitialize(NULL);  
   {  
      IEventSource* pSource = 0;  
      HRESULT hr = CoCreateInstance(__uuidof(CSource), NULL,         CLSCTX_ALL, __uuidof(IEventSource), (void **) &pSource);  
      if (FAILED(hr)) {  
         return -1;  
      }  
  
      // Create receiver and fire event  
      CReceiver receiver;  
      receiver.HookEvent(pSource);  
      pSource->FireEvent();  
      receiver.UnhookEvent(pSource);  
   }  
   CoUninitialize();  
   return 0;  
}  
```  
  
### <a name="output"></a>输出  
  
```  
MyHandler1 was called with value 123.  
MyHandler2 was called with value 123.  
```  
  
##  <a name="vcconeventhandlingincomanchorlayoutdependentcomevents"></a>布局取决于 COM 事件  
 布局依赖性只是 COM 编程中的一个问题。 在本机和托管事件处理中，处理程序的签名（返回类型、调用约定和参数）必须与其事件匹配，但处理程序的名称不必与其事件匹配。  
  
 但是，在 COM 事件处理中，当你设置*layout_dependent*参数**event_receiver**到**true**，强制执行的名称和签名匹配。 这意味着事件接收器中处理程序的名称和签名必须与处理程序将挂钩到的事件的名称和签名完全匹配。  
  
 当*layout_dependent*设置为**false**，调用约定和存储类 （虚拟、 静态的等等） 可以混合和匹配之间激发事件方法并与挂钩方法 （其委托）。 它是效率稍有提高具有*layout_dependent*=**true**。  
  
 例如，假设 `IEventSource` 定义为具有下列方法：  
  
```  
[id(1)] HRESULT MyEvent1([in] int value);  
[id(2)] HRESULT MyEvent2([in] int value);  
```  
  
 假定事件源具有以下形式：  
  
```  
[coclass, event_source(com)]  
class CSource : public IEventSource {  
public:  
   __event __interface IEvents;  
  
   HRESULT FireEvent() {  
      MyEvent1(123);  
      MyEvent2(123);  
      return S_OK;  
   }  
};  
```  
  
 则在事件接收器中，挂钩到 `IEventSource` 中的方法的任何处理程序必须与其名称和签名匹配，如下所示：  
  
```  
[coclass, event_receiver(com, true)]  
class CReceiver {  
public:  
   HRESULT MyEvent1(int nValue) {  // name and signature matches MyEvent1  
      ...  
   }  
   HRESULT MyEvent2(E c, char* pc) {  // signature doesn't match MyEvent2  
      ...  
   }  
   HRESULT MyHandler1(int nValue) {  // name doesn't match MyEvent1 (or 2)  
      ...  
   }  
   void HookEvent(IEventSource* pSource) {  
      __hook(IFace, pSource);  // Hooks up all name-matched events   
                               // under layout_dependent = true  
      __hook(&IFace::MyEvent1, pSource, &CReceive::MyEvent1);   // valid  
      __hook(&IFace::MyEvent2, pSource, &CSink::MyEvent2);   // not valid  
      __hook(&IFace::MyEvent1, pSource, &CSink:: MyHandler1); // not valid  
   }  
};  
```  
  
## <a name="see-also"></a>请参阅  
 [事件处理](../cpp/event-handling.md)