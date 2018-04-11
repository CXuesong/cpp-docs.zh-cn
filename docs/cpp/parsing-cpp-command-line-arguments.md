---
title: "分析 C++ 命令行参数 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- quotation marks, command-line arguments
- double quotation marks
- command line [C++], parsing
- parsing, command-line arguments
- startup code, parsing command-line arguments
ms.assetid: e634e733-ac2f-4298-abe2-7e9288c94951
caps.latest.revision: "8"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 58db951b2c9459eb9511e0a354e1daec4b29b53d
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="parsing-c-command-line-arguments"></a>分析 C++ 命令行参数
**Microsoft 专用**  
  
 在解释操作系统命令行上给定参数时，Microsoft C/C++ 启动代码将使用以下规则：  
  
-   参数用空白分隔，空白可以是一个空格或制表符。  
  
-   插入符号 (^) 未被识别为转义符或者分隔符。 字符传递给之前由操作系统中的命令行分析器完全处理`argv`在程序中的数组。  
  
-   由双引号括起来的字符串 ("*字符串*") 被解释为单个参数，而不考虑包含空白。 带引号的字符串可以嵌入在参数内。  
  
-   前面有反斜杠的双引号 (\\") 被解释为原义双引号字符 (")。  
  
-   反斜杠按其原义解释，除非它们紧位于双引号之前。  
  
-   如果为偶数的反斜杠后跟双引号，一个反斜杠置于`argv`每对反斜杠，和双引号的数组将被解释为字符串分隔符。  
  
-   如果奇数数目的反斜杠后跟双引号，一个反斜杠置于`argv`每对反斜杠，和双引号的数组"进行转义"其余的反斜杠，导致原义双引号 (") 放入`argv`。  
  
## <a name="example"></a>示例  
 下面的程序演示如何命令行参数传递：  
  
```  
// command_line_arguments.cpp  
// compile with: /EHsc  
#include <iostream>  
  
using namespace std;  
int main( int argc,      // Number of strings in array argv  
          char *argv[],   // Array of command-line argument strings  
          char *envp[] )  // Array of environment variable strings  
{  
    int count;  
  
    // Display each command-line argument.  
    cout << "\nCommand-line arguments:\n";  
    for( count = 0; count < argc; count++ )  
         cout << "  argv[" << count << "]   "  
                << argv[count] << "\n";  
}  
```  
  
 下表显示了示例输入和预期的输出，演示前面列表中的规则。  
  
### <a name="results-of-parsing-command-lines"></a>分析命令行的结果  
  
|命令行输入|argv[1]|argv[2]|argv[3]|  
|-------------------------|---------------|---------------|---------------|  
|`"abc" d e`|`abc`|`d`|`e`|  
|`a\\b d"e f"g h`|`a\\b`|`de fg`|`h`|  
|`a\\\"b c d`|`a\"b`|`c`|`d`|  
|`a\\\\"b c" d e`|`a\\b c`|`d`|`e`|  
  
**结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [main：程序启动](../cpp/main-program-startup.md)