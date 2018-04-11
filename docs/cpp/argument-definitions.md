---
title: "参数定义 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- envp argument
- main function, arguments
- arguments [C++], for main function
- argv argument
- argc argument
ms.assetid: 6148cbf3-ebe8-44f2-b277-de4b723991c7
caps.latest.revision: "13"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: cd1bc4f017a90bf2f42972831eadc02e77868151
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="argument-definitions"></a>参数定义
原型中的参数  
  
```  
  
int main( int  
argc[ ,char*argv[] [,char*envp[] ] ] );intwmain(intargc[ ,wchar_t*argv[] [,wchar_t*envp[] ] ] );  
```  
  
 允许通过命令行分析参数并（可选）允许访问环境变量。 参数定义如下所示：  
  
 `argc`  
 包含 `argv` 后面的参数计数的整数。 `argc` 参数始终大于或等于 1。  
  
 `argv`  
 表示由杂注用户输入的命令行参数的以 null 结尾的字符串的数组。 按照约定， `argv` **[0]**是与其调用该程序，该命令`argv` **[1]**为第一个命令行参数，依此类推，直到`argv` **[**`argc`**]**，这始终是**NULL**。 请参阅[自定义命令行处理](../cpp/customizing-cpp-command-line-processing.md)有关取消命令行处理的信息。  
  
 第一个命令行参数始终是`argv` **[1]**和最后一项`argv` **[** `argc` -1**]**。  
  
> [!NOTE]
>  按照约定，`argv`[0] 是用于调用程序的命令。  但是，很可能生成进程使用[CreateProcess](http://msdn.microsoft.com/library/windows/desktop/ms683197)并且当你使用第一个和第二个参数 (`lpApplicationName`和`lpCommandLine`)， `argv` **[0]**可能不是可执行文件的名称;使用[GetModuleFileName](http://msdn.microsoft.com/library/windows/desktop/ms683197)检索可执行文件的名称和其完全限定路径。  
  
## <a name="microsoft-specific"></a>Microsoft 专用  
 `envp`  
 在 Microsoft C++ 中使用 `envp` 数组（它是许多 UNIX 系统中的常见扩展）。 它是表示用户环境中的变量集的字符串的数组。 终止此数组**NULL**条目。 它可以声明为指向的指针的数组**char (char** \*envp []**)**或指针指向**char (char** \* \*envp**)**。 如果程序使用**wmain**而不是**主要**，使用`wchar_t`数据类型而不是`char`。 环境块传递给**主要**和**wmain**是当前环境的"冻结"副本。 如果随后更改通过调用环境**putenv**或`_wputenv`，当前环境 (如返回`getenv` / `_wgetenv`和`_environ` /  `_wenviron`变量) 更改，但由 envp 指向的块将不会更改。 请参阅[自定义命令行处理](../cpp/customizing-cpp-command-line-processing.md)有关取消环境处理的信息。 此参数在 C 中是 ANSI 兼容的，但在 C++ 中不是这样。  
  
**结束 Microsoft 专用**  
  
## <a name="example"></a>示例  
 下面的示例演示如何使用`argc`， `argv`，和`envp`参数**主要**:  
  
```  
// argument_definitions.cpp  
// compile with: /EHsc  
#include <iostream>  
#include <string.h>  
  
using namespace std;  
int main( int argc, char *argv[], char *envp[] ) {  
    int iNumberLines = 0;    // Default is no line numbers.  
  
    // If /n is passed to the .exe, display numbered listing  
    // of environment variables.  
  
    if ( (argc == 2) && _stricmp( argv[1], "/n" ) == 0 )  
         iNumberLines = 1;  
  
    // Walk through list of strings until a NULL is encountered.  
    for( int i = 0; envp[i] != NULL; ++i ) {  
        if( iNumberLines )  
            cout << i << ": " << envp[i] << "\n";  
    }  
}  
```  
  
## <a name="see-also"></a>请参阅  
 [main：程序启动](../cpp/main-program-startup.md)