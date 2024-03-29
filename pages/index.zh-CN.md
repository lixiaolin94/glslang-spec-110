# 简介

> 注意:这个文档的版本号与语言版本号分开跟踪。改变文档的修订号不会改变语言的版本号。本文档规定了 OpenGL 着色语言的 1.10 版本，文档修订号为 59。它要求 \_\_VERSION\_\_ 为 110，且 `#version` 接受 110。

## 自 1.051 版本以来的变化

- 添加了问题 101 到 105。根据这些问题做出的规范改动是让数组参数有大小，并对构造函数加了一些限制。参见第 4.2 节、第 5.4.2 节、第 6.1 节和第 6.1.1 节。
- 添加了与 ATI_draw_buffers 和 ARB_color_clamp_control 的交互，特别是输出变量`gl_FragData[n]`。
- 3.3 添加了 `#version` 和 `#extension` 来声明版本和扩展。
- 7.5 为矩阵的逆矩阵和转置矩阵添加了内置状态。
- 8 添加了内置函数`refract`、`exp`和`log`。
- 添加了以下澄清和修正:
  - 2.1 从顶点处理器的功能列表中删除了“颜色限幅”。这只是过时了。
  - 2.1 将“透视投影”更清晰地称为投影变换和透视除法，它们属于不同的列表。
  - 3.3 保留以"GL\_"开头的预处理宏。
  - 3.6 添加保留字`packed`、`this`、`interface`、`sampler2DRectShadow`。还澄清了所列的关键字和保留字是仅有的。
  - 4.1.5 删除“可以使用整数向量从纹理读取中获取多个整数”。这只是过时了。
  - 4.3.5 澄清结构可以是常量，且`const`必须使用什么初始化。
  - 5.8 澄清`\*=`、`+=`等的真正含义。还有`?:`不是左值。
  - 5.9 澄清整数之间的运算与浮点数一样是允许的，且列表是列出所有运算符和表达式。
  - 6.1 修正点积原型的示例。它们与原型列表不一致，而原型列表本身已经正确了一段时间。
  - 6.1 添加了这样一个说明:"如果一个内置函数在调用它之前在着色器中重新声明(即一个原型是可见的)，那么链接器将只在链接在一起的着色器集合中解析该调用。"
  - 7.2 删除过时的文本"对于使用相同源代码表达式计算深度的着色器，实现将提供不变的结果，但在着色器和固定功能之间不提供不变性。"
  - 7.4 更正内置常量名称列表:删除后缀并将值更新。
  - 8.2 说明指数函数的定义域。
  - 8.3 将`step()`改为比较`x < edge`而不是`x <= edge`。
  - 8.7 进一步说明阴影查找何时是未定义的。
  - 8.9 进一步规定噪声的范围和频率约束。
  - 语法:MOD_ASSIGN 改为保留(以匹配规范文本)。
  - 语法:改为要求函数参数中的数组大小。
- 修复了几处错字。

## 概述

本文档描述了一种称为 *OpenGL 着色语言* 或 *glslang* 的编程语言。最近图形硬件的趋势是在某些已经变得极其复杂的领域（例如顶点处理和片段处理）用可编程性取代固定功能。OpenGL 着色语言的设计目的是允许应用程序员表达发生在 OpenGL 流水线中那些可编程点的处理。

用这种语言编写的独立可编译单元称为*着色器*。*程序*是一组经过编译和链接在一起的着色器。本文档的目的是彻底规范这种编程语言。用于操纵和与程序及着色器通信的 OpenGL 入口点是与本语言规范分开定义的。

OpenGL 着色语言基于 ANSI C，并保留了许多特性，除非它们与性能或易于实现相冲突。C 语言被扩展了带有硬件限定符的向量和矩阵类型，以使它更简洁地进行 3D 图形中的典型操作。C++ 的一些机制也被借鉴，例如基于参数类型重载函数，以及在块的开始处而不是在需要时声明变量的能力。

