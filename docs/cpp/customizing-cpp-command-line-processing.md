---
title: "自定义 C++ 命令行处理 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- _setenvp
- _setargv
dev_langs: C++
helpviewer_keywords:
- command line [C++], processing
- command-line processing
- startup code, customizing command-line processing
- environment, environment-processing routine
- _setargv function
- command line [C++], processing arguments
- suppressing environment processing
- _setenvp function
ms.assetid: aae01cbb-892b-48b8-8e1f-34f22421f263
caps.latest.revision: "7"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 396f2a314c185f39593c92745346f988d666980f
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="customizing-c-command-line-processing"></a>自定义 C++ 命令行处理
## <a name="microsoft-specific"></a>Microsoft 专用  
 如果程序不采用命令行参数，则可以通过取消使用执行命令行处理的库例程来节省少量空间。 此例程称为**_setargv**和中所述[通配符扩展](../cpp/wildcard-expansion.md)。 若要禁止使用它，请定义不执行任何操作中包含的文件的例程**主要**函数，并将其命名**_setargv**。 调用**_setargv**然后按照你定义满足**_setargv**，并且不加载库版本。  
  
 同样，如果你永远不会访问环境表通过`envp`参数，你可以提供你自己的空例程用于代替了**_setenvp**，环境处理例程。 正如使用**_setargv**函数， **_setenvp**必须声明为**extern"C"**。  
  
 程序可以调用**spawn**或`exec`系列 C 运行时库中的例程。 如果是这样，则不应取消环境处理例程，因为可使用此例程将环境从父进程传递到子进程中。  
  
**结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [main：程序启动](../cpp/main-program-startup.md)