---
title: "__vectorcall |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
ms.assetid: 1c95ed59-86c6-4857-b4ed-10519193f851
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 54c1473e2341c783ebf73883680d51f161d99163
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="vectorcall"></a>__vectorcall
**Microsoft 专用**  
  
 `__vectorcall` 调用约定指定尽可能在寄存器中传递函数的参数。 `__vectorcall`参数比使用多个寄存器[__fastcall](../cpp/fastcall.md)或默认值[x64 调用约定](../build/overview-of-x64-calling-conventions.md)使用。 `__vectorcall` 调用约定仅受到包括流式处理 SIMD 扩展 2 (SSE2) 和更高版本的 x86 和 x64 处理器上的本机代码的支持。 使用 `__vectorcall` 可使传递多个浮点或 SIMD 矢量参数的函数加速，并执行利用寄存器中加载的参数的操作。 以下列表显示了 `__vectorcall` 的 x86 和 x64 实现所共有的功能。 本文的后面部分解释了这些差异。  
  
|元素|实现|  
|-------------|--------------------|  
|C 名称修饰约定|函数名的前缀为两个“at”符号 (@@)，后接参数列表中的字节数（以十进制形式表示）。|  
|大小写转换约定|不执行任何大小写转换。|  
  
 使用[/Gv](../build/reference/gd-gr-gv-gz-calling-convention.md)编译器选项将导致每个函数中的模块编译为`__vectorcall`除非函数是成员函数、 利用冲突调用约定特性进行声明、 使用`vararg`变量参数列表或具有名称`main`。  
  
 可以将三种类型的参数传递通过寄存器在`__vectorcall`函数：*整数类型*值，*矢量类型*值，和*同类矢量聚合*(HVA) 值。  
  
 整数类型满足两个要求：它适合处理器的本机寄存器大小（例如，x86 计算机上的 4 个字节或 x64 计算机上的 8 个字节），而且它可以转换成与寄存器长度一样的整数，并且不用更改其位表示形式就能转换回来。 例如，所有可以提升到 x86 上的 `int`（x64 上的 `long long`）- 例如，`char` 或 `short`（或可转换为 `int`（x64 上的 `long long`））是整数类型，它们无需更改即可返回其原始类型。 整数类型包括指针、引用和小于或等于 4 个字节（x64 上为 8 个字节）的 `struct` 或 `union` 类型。 在 x64 平台上，更大的 `struct` 和 `union` 类型通过引用传递给调用方所分配的内存；在 x86 平台上，通过堆栈上的值传递它们。  
  
 矢量类型是一个浮点类型（例如，`float` 或 `double`）或一个 SIMD 矢量类型（例如，`__m128` 或 `__m256`）。  
  
 HVA 类型是复合类型，包含四个具有相同矢量类型的数据成员。 HVA 类型具有和其成员的矢量类型相同的对齐要求。 这是有关 HVA `struct` 定义的示例，该定义包含三个相同的矢量类型且具有 32 字节对齐方式：  
  
```cpp  
typedef struct {  
   __m256 x;  
   __m256 y;  
   __m256 z;  
} hva3;    // 3 element HVA type on __m256  
  
```  
  
 使用头文件中的 `__vectorcall` 关键字对函数进行显式声明，以使独立编译的代码在链接时不会出错。 函数必须为使用 `__vectorcall` 的原型，而不能使用 `vararg` 可变长度参数列表。  
  
 成员函数可以通过使用 `__vectorcall` 说明符进行声明。 通过寄存器传递隐藏的 `this` 指针作为第一个整数类型参数。  
  
 在 ARM 计算机上，`__vectorcall` 由编译器接受和忽略。  
  
 对于非静态类成员函数，如果函数是超行定义的，则调用约定修饰符不必在超行定义中指定。 也就是说，对于类非静态成员，在定义时假定声明期间指定的调用约定。 给定此类定义：  
  
```cpp  
struct MyClass {  
   void __vectorcall mymethod();  
};  
```  
  
 此：  
  
```cpp  
void MyClass::mymethod() { return; }  
```  
  
 等效于此：  
  
```cpp  
void __vectorcall MyClass::mymethod() { return; }  
```  
  
 必须在创建指向 `__vectorcall` 函数的指针时，指定`__vectorcall` 调用约定修饰符。 下一个示例针对指向 `typedef` 函数的指针创建 `__vectorcall`，该函数采用四个 `double` 参数并返回一个 `__m256` 值：  
  
