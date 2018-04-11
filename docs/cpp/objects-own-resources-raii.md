---
title: "对象拥有资源 （RAII） |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: article
dev_langs: C++
ms.assetid: f86b484e-5a27-4c3b-a92a-dfaa5dd6d93a
caps.latest.revision: "4"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 05e23cc81666086bc34352a351ead8006b6c859f
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="objects-own-resources-raii"></a>对象拥有资源 （RAII）
请确保对象拥有自己资源。 此原则是也称为“资源获取即是初始化”或“RAII”。
  
## <a name="example"></a>示例  
 将每个"new"的对象作为构造函数参数传递给拥有它 (几乎总是 unique_ptr) 的另一个已命名的对象。  
  
```cpp  
void f() {  
    unique_ptr<widget> p( new widget() );  
    my_class x( new widget() );  
    // ...  
} // automatic destruction and deallocation for both widget objects  
  // automatic exception safety, as if "finally { p->dispose(); x.w.dispose(); }"  
```  
  
 始终立即将任何新的资源传递给拥有它的另一个对象。  
  
```cpp  
void g() {  
    other_class y( OpenFile() );  
    // ...  
} // automatic closing and release for file resource  
  // automatic exception safety, as if "finally { y.file.dispose(); }"  
```  
  
## <a name="see-also"></a>请参阅  
 [欢迎回到 C++](../cpp/welcome-back-to-cpp-modern-cpp.md)   
 [C++ 语言参考](../cpp/cpp-language-reference.md)   
 [C++ 标准库](../standard-library/cpp-standard-library-reference.md)