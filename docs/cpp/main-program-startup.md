---
title: "main： 程序启动 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- vc.main.startup
- _tmain
dev_langs: C++
helpviewer_keywords:
- program startup [C++]
- entry points, program
- wmain function
- _tmain function
- startup code, main function
- main function, program startup
ms.assetid: f9581cd6-93f7-4bcd-99ec-d07c3c107dd4
caps.latest.revision: "9"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 1fbb3d19101358012df795000907a0b3e8139601
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="main-program-startup"></a>main：程序启动
一个名为的特殊函数`main`是所有 C 和 C++ 程序的执行的起始点。 如果您要编写遵循 [!INCLUDE[TLA#tla_unicode](../atl-mfc-shared/reference/includes/tlasharptla_unicode_md.md)] 编程模型的代码，则可使用 `wmain`（它是 `main` 的宽字符版本）。  
  
 `main` 函数未由编译器预定义。 必须在程序文本中提供此函数。  
  
 `main` 的声明语法为  
  
```  
int main();  
```  
  
 或（可选）  
  
```  
int main(int argc, char *argv[], char *envp[]);  
```  
  
## <a name="microsoft-specific"></a>Microsoft 专用  
 `wmain` 的声明语法如下所示：  
  
```  
int wmain( );  
```  
  
 或（可选）  
  
```  
int wmain(int argc, wchar_t *argv[], wchar_t *envp[]);  
```  
  
 还可以使用 TCHAR.h 中定义的 `_tmain`。 除非定义了 _UNICODE，否则 `_tmain` 将解析为 `main`。 在该示例中，`_tmain` 将解析为 `wmain`。  
  
 或者，`main` 和 `wmain` 函数可以声明为返回 `void`（没有返回值）。 如果你声明`main`或`wmain`为返回`void`，你不能通过将退出代码返回到父进程或操作系统[返回](../cpp/return-statement-in-program-termination-cpp.md)语句。 若要返回退出代码时`main`或`wmain`声明为`void`，必须使用[退出](../cpp/exit-function.md)函数。  
  
**结束 Microsoft 专用**  
 `argc` 和 `argv` 的类型由语言定义。 名称 `argc`、`argv` 和 `envp` 是传统的，但编译器不需要这些名称。 有关详细信息及示例，请参阅[参数定义](../cpp/argument-definitions.md)。  
  
## <a name="see-also"></a>请参阅  
 [关键字](../cpp/keywords-cpp.md)   
 [使用 wmain 代替 main](../cpp/using-wmain-instead-of-main.md)   
 [main 函数限制](../cpp/main-function-restrictions.md)