```cpp  
typedef __m256 (__vectorcall * vcfnptr)(double, double, double, double);  
```  
  
## <a name="vectorcall-convention-on-x64"></a>x64 上的 __vectorcall 约定  
 x64 上的 `__vectorcall` 调用约定扩展标准 x64 调用约定以利用其他寄存器。 根据参数列表中的位置将整数类型参数和矢量类型参数映射到寄存器。 将 HVA 参数分配给未使用的矢量寄存器。  
  
 当前四个参数（按从左到右的顺序）都是整数类型参数时，它们将传递到对应该位置的寄存器，即 RCX、RDX、R8 或 R9。 隐藏的 `this` 指针被视为第一个整数类型参数。 当前四个参数之一中的 HVA 参数无法传入可用寄存器时，对调用方分配的内存的引用将传入相应的整数类型寄存器。 第四参数位置之后的整数类型参数在堆栈上传递。  
  
 当前六个参数（按从左到右的顺序）都是矢量类型参数时，将根据参数位置通过 0 到 5 的 SSE 矢量寄存器中的值传递它们。 浮点和 `__m128` 类型在 XMM 寄存器中传递，而 `__m256` 类型在 YMM 寄存器中传递。 这与标准 x64 调用约定不同，因为矢量类型是通过值而不是引用传递的，并且还使用了其他寄存器。 为矢量类型参数分配影子堆栈空间固定为 8 个字节，与[/homeparams](../build/reference/homeparams-copy-register-parameters-to-stack.md)选项不适用于。 在第七个参数位置和后面的参数位置的矢量类型参数在堆栈上通过对由调用方分配的内存的引用进行传递。  
  
 在为矢量参数分配寄存器后，只要整个 HVA 有足量的可用寄存器，HVA 参数的数据成员就会以升序分配给未使用的矢量寄存器 XMM0 至 XMM5（或者，对于 `__m256` 类型，为 YMM0 至 YMM5）。 如果没有足够多的可用寄存器，HVA 参数将通过对由调用方分配的内存的引用进行传递。 HVA 参数的影子堆栈空间固定为 8 个字节，且带有未定义的内容。 在参数列表中按从左到右的顺序将 HVA 参数分配给寄存器，并且这些参数可处于任意位置。 位于未分配给矢量寄存器的前四个参数位置中的任一位置的 HVA 参数将通过由对应于该位置的整数寄存器中的引用进行传递。 在第四个参数位置后通过引用传递的 HVA 参数将被推入堆栈。  
  
 如果可能，`__vectorcall` 函数的结果将由寄存器中的值返回。 整数类型的结果（包括小于或等于 8 字节的结构或联合）按 RAX 中的值返回。 根据大小，矢量类型结果在 XMM0 或 YMM0 中按值返回。 HVA 结果具有每个由 XMM0:XMM3 或 YMM0:YMM3 寄存器中的值返回的数据元素，取决于元素大小。 不适合对应的寄存器的结果类型将通过对由调用方分配的内存的引用返回。  
  
 堆栈在 `__vectorcall` 的 x64 实现中由调用方保持。 调用方 prolog 和 epilog 代码分配并清除被调用函数的堆栈。 参数从右向左推入堆栈；并且为传递到寄存器中的参数分配影子堆栈空间。  
  
 示例：  
  