## 动机

半导体技术已经发展到了每个顶点或每个片段可以进行的计算水平已经超出了通过设置状态来影响固定流水线阶段动作的传统 OpenGL 机制所能描述的程度。

暴露硬件扩展能力的愿望导致编写了大量扩展，不幸的后果是减少甚至消除了应用程序的可移植性，从而破坏了 OpenGL 的一个关键动机因素。

驯服这种复杂性和扩展扩散的一种自然方式是允许流水线的部分被用户可编程阶段取代。这在一些最近的扩展中已经完成，但编程是用汇编语言完成的，这是对当前硬件的直接表达，而不是面向未来的。主流程序员已经从汇编语言发展到高级语言，以获得生产力、可移植性和易用性。这些目标同样适用于编写着色器。

这项工作的目标是一种面向未来、独立于硬件的高级语言，易于使用且足够强大，能够经受住时间的考验，大大减少对扩展的需求。这些愿望必须受到在一两代硬件内快速实现的需求的调和。

## 设计考虑

我们将要引入的各种可编程处理器取代了 OpenGL 流水线的部分，作为起点，它们需要能够做它们所取代的一切。这仅仅是个开始，来自 RenderMan 社区和新游戏的例子为未来令人兴奋的可能性提供了一些提示。

为了促进这一点，着色语言应该处于足够高的水平，并具有我们正在解决的问题域的抽象。对于图形来说，这意味着向量和矩阵运算形成语言的基本部分。这从能够在表达式中直接指定标量/向量/矩阵运算，扩展到高效操作和分组向量和矩阵分量的方法。

该语言包括一组丰富的内置函数，它们可以像在标量上一样轻松地对向量进行操作。

我们很幸运地以 C 语言为基础，以 RenderMan 为现有的着色语言来学习。OpenGL 与“实时”图形(相对于离线图形)相关联，因此 C 和 RenderMan 的任何阻碍高效编译或硬件实现的方面都被丢弃了，但在大多数情况下，这些都不会被注意到。

OpenGL 着色语言是专门为在 OpenGL 环境中使用而设计的。它旨在提供 OpenGL 固定功能的某些部分的可编程替代品。通过设计，可以非常容易地从着色器中引用这些部分的现有 OpenGL 状态。

通过设计，在 OpenGL 处理管线的一部分使用固定功能，在另一部分使用可编程处理也是可能且非常容易的。其目的是，为着色器生成的目标代码独立于其他 OpenGL 状态，因此不需要重新编译或管理目标代码的多个副本。

图形硬件在顶点和片段处理级别上都在开发越来越多的并行性。在定义 OpenGL 着色语言时已经非常小心，以允许更高水平的并行处理。

最后，使用相同的高级编程语言来编程 OpenGL 流水线的所有可编程部分是一个目标。某些类型和内置函数不允许在某些可编程处理器上使用，但大部分语言在所有可编程处理器上都是相同的。这使得应用程序开发人员更容易接受着色语言，并使用它来解决他们的 OpenGL 渲染问题。

## 错误处理

编译器通常接受格式不正确的程序，因为检测所有格式不正确的程序是不可能的。例如，完全准确地检测未初始化变量的使用是不可能的。可移植性只对格式良好的程序有保证，本规范对此进行了描述。鼓励编译器检测格式不正确的程序并发出诊断消息，但不要求对所有情况都这样做。编译器需要返回关于词法、语法或语义不正确的着色器的消息。

## 排版惯例

斜体、粗体和字体选择主要用于提高可读性。代码片段使用固定宽度字体。嵌入在文本中的标识符用斜体表示。嵌入在文本中的关键字用粗体表示。运算符按其名称称呼，后跟括号中的粗体符号。文本中用于说明的语法片段对字面量使用粗体，对非终结符使用斜体。第 9 节“着色语言语法”中的正式语法对终结符使用全大写，对非终结符使用小写。
