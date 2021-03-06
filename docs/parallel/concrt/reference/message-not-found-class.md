---
title: "message_not_found 类 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-windows
ms.tgt_pltfrm: 
ms.topic: article
f1_keywords:
- message_not_found
- CONCRT/concurrency::message_not_found
- CONCRT/concurrency::message_not_found::message_not_found
dev_langs: C++
helpviewer_keywords: message_not_found class
ms.assetid: a96b9995-5ad7-4600-83c8-c15e329ff10e
caps.latest.revision: "19"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 245f62879d1f44c7363b13f369b9f834231f6678
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="messagenotfound-class"></a>message_not_found 类
此类描述消息块找不到请求的消息时引发的异常。  
  
## <a name="syntax"></a>语法  
  
```
class message_not_found : public std::exception;
```  
  
## <a name="members"></a>成员  
  
### <a name="public-constructors"></a>公共构造函数  
  
|名称|描述|  
|----------|-----------------|  
|[message_not_found](#ctor)|已重载。 构造 `message_not_found` 对象。|  
  
## <a name="inheritance-hierarchy"></a>继承层次结构  
 `exception`  
  
 `message_not_found`  
  
## <a name="requirements"></a>惠?  
 **标头：** concrt.h  
  
 **命名空间：** 并发  
  
##  <a name="ctor"></a>message_not_found 

 构造 `message_not_found` 对象。  
  
```
explicit _CRTIMP message_not_found(_In_z_ const char* _Message) throw();

message_not_found() throw();
```  
  
### <a name="parameters"></a>参数  
 `_Message`  
 错误的描述性消息。  
  
## <a name="see-also"></a>请参阅  
 [并发 Namespace](concurrency-namespace.md)   
 [异步消息块](../../../parallel/concrt/asynchronous-message-blocks.md)