```cpp  
// crt_vc64.c  
// Build for amd64 with: cl /arch:AVX /W3 /FAs crt_vc64.c  
// This example creates an annotated assembly listing in  
// crt_vc64.asm.  
  
#include <intrin.h>  
#include <xmmintrin.h>  
  
typedef struct {  
   __m128 array[2];  
} hva2;    // 2 element HVA type on __m128  
  
typedef struct {  
   __m256 array[4];  
} hva4;    // 4 element HVA type on __m256  
  
// Example 1: All vectors  
// Passes a in XMM0, b in XMM1, c in YMM2, d in XMM3, e in YMM4.  
// Return value in XMM0.  
__m128 __vectorcall   
example1(__m128 a, __m128 b, __m256 c, __m128 d, __m256 e) {  
   return d;  
}  
  
// Example 2: Mixed int, float and vector parameters  
// Passes a in RCX, b in XMM1, c in R8, d in XMM3, e in YMM4,   
// f in XMM5, g pushed on stack.   
// Return value in YMM0.  
__m256 __vectorcall   
example2(int a, __m128 b, int c, __m128 d, __m256 e, float f, int g) {  
   return e;  
}  
  
// Example 3: Mixed int and HVA parameters  
// Passes a in RCX, c in R8, d in R9, and e pushed on stack.  
// Passes b by element in [XMM0:XMM1];   
// b's stack shadow area is 8-bytes of undefined value.   
// Return value in XMM0.  
__m128 __vectorcall example3(int a, hva2 b, int c, int d, int e) {  
   return b.array[0];  
}  
  
// Example 4: Discontiguous HVA   
// Passes a in RCX, b in XMM1, d in XMM3, and e is pushed on stack.  
// Passes c by element in [YMM0,YMM2,YMM4,YMM5], discontiguous because  
// vector arguments b and d were allocated first.   
// Shadow area for c is an 8-byte undefined value.  
// Return value in XMM0.  
float __vectorcall example4(int a, float b, hva4 c, __m128 d, int e) {  
   return b;  
}  
  
// Example 5: Multiple HVA arguments  
// Passes a in RCX, c in R8, e pushed on stack.  
// Passes b in [XMM0:XMM1], d in [YMM2:YMM5], each with   
// stack shadow areas of an 8-byte undefined value.  
// Return value in RAX.  
int __vectorcall example5(int a, hva2 b, int c, hva4 d, int e) {  
   return c + e;  
}  
  
// Example 6: HVA argument passed by reference, returned by register  
// Passes a in [XMM0:XMM1], b passed by reference in RDX, c in YMM2,   
// d in [XMM3:XMM4].   
// Register space was insufficient for b, but not for d.  
// Return value in [YMM0:YMM3].  
hva4 __vectorcall example6(hva2 a, hva4 b, __m256 c, hva2 d) {  
   return b;  
}  
  
int __cdecl main( void )  
{  
   hva4 h4;  
   hva2 h2;  
   int i;  
   float f;  
   __m128 a, b, d;  
   __m256 c, e;  
  
   a = b = d = _mm_set1_ps(3.0f);  
   c = e = _mm256_set1_ps(5.0f);  
   h2.array[0] = _mm_set1_ps(6.0f);  
   h4.array[0] = _mm256_set1_ps(7.0f);  
  
   b = example1(a, b, c, d, e);  
   e = example2(1, b, 3, d, e, 6.0f, 7);  
   d = example3(1, h2, 3, 4, 5);  
   f = example4(1, 2.0f, h4, d, 5);  
   i = example5(1, h2, 3, h4, 5);  
   h4 = example6(h2, h4, c, h2);  
}  
  
```  
  
## <a name="vectorcall-convention-on-x86"></a>x86 上的 __vectorcall 约定  
 `__vectorcall` 调用约定遵循 32 位整数类型参数的 `__fastcall` 约定，并利用矢量类型和 HVA 参数的 SSE 矢量寄存器。  
  
 将在参数列表中从左至右发现的前两个整数类型参数分别放入 ECX 和 EDX 中。 隐藏的 `this` 指针被视为第一个整数类型参数，并且被传入 ECX。 前六个矢量类型参数通过 XMM 或 YMM 寄存器中的 SSE 矢量寄存器 0 到 5（具体取决于参数大小）中的值传递。  
  
 前六个矢量类型参数（按从左至右的顺序）按 SSE 矢量寄存器 0 到 5 中的值传递。 浮点和 `__m128` 类型在 XMM 寄存器中传递，而 `__m256` 类型在 YMM 寄存器中传递。 不会为寄存器传入的矢量类型参数分配影子堆栈空间。 第七个矢量类型参数和后面的矢量类型参数在堆栈上通过对由调用方分配的内存的引用进行传递。 编译器错误限制[C2719](../error-messages/compiler-errors-2/compiler-error-c2719.md)不适用于这些参数。  
  
 在为矢量参数分配寄存器后，只要整个 HVA 有足量的可用寄存器，HVA 参数的数据成员就会以升序分配给未使用的矢量寄存器 XMM0 至 XMM5（或者，对于 `__m256` 类型，为 YMM0 至 YMM5）。 如果没有足量的可用寄存器，则 HVA 参数将在堆栈上通过对由调用方分配的内存的引用进行传递。 不会为 HVA 参数分配堆栈影子空间。 在参数列表中按从左到右的顺序将 HVA 参数分配给寄存器，并且这些参数可处于任意位置。  
  
 如果可能，`__vectorcall` 函数的结果将由寄存器中的值返回。 整数类型的结果（包括小于或等于 4 字节的结构或联合）按 EAX 中的值返回。 小于或等于 8 字节的整数类型结构或联合由 EDX:EAX 中的值返回。 根据大小，矢量类型结果在 XMM0 或 YMM0 中按值返回。 HVA 结果具有每个由 XMM0:XMM3 或 YMM0:YMM3 寄存器中的值返回的数据元素，取决于元素大小。 其他结果类型通过对由调用方分配的内存的引用返回。  
  
 `__vectorcall` 的 x86 实现遵循由调用方从右向左推入堆栈的参数的约定，而调用的函数可在其返回之前清除堆栈。 仅将未置于寄存器上的参数推入堆栈。  
  
 示例：  
  
