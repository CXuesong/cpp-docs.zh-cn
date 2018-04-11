---
title: "二元运算符 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- member-selection operators [C++]
- operators [C++], binary
- binary operators [C++]
ms.assetid: c0e7fbff-bc87-4708-8333-504ac09ee83e
caps.latest.revision: "7"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 15c52d48359210a21b23caa72afee7e2a3bcd8cc
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="binary-operators"></a>二元运算符
下表显示可重载的运算符的列表。  
  
### <a name="redefinable-binary-operators"></a>可重新定义的二进制运算符  
  
|运算符|name|  
|--------------|----------|  
|**，**|逗号|  
|`!=`|不相等|  
|`%`|取模|  
|`%=`|取模/赋值|  
|**&**|按位“与”|  
|**&&**|逻辑“与”|  
|**&=**|按位“与”/赋值|  
|**\***|乘法|  
|**\*=**|乘法/赋值|  
|**+**|添加|  
|`+=`|加法/赋值|  
|**-**|减法|  
|**-=**|减法/赋值|  
|**->**|成员选择|  
|**->\***|指向成员的指针选定内容|  
|**/**|除号|  
|`/=`|除法/赋值|  
|**<**|小于|  
|**<<**|左移|  
|**<<=**|左移/赋值|  
|**<=**|小于或等于|  
|**=**|赋值|  
|`==`|相等|  
|**>**|大于|  
|**>=**|大于或等于|  
|**>>**|右移|  
|**>>=**|右移/赋值|  
|**^**|异或|  
|`^=`|异或/赋值|  
|**&#124;**|按位“与或”|  
|`&#124;=`|按位“与或”/赋值|  
|`&#124;&#124;`|逻辑“或”|  
  
 若要将二元运算符函数声明为非静态成员，您必须用以下形式声明它：  
  
 *ret 类型***运算符**`op`**(** `arg` **)**  
  
 其中*ret 类型*是返回类型，`op`是上表中中, 列出的运算符之一和`arg`是任何类型的参数。  
  
 若要将二元运算符函数声明为全局函数，您必须用以下形式声明它：  
  
 *ret 类型***运算符**`op`**(** `arg1` **，** `arg2` **)**  
  
 其中*ret 类型*和`op`如上所述用于成员运算符函数和`arg1`和`arg2`是参数。 至少有一个参数必须是类类型。  
  
> [!NOTE]
>  对二元运算符的返回类型没有限制；但是，大多数用户定义的二元运算符将返回类类型或对类类型的引用。  
  
## <a name="see-also"></a>请参阅  
 [运算符重载](../cpp/operator-overloading.md)