---
title: "_com_error:: wcode |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: _com_error::WCode
dev_langs: C++
helpviewer_keywords: WCode method [C++]
ms.assetid: f3b21852-f8ea-4e43-bff1-11c2d35454c4
caps.latest.revision: "6"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 74f79491b9d4f88e19bcd0f7a20c94b26d0f7909
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="comerrorwcode"></a>_com_error::WCode
**Microsoft 专用**  
  
 检索映射到封装的 `HRESULT` 中的 16 位错误代码。  
  
## <a name="syntax"></a>语法  
  
```  
  
WORD WCode ( ) const throw( );  
  
```  
  
## <a name="return-value"></a>返回值  
 如果`HRESULT`在范围 0x80040200 到 0x8004ffff 内内, **WCode**方法返回`HRESULT`减去 0x80040200; 否则，它将返回零。  
  
## <a name="remarks"></a>备注  
 **WCode**方法用于撤消发生在 COM 支持代码中的映射。 包装器**调度接口**属性或方法调用的参数和调用包的支持例程**idispatch:: Invoke**。 返回后，如果失败`HRESULT`的`DISP_E_EXCEPTION`返回，则错误将检索信息从**EXCEPINFO**结构传递给**idispatch:: Invoke**。 错误代码可以是存储在一个 16 位值`wCode`的成员**EXCEPINFO**结构或中的完整的 32 位值**scode**的成员**EXCEPINFO**结构。 如果返回了 16 位 `wCode`，则必须先将其映射到 32 位失败 `HRESULT`。  
  
 **结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [_com_error::HRESULTToWCode](../cpp/com-error-hresulttowcode.md)   
 [_com_error::WCodeToHRESULT](../cpp/com-error-wcodetohresult.md)   
 [_com_error 类](../cpp/com-error-class.md)