```cpp  
// crt_vc86.c  
// Build for x86 with: cl /arch:AVX /W3 /FAs crt_vc86.c  
// This example creates an annotated assembly listing in  
// crt_vc86.asm.  
  
#include <intrin.h>  
#include <xmmintrin.h>  
  
typedef struct {  
   __m128 array[2];  
} hva2;    // 2 element HVA type on __m128  
  
typedef struct {  
   __m256 array[4];  
} hva4;    // 4 element HVA type on __m256  
  
// Example 1: All vectors  
// Passes a in XMM0, b in XMM1, c in YMM2, d in XMM3, e in YMM4.  
// Return value in XMM0.  
__m128 __vectorcall   
example1(__m128 a, __m128 b, __m256 c, __m128 d, __m256 e) {  
   return d;  
}  
  
// Example 2: Mixed int, float and vector parameters  
// Passes a in ECX, b in XMM0, c in EDX, d in XMM1, e in YMM2,   
// f in XMM3, g pushed on stack.   
// Return value in YMM0.  
__m256 __vectorcall   
example2(int a, __m128 b, int c, __m128 d, __m256 e, float f, int g) {  
   return e;  
}  
  
// Example 3: Mixed int and HVA parameters  
// Passes a in ECX, c in EDX, d and e pushed on stack.  
// Passes b by element in [XMM0:XMM1].   
// Return value in XMM0.  
__m128 __vectorcall example3(int a, hva2 b, int c, int d, int e) {  
   return b.array[0];  
}  
  
// Example 4: HVA assigned after vector types  
// Passes a in ECX, b in XMM0, d in XMM1, and e in EDX.  
// Passes c by element in [YMM2:YMM5].   
// Return value in XMM0.  
float __vectorcall example4(int a, float b, hva4 c, __m128 d, int e) {  
   return b;  
}  
  
// Example 5: Multiple HVA arguments  
// Passes a in ECX, c in EDX, e pushed on stack.  
// Passes b in [XMM0:XMM1], d in [YMM2:YMM5].  
// Return value in EAX.  
int __vectorcall example5(int a, hva2 b, int c, hva4 d, int e) {  
   return c + e;  
}  
  
// Example 6: HVA argument passed by reference, returned by register  
// Passes a in [XMM1:XMM2], b passed by reference in ECX, c in YMM0,   
// d in [XMM3:XMM4].   
// Register space was insufficient for b, but not for d.  
// Return value in [YMM0:YMM3].  
hva4 __vectorcall example6(hva2 a, hva4 b, __m256 c, hva2 d) {  
   return b;  
}  
  
int __cdecl main( void )  
{  
   hva4 h4;  
   hva2 h2;  
   int i;  
   float f;  
   __m128 a, b, d;  
   __m256 c, e;  
  
   a = b = d = _mm_set1_ps(3.0f);  
   c = e = _mm256_set1_ps(5.0f);  
   h2.array[0] = _mm_set1_ps(6.0f);  
   h4.array[0] = _mm256_set1_ps(7.0f);  
  
   b = example1(a, b, c, d, e);  
   e = example2(1, b, 3, d, e, 6.0f, 7);  
   d = example3(1, h2, 3, 4, 5);  
   f = example4(1, 2.0f, h4, d, 5);  
   i = example5(1, h2, 3, h4, 5);  
   h4 = example6(h2, h4, c, h2);  
}  
  
```  
  
 **结束 Microsoft 专用**  
  
## <a name="see-also"></a>请参阅  
 [参数传递和命名约定](../cpp/argument-passing-and-naming-conventions.md)   
 [关键字](../cpp/keywords-cpp.md)