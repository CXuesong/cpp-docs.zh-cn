---
title: "通配符扩展 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: _setargv
dev_langs: C++
helpviewer_keywords:
- asterisk wildcard
- _setargv function
- command line [C++], processing arguments
- command line [C++], wildcards
- command-line wildcards
- question mark, wildcard
ms.assetid: 1a543398-607b-4404-93d1-45d290bde638
caps.latest.revision: "9"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 2c9bbb5dcc706e08b2b985769248969f9bd23201
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="wildcard-expansion"></a>通配符扩展
## <a name="microsoft-specific"></a>Microsoft 专用  
 可以使用通配符（问号 (?) 和星号 (*)）在命令行上指定文件名和路径参数。  
  
 命令行参数由调用的例程**_setargv** (或**_wsetargv**宽字符环境中)，后者的默认设置不将通配符展开到单独字符串中`argv`字符串数组。 有关启用通配符扩展的详细信息，请参阅[展开通配符参数](../c-language/expanding-wildcard-arguments.md)。  
  
**结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [main：程序启动](../cpp/main-program-startup.md)