# 问题

## #1 在这些可编程处理器上运行的程序应该称为着色器还是程序?

讨论:着色器(Shader)符合 RenderMan 和 DX8 中的常见用法。有人认为着色(shading)有颜色操作的含义,因此不适合顶点操作。

RenderMan 和 DX8 没有做出这种区分。似乎明智的做法是沿用着色器作为对图形管线某部分进行操作的程序的通用术语。

2001 年 10 月 12 日决议:将使用术语"着色器"。

注意:着色器用于表示单个独立的编译单元。程序用于表示链接在一起的一组着色器。

2002 年 9 月 10 日结案。

## #2 是否应该有一个单独的可编程单元来执行像素传输操作?

讨论:我们最初有一个单独的像素着色器的概念,像素和成像操作将在其中完成。经过进一步考虑,似乎不太可能有人将其实现为独立的功能单元,而更可能在幕后的片段着色器中完成。OpenGL 将像素和片段操作视为互斥的,因此共享一个处理单元是很自然的实现。强制一个与现实不同的抽象似乎是一个障碍,除了增加工作量。

2001 年 10 月 12 日决议:不,片段处理器将用于处理几何数据和像素数据。

2002 年 9 月 10 日结案。

## #3 是否应该允许着色器对它们替换的固定功能进行子集化?

讨论:定义接口以允许子集化会有很多复杂性。编写实现图形处理管线全部功能的着色器并不难。

2001 年 10 月 12 日决议:不,着色器不能对它们替换的固定功能进行子集化。如果着色器想以某种方式改变光照,那么它们也必须做其他项目。有实现 OpenGL 固定功能管线的示例着色器会有所帮助。

2002 年 9 月 10 日结案。

## #4 是否应该在 OpenGL 之上分层一种更高级的着色语言,而不是设计成适合 OpenGL 内部?

讨论:在当前设计中,着色语言集成到 OpenGL 中,只是提供了前面概述的状态控制管线的替代方法。Stanford 的方法是在 OpenGL 之上分层他们的着色语言。这有一些优点和缺点,在检查差异时会变得明显。

Stanford 的方法使用更高的抽象级别。当抽象与问题域匹配时,这有助于编写某些类型的程序。例如,将灯光和表面视为抽象实体使得某些 3D 图形操作更容易,但是 OpenGL 现在正用于视频和图像处理,在这些领域中这种抽象在很大程度上是无关紧要的。类似地,许多游戏已经避开了通过传统方式的光照,而是使用纹理(光照贴图)。

语言或绑定中没有任何内容可以阻止在可编程 OpenGL 之上分层更高级别的抽象。我们还希望保持 OpenGL 的总体抽象级别与其当前级别相同。

Stanford 的方法还提供了不同的计算频率。通过在更高的抽象级别上,其中一个程序定义了当前的整体图形操作,允许编译器分离出需要在图元组级别、图元级别、顶点级别和片段级别运行的部分。因此,编译器可以根据需要生成在 CPU、顶点处理器和片段处理器上运行的代码。这显然比让程序员指定在管线的每个部分运行的程序更复杂(尽管 Stanford 语言仍然需要一些提示),尽管这确实使硬件的虚拟化更容易,因为编译器具有整体视图。

这样做的主要缺点是,它迫使对 OpenGL 进行更具侵入性的更改,以支持图元、顶点和片段操作的清晰划分。许多核心 OpenGL 特性已被替换或不可用,无法将标准 OpenGL 变换和光照操作与自定义片段着色器一起使用(反之亦然),或允许一个顶点着色器驱动多个片段着色器。当前方法的一个优点是保持了 OpenGL 1.4 的外观和感觉,并允许在从固定功能过渡到完全可编程性期间采用优雅的混合搭配方法。

这并不是对 Stanford 工作的批评,因为他们别无选择,只能在 OpenGL 之上分层。

2001 年 10 月 12 日决议:OpenGL 着色语言应内置到 OpenGL 中,而不是分层在其之上。还应注意,如果不是这种情况,OpenGL 仍然应该有一种标准的着色语言,因此本文档仍然存在。因此,这个问题不是反对这个文档,而是反对 OpenGL API。

2002 年 9 月 20 日结案,已移至 API 问题列表。

## #5 着色模型是否应该是被片段处理器替换的固定功能片段处理的一部分?

讨论:着色模型在 Gouraud 和平面着色之间进行选择,将其作为被片段着色器替换的功能的一部分似乎很自然。平面着色涉及对图元类型的了解(对于触发顶点),这并不真正属于片段着色器。如果着色模型是平面的,片段着色器总是可以假设颜色是插值的,那么颜色梯度的设置计算可以将梯度设置为零。

2001 年 10 月 12 日决议:不,片段处理器的可编程功能不会取代着色模型。

2002 年 9 月 10 日结案。

## #6 alpha 测试是否可编程?

讨论:片段着色器有一个终止片段的函数,因此可以进行类似 alpha 的测试,但是 OpenGL 管线指定 alpha 测试应该在覆盖率修改 alpha 值之后发生。我们不想在片段着色器中进行覆盖率计算,因此 alpha 测试仍然在外部。如果用户乐意在自己的程序中在覆盖率之前进行 alpha 测试,那么他们可以这样做。

2001 年 10 月 12 日决议:是的,应用程序可以在片段着色器中进行 alpha 测试,但需要注意,在片段着色器中进行时,它发生在覆盖率计算之前。

2002 年 9 月 10 日结案。

## #7 alpha 混合是否可编程?

片段着色器可以使用内置变量 gl_FBColor、gl_FBDepth、gl_FBStencil 和 gl_FBDatan 读取当前位置的帧缓冲区内容。使用这些功能,应用程序可以实现自定义的混合、模板测试等算法。但是,这些帧缓冲区读取操作可能会导致性能显著下降,因此强烈建议应用程序尽可能使用 OpenGL 的固定功能进行这些操作。用于实现片段着色器(和顶点着色器)的硬件如果能够在空间和时间上独立地处理每个片段,就可以变得更简单和更快。通过允许将读-修改-写操作(如 alpha 混合所需的操作)作为片段处理的一部分来完成,我们引入了空间和时间关系。由于性能所需的极深流水线、缓存和内存仲裁,这些关系使设计复杂化。渲染到纹理、将帧缓冲区复制到纹理、辅助数据缓冲区和累积缓冲区等方法可以完成大部分(如果不是全部)可编程 alpha 混合可以完成的工作。此外,高级着色语言和自动资源管理减少了(或者至少抽象了)多遍的需求。

2001 年 10 月 12 日决议:是的,应用程序可以进行 alpha 混合,尽管与使用固定功能混合操作相比,可能会带来性能损失。

2002 年 7 月 9 日重新打开:这个问题与问题(23)相关,后者仍然开放,因此这个问题也应该保持开放。

另一种可能性是创建一个扩展,允许比当前 alpha 混合更大的灵活性,但仍将被视为固定功能。

决议:问题(23)决定允许帧缓冲区读取,因此这个问题再次决定允许 alpha 混合,但有上述注意事项。

2002 年 12 月 10 日重新打开。问题(23)重新决定禁止帧缓冲区读取。

决议:不,应用程序不能进行 alpha 混合,因为它们不能读取 alpha。

2002 年 12 月 10 日结案。

## #8 语言的定义方式是否应该使其可以在现有硬件上实现?

讨论:今天这一代硬件确实具有一定的可编程性。定义一种可以在今天和明天的硬件上工作的语言似乎是可取的。

2001 年 10 月 12 日决议:我们试图使着色语言具有前瞻性,并将其定位在我们认为硬件在一两代内可以达到的水平。我们避免添加功能(如小的定点数据类型或隐式限幅)或降低语言的级别(删除循环和函数)以更好地支持现有硬件,因为这是一种倒退。在现有硬件上运行着色器的有限子集是可能的,但应用程序以可移植的方式确定着色器是否能运行或是否能产生可接受的结果并不容易。总的来说,这里的决定是为硬件设定一个目标,在接下来的几年中努力实现。

2002 年 9 月 20 日结案。

## #9 是否应该放弃语言的预处理器概念?

讨论:我们可以不使用 #ifdef,而是使用 if (false),并依赖编译器剥离掉无法到达的代码。C++ 规范似乎不再强调使用 #ifdef,但我们仍然保留它,因为它是一种常见的惯用法,在代码中更容易看到,并且可以在语法不允许 if (false) 的地方使用。

我们是否需要特定于供应商的预定义 #define 以允许解决编译器问题?

从理想主义的角度来看,不需要,因为它为扩展提供了后门,但实际上差异会存在。我们已经看到着色器编写者使用 #define 来使着色器更具可读性的实例(例如,#define MVP gl_ModelViewProjectionMatrix)。

2001 年 10 月 12 日决议:不,应保留预处理器。支持的预处理器指令是 #ifdef、#ifndef、#undef、#else、#endif、#pragma、#define token(无参数)和 #error。

添加问题(55)以解决其他预处理器指令。

2002 年 9 月 10 日结案。

## #10 表示纹理分量的字段是否应该命名为 s、t、p 和 q?

讨论:考虑了其他重命名纹理 r 的替代方案,但被拒绝,因为它们似乎更令人困惑或容易出错。

A. 改为对颜色分量选择使用 red、green、blue、alpha。这使得后面描述的分量组机制过于繁琐。

B. 将颜色或纹理分量名称大写。

C. 删除颜色或纹理的名称。我们不想放弃向量的一些重要用法的符号便利性。

D. 将颜色分量顺序更改为 bgra,以使两个 r 分量现在对齐。这种颜色顺序对于 OpenGL 来说非常陌生,因此在现有 API 值如何映射到着色器使用的值方面会导致许多令人困惑的情况。

2001 年 10 月 12 日决议:是的,使用 s、t、p 和 q 作为表示纹理分量的字段名称是最佳选择。

2002 年 9 月 10 日结案。

## #11 是否应该有两个单独的活动片段着色器来处理背面和正面的情况?

讨论:如果用户指定两个片段着色器,一个用于正面片段,一个用于背面片段,则可以自动运行适当的着色器。这可能会提供更快的着色速率,但它迫使用户维护两个程序(其中大部分代码可能是通用的)。如果实现希望针对这种情况进行优化,编译器可以透明地完成此操作。

2001 年 10 月 12 日决议:不,应该使用单个着色器来处理背面和正面几何体。

2002 年 9 月 10 日结案。

## #12 是否应要求内置函数不仅在返回类型上有所不同?

讨论:考虑了重载仅在返回类型上不同的函数。然而,编译器的初步工作表明,这一功能严重复杂化了表达式的语义分析,以在返回类型明确但隐藏在表达式中时推断返回类型。

这可能比它的价值更复杂(并且可能是 C++ 不允许这样做的原因)。

再加上程序员用来消除歧义情况的新语法的需求,简单地为不同的返回类型给函数不同的名称更容易。

2002 年 2 月 25 日决议:是的,内置函数必须不仅在返回类型上有所不同。

2002 年 9 月 10 日结案。

## #13 如何定义噪声函数以允许从一个实现到另一个实现的一致行为?

讨论:噪声函数非常有用,在 RenderMan 的许多着色技术中起着重要作用。它在规范(和一致性测试)中提出了一个问题,即完全有效的噪声函数会给出非常不同的结果。OpenGL 避免将操作指定得如此严格,以至于不同的实现会给出精确到像素的结果——这允许实现在准确性/性能/成本权衡方面有一定的自由度。它还避免了必须将所有内容都指定到最小的细节。Perlin(噪声函数的发明者)已经认识到标准噪声函数的可取性(就像每个人都希望 sin 函数以相同的方式运行一样),并记录了他的想法。也许这应该是一个强烈推荐的实现。

这个问题与问题(36)几乎相同。

2002 年 9 月 19 日决议:不需要噪声的特定实现,但规范将尝试以一种可以从一个实现到另一个实现获得类似结果的方式来定义噪声函数。

2002 年 9 月 19 日结案:

## #14 是否应该允许字段具有数字选择器(例如 foo.2)?

讨论:这打破了标识符以字母开头的惯例。它使语言不那么纯粹,使词法分析更加困难,并为数字的表示方式增加了约束。

2002 年 9 月 10 日决议:不,语言应该按照问题(16)中的建议进行更改,以便没有数字选择器。

2002 年 9 月 10 日结案。

## #15 我们是否应该允许重组向量分量的字段?

讨论:这似乎是语言中过于复杂的一部分,没有任何额外的功能是无法用语言的其他部分轻松表达的。另一方面,重组在较低级别的汇编语言中是公开的。第三方面,也许这只是一个不应该/不需要在高级语言中公开的硬件"特性"。第四方面,已经演示了一些重组的有用示例,并且在编译器中支持它并不困难。

2002 年 9 月 10 日决议:分量重组被认为是一个有用的语言特性,将被保留。

2002 年 9 月 10 日结案。

## #16 是否应该有一种间接引用向量或矩阵的方法?

讨论:问题(14)和这个问题可以通过添加 [ ] 作为索引向量的数字方式来同时解决。然后,人们会说 foo[2],而不是 foo.2,解决问题(14),以及 foo[x] 作为间接引用。然后可以像在 C 中一样表示数字。

2002 年 9 月 10 日决议:是的,应该按照上述讨论中的建议允许对向量和矩阵进行间接引用。

2002 年 9 月 10 日结案。

## #17 gl_Position 和其他当前"只写"变量是否应该可读?

讨论:这只是一个编译器特性,对硬件支持没有影响。如果没有这个特性,编写代码通常很麻烦。如果需要,编译器可以使用临时变量来存储中间值。这也会使程序更整洁一些。

另一方面,只读输入和只写输出的 api 模型对于着色器编写者来说可能更清晰。

2002 年 9 月 19 日决议。是的,允许"只写"变量可读。

2002 年 9 月 19 日结案。

## #18 如何提供性能/空间/精度提示?

讨论:到目前为止基本上达成一致的一个是 varying:"varying" 表示透视校正,而 "fast varying" 表示如果能节省时间就走捷径。也许我们可以为此定义一个 #pragma。

也许这也可以应用于其他领域。

决议:性能/空间/精度提示和类型不会作为语言的标准部分提供,但会为此保留关键字。

结案:2002 年 11 月 26 日。

## #19 是否应该添加内置函数 "lookup"?

讨论:纹理可以用作查找表,而不仅仅是纹理。主要区别在于查找表会有与返回值相关的类型。

决议:是的,应该添加 lookup 函数作为内置函数,以便着色器可以表达返回值的类型。像 i8texture3 这样的函数将被添加,表示正在查找三个 8 位整数。这仍将被称为纹理,因为它预计会共享纹理资源。独立于纹理资源的通用表查找被推迟到 1.1 版。

2002 年 10 月 22 日结案。

注意:作为将此语言适配到 OpenGL 1.4 的一部分,这些函数后来被删除了,因为 OpenGL 1.4 不支持这些函数将操作的纹理。

## #20 是否应该添加大于 16 位的整数?

讨论:着色语言的设计方式不会通过要求大量不必要和冗余的功能而给硬件设计者带来过重的负担。整数很有用,并且可能在用作循环计数器和数组索引时更有效。16 位整数被添加到语言中,以提高这些情况下的效率。浮点值的尾数可用于执行整数运算,因此除了浮点数学单元之外,硬件设计者不需要完整的整数数学单元。如果这最终成为决定这个问题的关键因素,可以将整数定义为 23 位(至少对于处理器内的操作而言),因为这是 IEEE FP32 值的尾数大小。作为另一个数据点,Renderman 根本不支持整数。

决议:今天有硬件方面的原因将整数限制为 16 位,所以它们将是 16 位。

结案:2002 年 11 月 19 日。

## #21 是否应该添加 int 的向量或(局部变量)数组?

讨论:例如,问题 (19) 中提出的 lookup 函数可以返回 3 个整数值。着色器不仅仅是浮点算法,还做诸如表查找、间接寻址和其他通用算法计算之类的事情。语言不必直接映射到硬件。另一方面,为了支持这一点,我们必须添加 ivec2、ivec3 和 ivec4,或者允许 int 的局部变量数组。

决议:是的。将添加 int 的向量。

结案:2002 年 11 月 5 日。

## #22 是否应该支持递归?

讨论:可能没有必要,但这是另一个基于语言如何直接映射到硬件来限制语言的例子。一个想法是,递归会使光线跟踪着色器受益。另一方面,许多递归操作也可以通过用户使用数组管理递归来实现。RenderMan 不支持递归。如果证明有必要,可以在以后添加这个功能。

2002 年 9 月 10 日决议:实现不需要支持递归。

2002 年 9 月 10 日结案。

## #23 是否应该允许片段着色器读取帧缓冲区中的当前位置?

讨论:在考虑多重采样的情况下,可能很难正确地指定这一点。对于硬件实现者来说,实现这一功能也可能相当困难,至少在性能合理的情况下是这样。但是,在着色语言白皮书最初发布后,这是两个最需要的项目之一。ISV 继续告诉我们,他们需要这种能力,而且它必须是高性能的。

决议:是的。允许这样做,但要强烈警告对性能的影响。

2002 年 12 月 10 日重新打开。对性能影响和实现的不切实际性的担忧太多了。

2002 年 12 月 10 日结案。

## #24 是否需要在语言中添加任何内容,以允许程序在编译时编译,而不需要为 OpenGL 状态更改保存多个编译版本?

讨论:强烈希望实现能够在编译时生成正确的代码,而不必在 OpenGL 状态在稍后时间发生更改的情况下(例如,在执行时之前不知道纹理贴图的属性)拥有多个编译版本或稍后重新编译。也许是语言中需要更多提示的另一个领域,或者硬件演进可以解决这些问题。

决议:这被确定为 OpenGL 着色语言的一般设计目标......解决其他问题时,目的是使从着色器生成的目标代码独立于其他 OpenGL 状态。

结案:2002 年 11 月 5 日。

## #25 我们是否应该添加接受 gen-type 和标量的 min 和 max,以匹配 clamp 语义?

决议:是的,应该添加这些内容。

结案:2002 年 9 月 22 日。

## #26 可编程性是否应该按功能(例如,光照着色器、表面着色器、变换着色器、纹理生成着色器等)分解,而不是当前提案中以硬件为中心的方法(顶点和片段着色器)?

2001 年 12 月 7 日决议:不,顶点和片段着色器的概念更适合作为以硬件为中心的 API 的 OpenGL,并在审查期间获得了积极的反馈。

2002 年 9 月 10 日结案。

## #27 纹理单元是应该指定为关键字还是数字?

讨论:对于内置的纹理访问函数,纹理单元被指定为一个数字。

它是否应该定义为关键字?当前的感觉是,在某些情况下,将纹理指定为程序值而不是关键字更方便。

这个问题实际上是问题 (51) 的一部分。

2001 年 12 月 7 日决议:纹理单元被指定为一个数字。在某些情况下,将纹理指定为程序值而不是关键字更方便。

2002 年 7 月 12 日重新打开:需要进一步讨论提供的真正便利性。

决议:作为问题 (51) 解决。

2002 年 10 月 22 日结案。

## #28 全局值是否自动初始化?

2001 年 12 月 7 日决议:不,全局值不会自动初始化。但是,对于实现来说,支持自动初始化作为调试模式选项可能很有用。

2002 年 9 月 10 日结案。

## #29 语言是否应该支持按位操作?

2001 年 12 月 7 日决议:语言本身支持按位操作。在某些可编程单元(打包和解包处理器)中,这些操作至关重要。然而,有一种愿望是限制每个可编程单元的复杂性。对于顶点和片段处理器,支持布尔运算但不支持通用按位运算。这是为了避免在这些处理器已经需要的浮点能力之上还需要完整的整数处理功能。

2002 年 7 月 12 日重新打开:某些位操作非常有用,不能用浮点操作轻松模拟。例如,应用程序可以将多个数据字段复用到纹理分量中,并使用按位运算符提取这些值(例如,使用 16 位亮度纹理的 12 位存储强度,剩余的 4 位存储不透明度)。让着色器能够执行这种提取比定义新的纹理格式更可取。

处理此功能的另一种方法是使用内置函数从整数中提取位字段,因为这是按位运算符的一个预期用途。

这与问题 (90) 有关。没有整数纹理,解决这个问题的需求就会减少。

决议:位支持推迟到未来版本。

结案:2002 年 12 月 10 日。

## #30 是否应要求以 32 位浮点精度进行内部计算?或者,如果实现愿意,是否应该允许它们以更高或更低的精度执行计算?

讨论:此问题与问题 (33) 和问题 (68) 相关。

决议:OpenGL 规范 1.4 版第 2.1.1 节已经隐含了浮点要求。这就足够了。

结案:2002 年 11 月 26 日。

## #31 可以在片段着色器内覆盖计算得到的 LOD 或偏差吗?

2001 年 10 月 12 日决议:计算得到的 LOD 可以由片段着色器提供的值偏移。为此提供了带有 LOD 参数的内置纹理访问函数。

2002 年 9 月 19 日结案。

## #32 插值值是否进行透视校正?

2002 年 6 月 3 日决议:是的,定义为 varying 的变量是透视校正的。

2002 年 9 月 10 日结案。

## #33 是否应该支持精度提示(例如,使用 16 位浮点数或 32 位浮点数)?

讨论:将计算的数据类型标准化为单一类型大大简化了语言的规范。即使允许实现悄悄地提升精度值,如果编写者无意中依赖了精度运算符的限幅或环绕语义,着色器也可能表现出不同的行为。通过定义一组精度类型,我们最终所做的只是迫使硬件实现它们以保持兼容性。

在编写通用程序时,程序员长期以来已经不再担心用字节、短整型还是长整型进行计算是否更有效,我们不希望着色器编写者认为他们必须同样关注这一点。支持精度数据类型的唯一短期好处是,它可能允许现有硬件更有效地运行着色器的子集。

此问题与问题 (30) 和问题 (68) 相关。

决议:性能/空间/精度提示和类型不会作为语言的标准部分提供,但会为此保留关键字。

结案:2002 年 11 月 26 日。

## #34 OpenGL 着色语言的设计是否应该包括对非实时性质的着色器的支持?

2002 年 9 月 19 日决议:是的,语言的设计应该考虑非实时性质的应用程序。

2002 年 9 月 19 日结案。

## #35 是否应该向着色语言添加点、法线、颜色等附加类型?

2001 年 10 月 12 日决议:不,不应添加这些类型。现有的通用向量类型可以支持它们,而无需向语言添加额外的类型。

2002 年 9 月 10 日结案。

## #36 每个人都会对 smoothstep 和 noise 有不同的实现,还是我们应该尝试指定和强制执行这些函数的通用实现?

讨论:这个问题与问题 (13) 几乎相同。smoothstep 的定义应该足够了。OpenGL 不是像素精确的,smoothstep 的定义对于规范来说已经足够准确了。

2002 年 9 月 19 日决议:OpenGL 着色语言有很大的实现空间。即使对于现在的 OpenGL,也不期望在两个不同的硬件上生成的图片会产生相同的结果。规范的编写方式应该使实现产生非常相似(尽管不完全相同)的结果。

OpenGL 着色语言的一致性测试是 OpenGL ARB 将来要解决的问题。

进一步决定在规范中添加 noise() 的源代码规范。但是,这还没有完成。

2002 年 9 月 19 日结案。

## #37 片段着色器"杀死"片段的功能应该是关键字还是内置函数?

讨论:Kill 感觉像是一个类似于 break 和 continue 的流控制指令。它不是一个应该被用户函数覆盖的函数。布尔表达式可以在 kill 关键字之前的 if 语句中求值。一旦执行 kill,就没有理由继续处理,所以没有理由将其伪装成函数调用。kill(boolExpr); 作为 if (boolExpr) kill; 的快捷方式并没有节省多少。 

2002 年 4 月 15 日决议:片段着色器"杀死"片段的功能应该是一个关键字。

2002 年 9 月 19 日结案:

## #38 是否应该允许从顶点着色器中使用内置的纹理和噪声函数?

讨论:有许多支持置换映射的请求。通过允许内置的噪声和纹理函数在顶点着色器和片段着色器中可用,可以满足这一请求。这可以通过让编译器将顶点着色器分成序言、纹理/噪声访问和尾声来实现。序言将由顶点处理硬件执行,纹理/噪声访问将由片段处理硬件完成。中间结果将反馈到顶点处理硬件以执行尾声,然后传递到片段着色器进行片段处理。另一方面,应用程序可以在主机 CPU 上完成所有这些工作。

决议:是的,也应该允许从顶点着色器中使用纹理和噪声函数。

2002 年 10 月 22 日结案。

## #39 是否应该定义 varying 变量的插值值由片段中心处的采样决定?

讨论:这与多重采样有关,需要进一步调查。

决议:这遵循 gl 规范 1.4 版第 3.2.1 节中概述的相同规则。

结案:2002 年 11 月 26 日。

## #40 语言中是否应该支持顶点和片段处理的无符号整数?

讨论:这个问题与问题 (29) 相关。如果我们允许按位运算符,可能需要一种方法来指定有符号或无符号整数。目前,无符号整数仅为打包和解包着色器语言定义,而不是为顶点和片段语言定义。

决议:因为已经确定整数携带 16 位精度,除了符号位,所以没有必要引入无符号整数类型。

结案:2002 年 11 月 26 日。

## #41 gl_FrontMaterial 和 gl_BackMaterial 是 attribute 还是 uniform?

讨论:规范目前将这些定义为 attribute 数组,但规范也指出不允许 attribute 使用数组。如果我们想将它们视为 uniform,它们可以保留为数组。

否则,我们应该更改名称和定义,以避免使用数组(即,为每个 attribute 提供唯一的名称)。

2002 年 9 月 19 日决议:这些将被视为 uniform。展望未来,如果需要在每个顶点更改这些值,我们宁愿鼓励应用程序使用用户定义的 attribute。

2002 年 9 月 19 日结案。

## #42 是否应该有一种方法来指定顶点着色器生成的变换位置应该相对于固定功能管线不变?

讨论:一个 ISV 请求了此功能。没有它,在许多图形架构上,使用顶点着色器渲染的几何体可能无法与使用固定功能路径渲染的几何体精确匹配。

一个可能的解决方案是更改规范,其中指出内置变量 gl_Position 必须由所有顶点着色器写入。相反,如果顶点着色器没有写入变量 gl_Position,顶点位置将以相对于固定功能管线不变的方式进行变换。如果顶点着色器写入了 gl_Position,结果位置可能与固定功能管线不变,也可能不变。

但是这个解决方案增加了着色器编写者可能无意中未能写入 gl_Position 的风险,这现在不会产生错误,而是不变地变换 gl_Vertex。因此,这里有一组可能的替代解决方案。由于我们有内置函数,内置函数可能是对不变变换请求的一个干净的解决方案。下面的三个内置函数都可以提供 gl_Vertex 的不变变换。替代方案 (A) 对 RenderMan 着色器编写者来说最熟悉(减去命名空间)。替代方案 (B) 和 (C) 仅提供不变变换,其中 (B) 允许指定输入,而 (C) 隐式输入 gl_Vertex。

原始建议的解决方案和这些替代解决方案解决了 ISV 请求,但方式不同。

(A) 内置函数:

```
genType transform( [mat xform,] genType coord )
```

如果指定了矩阵,则返回 xform\*coord。否则,变换 coord 不变于固定函数方法。

示例:

```glsl
// 通过 MVP 变换,不一定与固定函数不变。
gl_Position = transform( gl_ModelViewProjectionMatrix, gl_Vertex );

// 与固定函数不变地变换。
gl_Position = transform( gl_Vertex );
```

(B) 与 (A) 相关,但没有可选矩阵:

```
genType transform( genType coord )
```

变换 coord 不变于固定函数方法示例:

```glsl
// 与固定函数不变地变换。
gl_Position = transform(gl_Vertex);
```

(C) 与 (B) 相关,但没有参数,重命名函数:

```
vec4 fixedtransform()
```

输出与固定函数方法不变,隐式输入为 gl_Vertex。

示例:

```glsl
gl_Position = fixedTransform();
```

决议:使用上述选项 C。

2002 年 10 月 22 日结案。

## #43 gl_FB\* 的内置导数函数的定义是什么?

讨论:一个简短的示例片段着色器最能演示这个问题。

```glsl
void main(void)
{
  gl_FragColor = dFdy(abs(gl_FBColor));
}
```

早期的白皮书允许在片段处理器中进行通用帧缓冲区读取。OpenGL 通常只指定由光栅化生成的片段,而不指定片段由光栅化生成的顺序。因此,片段处理器中的通用帧缓冲区读取可能导致未定义的行为。

后来的白皮书只允许在片段处理器中进行受限的帧缓冲区读取。(片段的 xw、yw 窗口坐标处的像素。)因此,问题变成,内置导数函数在概念上是否需要隐式的通用帧缓冲区读取(至少在片段的 xw、yw 窗口坐标处的像素的直接邻域内)?在这种情况下,"在任何给定的时间点"是什么意思?

可能的解决方案:

a) 不允许在片段处理器中进行 gl_FB* 读取操作。(这与问题 (23) 相关)

b) 如果 gl_FB* 是表达式的父级,则内置导数函数未定义。(在条件或循环体内,内置导数函数在某些情况下未定义。) 

拒绝的解决方案:

c) 明确定义 OpenGL 光栅化片段的顺序。

决议:gl_FB\* 已被删除。问题 (23) 已重新打开并关闭,不允许帧缓冲区读取。

2002 年 12 月 11 日结案。

## #44 表示当前 OpenGL 状态的 uniform 变量是否应该只对特定处理器可用,还是对任何处理器都可用?

讨论:当前规范偏向于顶点光照和片段着色。

目前,表示为内置 uniform 变量的 OpenGL 状态仅对特定处理器可用(例如,光照状态仅对顶点处理器可用)。这使得片段着色器使用 OpenGL 状态进行光照计算变得不必要地困难。规范应该不考虑哪些着色器需要访问哪些内置的 uniform OpenGL 状态。

决议:封装为 uniform 变量的 OpenGL 状态应该可供任何处理器访问。

2002 年 10 月 22 日结案。

## #45 OpenGL 状态的命名约定是否应与 ARB_vertex_program 扩展采用的约定相同?

讨论:OpenGL 着色语言使用 gl_ModelViewMatrix 来引用特定的 OpenGL 状态,而 ARB_vertex_program 扩展使用 state.matrix.modelview。

为了保持一致性,约定是否应该相同?

OpenGL 着色语言引用 GL 状态的约定是在还不清楚由于 IP 问题和缺乏共识,ARB 顶点程序扩展是否甚至可能之前开发的。ARB_vertex_program(和 ARB_fragment_program)状态绑定可能会使某些人误以为语法是类 C 语言中的结构。(在类汇编语言中出现此混淆的风险较小。)ARB_vertex_program 和 ARB_fragment_program 将状态打包到 vec4 中。

在类 C 语言中,这种打包的需求较少。

2002 年 8 月 13 日决议:不,约定不必相同。目前对进行此名称更改没有足够的兴趣。

2002 年 9 月 10 日结案。

## #46 对于对象轮廓处的一般导数,预期的行为是什么?

讨论:如果要用于滤波宽度或 lod 计算,它必须是局部瞬时导数。这几乎决定了没有实现可以查看相邻片段来计算导数,因为始终可以构造一个仅命中一个片段(因此没有有效邻居)的对象。

决议:参见文章的导数部分。

2002 年 12 月 4 日结案。

## #47 导数函数的名称是否应该与 Renderman 中使用的名称更相似?

讨论:导数函数的命名与 RenderMan 建立的先例有些不一致,在 RenderMan 中,Du(f) 和 Dv(f) 分别计算 df/du 和 df/dv。dPdu 和 dPdv 可能更准确,但在功能上等同于 Du(P) 和 Dv(p),其中 P 是样本 3D 位置的内置变量。我们至少应该考虑将 Dx() 和 Dy() 用作 OGL2 导数函数的名称。

决议:名称更改为 dFdx、dFdy。

2002 年 12 月 4 日结案。

## #48 是否应该添加 dPdz(或 Dz)函数?

讨论:如果有人想推导出 df/du 和 df/dv,我们还需要 dPdz() 或 Dz() 来避免在对象轮廓处出现奇点

决议:不添加此内容。

2002 年 12 月 4 日结案。

## #49 着色语言是否应包括结构体?

讨论:着色语言应该支持结构体。结构体提供了一种干净的方式来对数据进行分组以创建抽象数据类型。它们对开发人员很方便,并且在 C 和其他通用编程语言中受支持。

另一方面,还没有提出将结构体添加到语言中的令人信服的理由。如果我们将此留到语言规范的后期修订版,可能有助于我们更快地完成语言。但是,如果添加了结构体,最好用结构体定义光照状态。Vital Images(ISV)表示他们希望语言中有结构体。

2002 年 9 月 19 日决议:着色语言将包括结构体。

2002 年 9 月 19 日结案。

## #50 顶点处理器的定义方式是否应该允许它对曲面进行曲面细分?

讨论:几何 LOD 和曲面的问题非常复杂,并且不断发展,除了通用可编程 CPU 之外,没有任何硬件足以胜任这项工作。任何图元的选择都会受到严重争议。最流行的曲面图元(如有折痕的细分曲面或修剪的 NURBS)在硬件中不容易实现。

如果我们从性能的角度看问题,LOD 的生成通常不是问题,因为只有当几何体以显著方式更接近或更远离相机时,才需要生成新的 LOD。当我们对曲面进行动画或交互式操作时,问题就出现了。在这些情况下,拓扑结构不会改变,因此可以以非常高效的方式解决。我们只需为 LOD 中的每个顶点存储对 CV 的引用列表和权重因子。

这个简单的代码可以适应所有类型的曲面:

```glsl
for(i = 0; i <vertex_count; i++)
{
  x = 0;
  y = 0;
  z = 0;
  for(j = 0; j < *influence_list_length; j++)
  {
    index = *index_array++;
    value = *value_array++;
    x += value * control_vertex_array[index].x;
    y += value * control_vertex_array[index].y;
    z += value * control_vertex_array[index].z;
  }
  surface_vertex_array[i].x = x;
  surface_vertex_array[i].y = y;
  surface_vertex_array[i].z = z;
  influence_list_length++;
}
```

这可以集成到顶点着色器中以实现最大的灵活性,但这意味着顶点着色器必须能够对数据数组进行随机访问。

决议:推迟到本规范的未来版本。

结案:2002 年 10 月 22 日。

## #51 语言是否应该提供某种机制来区分位置无关变量和位置相关变量?

讨论:有人评论说"我们真的应该在语言中暴露 SIMD 语义吗?"回应:

• 真正引入的是位置独立性。 

• 现有的 'uniform' 和 'varying' 已经引入了这个概念,这个提议只是完善了它。

• 大多数硬件都会从中受益。

可行性:编译器可以进行足够的数据流和控制流分析,以找到可能导致位置独立变量赋值的所有路径。它可能会找到额外的路径,但不允许遗漏任何实际路径。由此,编译器可以证明位置独立变量是否只采用位置独立的值。在罕见的情况下,它可能会错误地说位置独立变量不是这样,但这些通常发生在退化的代码中。

全局 Uniform。要求编译器进行跨函数数据流分析是要求过高的,尤其是跨不同的编译单元。因此,不支持全局读/写位置独立变量的想法,也没有提议。因此,这些 'uniform' 的用法之间没有冲突。

输出 Uniform 参数。与 uniform 全局变量相同的问题。Uniform 全局变量和参数必须是只读的。

过去的一个替代方案是使用 'int' 类型作为提示编译器某个值是位置无关的,例如循环索引、纹理 ID、数组下标等。这有问题,因为基于浮点数的控制流可能会使提示无效,使编译器的负担与没有提示时一样重。和/或它使 'int' 类型的用处减少,迫使它遵守提议的 'uniform' 语义。这将本来独立的想法捆绑在一起的程度太高了。

这个问题与问题 (27) 相关。

决议:使用 'uniform' 限定符来标识局部作用域变量、函数返回值和函数参数是位置独立的。局部 'uniform' 变量不能写入从位置派生的值。声明为返回 'uniform' 的函数只能返回不是从位置派生的值。

如果代码中有一条静态可识别的路径,会在位置独立变量中留下一个位置相关的派生值,编译器可能会返回警告。也就是说,如果一个变量被声明为 uniform 或传递给 uniform 参数,如果编译器无法证明该变量始终是位置独立的,则会发出错误。

2002 年 10 月 22 日结案。

## #52 着色语言的资源限制应该如何定义?

讨论:讨论了各种提议。一个非常重要的考虑因素是最终得到一个提供应用程序可移植性的规范(例如,ISV 不需要支持多个渲染后端才能在所有不同风格的硬件上运行)。ISV 肯定希望规范说明着色语言实现负责确保所有有效着色器都必须运行。

决议:易于计数的资源(顶点处理器 uniform 的数量、片段处理器 uniform 的数量、attribute 的数量、varying 的数量、纹理单元的数量)将具有可查询的限制。应用程序负责在这些外部可见的限制内工作。着色语言实现负责虚拟化不易计数的资源(最终可执行文件中的机器指令数、最终可执行文件中使用的临时寄存器数等)。

2002 年 10 月 29 日结案,作为 API 问题列表的一部分。

## #53 如果坐标空间由非线性变换分隔,用户裁剪平面将如何处理?

讨论:着色语言规范依赖于 GL 裁剪的标准定义。只要坐标空间仅由线性变换分隔,这就可以工作,但是着色语言也解除了这些限制。

建议的解决方案:采用 NVIDIA 为 ARB_vertex_program 提出的某些建议中的"裁剪坐标输出"方法(在规范最终确定之前很久就被删除了)。这种方法提供了完全可编程的用户裁剪,不依赖于程序的任何语义或任何分析;并且它不会使大多数情况未定义。

决议:指定用户裁剪平面仅在线性变换下工作。在非线性变换下会发生什么目前是未定义的。

2002 年 10 月 22 日结案。

## #54 如何支持全局像素操作(例如直方图、最小/最大值)?

2002 年 9 月 10 日添加。

讨论:当前规范仅允许访问当前位置的片段(尽管根据问题 (23) 的讨论,这是开放的)。明确禁止需要访问帧缓冲区或传入数据流中其他片段位置的操作。着色语言将如何提供支持直方图和最小/最大值等全局像素操作的功能?

希望能够运行一个对多个片段进行操作的程序(类似于如何运行生成新几何体的顶点程序,问题 (50))。

决议:推迟到本规范的未来版本。

结案:2002 年 10 月 22 日。

## #55 除了已经定义的指令外,预处理器是否应该有任何指令?

2002 年 9 月 10 日添加。

讨论:可以向语言规范中添加许多预处理器指令,例如 #if、#elif、#include、#define token(...)(带参数)、##(标记粘贴)、#line、#error、#(本身)、#(将标记转换为字符串)、defined(token)(以及所有其他运算符,&&、|、+等)和预定义的宏,如 \*\*DATE\*\*、\*\*FILE\*\*。

特别是,#if 将是一个有用的补充,以支持版本、日期等的处理。但这需要引入 ||、&&、>、<、! 运算符。

2002 年 9 月 24 日决议:着色语言预处理器基本上将具有 C 预处理器的所有功能,只是不支持 #include 指令,也不包括基于字符串的指令。

2002 年 9 月 24 日结案。

## #56 如果规范说不支持递归,那么实现支持递归是否是错误?

2002 年 9 月 10 日添加。

讨论:这个问题与问题 (22) 相关。如果我们说不支持递归(或其他某些功能),实现支持它是否是错误?也许规范应该对这类事情保持沉默,以便以后可以优雅地将它们作为扩展或标准的一部分添加进来。

决议:一般来说,语言中有一些程序在编译器无法检测到的方面不是格式良好的。可移植性只能为格式良好的程序保证。检测递归就是一个例子。语言会说格式良好的程序不能递归,但不强制编译器检测可能发生的递归。

结案:2002 年 11 月 29 日。

## #57 是否应该有一种标准方式供应用程序调用调试模式?

2002 年 9 月 10 日添加。

被问题 (67) 取代。

2002 年 9 月 24 日结案。

## #58 语言是否应包含保留字列表?

2002 年 9 月 10 日添加。

讨论:目前规范中不包含保留字列表。如果没有这样的列表,当我们将来对语言进行添加时,有效的着色器可能会变得无效。

建议的解决方案:是的,语言应该包含一个保留字列表,包括以下内容:struct、union、enum、typedef、template、goto、switch、default、inline、noinline、long、short、double、sizeof、volatile、public、static、namespace、using、asm、cast、half、fixed 以及包含两个连续下划线的所有标记。

2002 年 9 月 24 日决议:是的,着色语言应该包含一个保留字列表。

2002 年 9 月 24 日结案。

## #59 函数参数应该如何传递?

2002 年 9 月 10 日添加。

讨论:今天的规范说函数参数是通过引用调用的,不允许别名,并且 output 用于输出参数。这有一些不明显的问题:

(A)Uniform 和其他全局变量不能作为参数传入,因为这会创建别名。
(B)Varying 和其他只写变量通过引用传递非常棘手,因为没有任何内容表示参数是只写的。
(C)如果着色器写入"通过引用传递"的参数,它应该更新调用者的参数,或者着色器应该生成错误,因为它不是输出参数。然而,预期的用法似乎是写入非输出是可以的,效果只是局部的。

可以将规范更改为说函数参数是按值-返回调用的,这意味着以下内容:

(A)没有限定符的参数意味着在调用时从调用者那里复制参数。
(B)限定符 output(或 out)意味着参数将在返回时复制回调用者,但不会在调用时复制。
(C)限定符 input output(或 inout)意味着参数既复制进来又复制回去。

这些语义解决了所有与参数相关的别名问题。编译器不必检查别名,它可以编译为没有别名,并且对于着色器编写者来说,如果他们以看起来像别名的方式传递参数,会发生什么是明确定义的。这些语义还允许将只写变量传递给函数。最后,这个解决方案允许写入非输出参数,同时明确说明只有局部副本被修改。

2002 年 9 月 24 日决议:更改规范,说明函数参数是按照上述定义通过值-返回调用的。

2002 年 9 月 24 日结案。

## #60 光照状态的内置名称应该如何定义?

2002 年 9 月 11 日添加。

讨论:当前光照状态的名称(gl_Light0..n[8] 和相关的预定义数组索引值)使得编写处理灯光的循环变得很麻烦。这个问题与问题 (49) 相关。

2002 年 9 月 24 日决议:应该向着色语言添加结构体,并将光照状态重新定义为灯光结构体数组。

2002 年 9 月 24 日结案。

## #61 是否应该允许用户定义的函数重新定义内置函数?

2002 年 9 月 13 日添加。

讨论:目前还不清楚允许这样做有什么好处。如果用户无意中使用了与内置函数相同的名称,他们将得到意外的行为或性能下降,或者两者兼而有之。

决议:是的。这是语言和库的正常行为。

2002 年 9 月 10 日结案。

## #62 语言是否应包括用于 eye/object 平面的纹理生成系数?

2002 年 9 月 13 日添加。

讨论:由 Intel 的 Kent Lin 提出的问题。

决议:是的,应该添加这个状态。

2002 年 10 月 22 日结案。

## #63 语言是否应包括投影矩阵的内置变量?

2002 年 9 月 13 日添加。

讨论:已经为模型视图矩阵和模型视图投影矩阵定义了内置变量。是否也应该为投影矩阵添加内置变量?

决议:是的,应该添加这个状态。

2002 年 10 月 22 日结案。

## #64 是否应该为 OpenGL 1.4 中引入的状态添加内置变量名?

2002 年 9 月 13 日添加。

讨论:规范目前是针对 OpenGL 1.3 编写的,因此它不包含点参数状态和雾坐标状态。是否应该添加这些内容?

2002 年 9 月 24 日决议:是的,我们应该为 OpenGL 1.4 中引入的状态添加内置变量名。

2002 年 9 月 24 日结案。

## #65 mat \* mat 是执行矩阵乘法还是逐分量乘法?

2002 年 9 月 16 日添加。

讨论:目前规范规定,如果指定了两个矩阵,"\_"运算符将导致逐分量乘法。乘法运算符(\_)对标量 \_ 标量、标量 \_ 向量和矩阵 \_ 向量执行预期的线性代数运算,但对于矩阵 \_

矩阵,它是逐分量的,这是一种相对罕见的操作。之所以这样做,是为了与其他按分量运算的运算符保持一致。(例如,我们可能不希望矩阵/矩阵是真正的矩阵除法,而不是逐分量的除法。是否应该更改矩阵 \* 矩阵以表示矩阵乘法操作?

决议:应修改规范,以表明如果两个操作数是矩阵,"\*"运算符将导致矩阵乘法。

2002 年 10 月 22 日结案。

## #66 关于着色器允许运行的时间长度,规范应该说些什么?

2002 年 9 月 16 日添加。

讨论:早期版本的白皮书谈到了看门狗计时器。作为语言规范的一部分,这样的东西有必要吗?

决议:语言规范不应该说任何关于着色器允许运行的时间长度的内容。超时、交互性和检测恶意着色器是实现和/或操作环境的细节。GL2 扩展规范中可能应该有这样的内容:如果导致该着色器执行的应用程序终止,则正在执行的着色器也会终止。

2002 年 10 月 22 日结案。

## #67 是否应该有一种标准化的方式来指定调试和优化级别?

2002 年 9 月 16 日添加。

讨论:有四种可能的选择。(A)我们不调试,并且总是优化,所以没有问题。(B)我们为编译和链接的入口点添加调试和优化参数。(C)我们使用 #pragma 指定调试和优化级别,并概述基本的可移植含义。(D)我们说这完全取决于平台,不指定任何内容。

(A)似乎是短视的,因为打开/关闭优化是跟踪某些类型缺陷的技术和解决方法,我们最终会想要调试着色器,并且会有编译时与运行时的权衡(例如,如果应用程序动态生成生命周期非常短的着色器,关闭较慢的优化可能会更快)。

(B)似乎有点笨拙,因为这些活动会有平台相关的方面。(D)似乎对原则上存在于所有平台上的东西来说走得太远了。

决议:使用 #pragma 指定调试和优化级别,并概述基本的可移植含义。

2002 年 10 月 22 日结案。

## #68 语言是否应该支持显式数据类型,如 'half'(16 位浮点数)和 'fixed'(定点限幅数据类型)?

2002 年 9 月 17 日添加。

高级语言通常支持多种数值数据类型,允许程序员在性能和精度之间做出适当的平衡。例如,C 语言支持 float 和 double 数据类型,以及各种整数数据类型。同样的一般考虑也适用于着色语言。

对于着色计算,精度远低于 32 位浮点数通常就足够了。直到最近,大多数图形硬件都使用 9 或 10 位定点算法执行所有着色计算。低精度数据类型可以以更高的性能实现,特别是当数据必须离开芯片时(例如纹理数据)。因此,在硬件着色语言中提供对精度低于 32 位的数据类型的访问是可取的。

问题 (33) 讨论了精度提示。精度提示不如附加数据类型有用,因为精度提示不允许按精度进行函数重载。开发人员发现,能够使用具有不同精度数据类型的相同名称和参数列表的函数非常方便和有用。

能够按变量(而不是按着色器)指定数据类型也很重要,因为某些计算(例如纹理坐标计算)通常需要比其他计算更高的精度。

另一方面,人们希望确保着色器在不同的实现之间是可移植的。为了实现可移植性,没有对 half 进行本地支持的实现将受到影响,因为它们必须将中间计算限制在适当的精度内。如果这些附加数据类型是编译器可以选择以较低精度进行计算的提示,那么这会使 ISV 面临意外的限幅或溢出语义,因此不同的架构可能会给出非常不同的结果。提示还意味着在幕后有一种明确指定的在类型之间进行转换的方法,因此函数重载解析变得更加复杂,需要额外的规则来解决歧义,除非提供所有合法组合的函数。指定所有合法组合需要添加相当多的附加函数类型(点积需要 { float, half, fixed} \_ { float, half, fixed} \_ 分量数,即 36 个版本(而仅使用 float 只需 4 个版本)。

如果附加数据类型是真实类型,那么它们可以应用于什么?如果是 uniform 和 attribute,那么不同的大小现在反映在 API 中,但 half 和 fixed 在 C 中没有本地支持。如果 half 后面跟着一个 float,这是否意味着 float 必须从 16 位边界开始?fixed 的打包呢——真实大小未定义。如果 half 和 fixed 仅限于临时变量,那么这会使事情变得更容易,但现在存储效率的优势就丧失了。

OpenGL 规范目前规定"用于表示位置或法线坐标的浮点数的最大可表示幅度必须至少为 232"。我们是否应该引入与此相悖的东西?s10e5 精度对于纹理坐标来说是不够的,即使对于 1k × 1k 的纹理也是如此。似乎 half-float 为精度问题在整个着色器中传播打开了一扇门。

决议:性能/空间/精度提示和类型不会作为语言的标准部分提供,但会为此保留关键字。

结案:2002 年 11 月 26 日。

## #69 片段着色器是否应该能够访问提供位置的 varying 变量?

2002 年 9 月 17 日添加。

讨论:窗口位置在某些片段着色器中非常有用。例如,它可用于使用片段着色器实现点画模式。

决议:窗口位置是片段处理器中可用的内置变量 gl_FragCoord 的一部分。应修改规范,以明确说明此变量的 x 和 y 值定义了片段的窗口位置。

2002 年 10 月 22 日结案。

## #70 语言是否应该支持布尔向量(例如 bool2、bool3、bool4)以及相应的向量运算符?

2002 年 9 月 17 日添加。

讨论:该语言支持短浮点向量(例如 vec2、vec3、vec4),因此支持布尔向量也是一致的。如果语言包括对布尔向量和运算的支持,则可以简单地表达按元素的向量计算。例如,可以使用布尔向量运算在语言内优雅地实现 min 和 max 向量运算。

当比较运算符(如'<')应用于向量操作数时,结果是一个布尔向量,其中包含按元素比较的结果。如果第一个操作数是布尔向量,'?:'构造将以按元素方式运行。if、while 和 for 仍然需要标量布尔值

决议:将添加 bool 的向量。但是将保留 C 风格的 && 和 || 的短路求值。对于此版本,if 和 ?: 将仅根据标量 bool 进行选择。由于 == 和 != 也应该返回标量 bool(就像它们对所有类型一样,包括 struct),因此它们将这样做,并且在比较向量时不会返回 bool 的向量。因此,<、>、<= 和 >= 也不会创建 bool 的向量。

它们不会合法地对向量进行操作。相反,将为向量上的关系运算添加内置函数。

结案:2002 年 11 月 5 日。

## #71 着色语言是否应该支持复合数据结构,例如数组的数组、数组的结构体、结构体的数组等?

2002 年 9 月 17 日添加。

讨论:创建复合用户定义数据结构的能力是几乎所有高级编程语言的基本组成部分。省略对这些功能的支持将与 OpenGL 着色语言设计工作通常面向未来的性质不一致。

另一方面,与添加结构体的问题一样(问题 (49)),可以为每种语言提出理由,即它将在某个地方、某个时候对某人有用。花时间和精力确保此功能是规范第一版的一部分是否值得?将其推迟并稍后添加会导致任何问题吗?

2002 年 9 月 24 日决议:是的,着色语言应该支持数组的数组、数组的结构体、结构体的数组等。

2002 年 9 月 24 日结案。

## #72 着色语言是否应包括 switch 语句?

2002 年 9 月 19 日添加。

讨论:C 语言的 switch 语句在编写干净的代码时很有用。另一种选择是使用可读性较差的 if 语句集合。

决议:初始版本不会添加 Switch。有人希望管理浮点范围,这需要很长时间才能解决。

2002 年 10 月 1 日结案。

## #73 关键字 return 在 main() 函数中的语义是什么?

2002 年 9 月 19 日添加。

讨论:目前规范没有说明,如果关键字 return 包含在 main() 函数中除了函数最末尾之外的任何位置会发生什么。

决议:return 表示退出 main,就像到达末尾一样。它不意味着 kill。

2002 年 10 月 1 日结案。

## #74 顶点或片段着色器计算的数据如何传回应用程序?

2002 年 9 月 24 日添加。

讨论:此问题与问题 (50) 和问题 (54) 相关。如果允许顶点处理器或片段处理器计算不继续沿处理管道向下的结果(例如,直方图、最小/最大值或从控制点计算顶点数组数据),那些结果将如何传回应用程序?

决议:推迟到规范的未来版本。

结案:2002 年 10 月 22 日。

## #75 由应用程序初始化的 uniform 和 attribute 是否允许为结构体?

2002 年 9 月 25 日添加。

讨论:如果我们不允许这样做,应用程序必须通过单独的全局变量传递所有数据,着色器代码必须将数据打包在结构体中才能使用结构体。这会导致不必要的丑陋的着色器代码。另一方面,初始化结构体数据不应涉及 API 的复杂化。

此外,在语言中有结构体但没有从应用程序初始化它们的方法似乎是有问题的。另一方面,初始化结构体的完整解决方案似乎超出了此版本的范围。

还注意到,attribute 可以是矩阵,但没有用于初始化它们的 API。

决议:

- 添加入口点以初始化 attribute 矩阵。在绑定时,4x4 矩阵占用 4 个连续位置,3x3 矩阵占用 3 个连续位置,2x2 矩阵占用 2 个连续位置。

布局细节是隐藏的。如果实现只需要一个槽用于 2x2,它只需要使用一个槽,但对于需要两个槽的实现,空间是存在的。

- 尚不支持 attribute 的数组和 attribute 的结构体。

- 允许 uniform 结构体和结构体数组。

- 通过在 GetUniformLocation 时指定选择要初始化的成员的字符串,支持 API 初始化结构体成员。例如 "struct.member"、"struct[4].member"、

"struct[2].member[2]" 等。

- 尚不支持在 API 中进行结构体级别的初始化,等待未来完全理解步幅、对齐、填充等的完整解决方案。

2002 年 10 月 22 日结案。

## #76 vec2、vec3、vec4、mat2、mat3 和 mat4 是否应定义为结构体?

2002 年 9 月 26 日添加

讨论:将这些类型视为结构体,语言会更清晰,更容易指定。这也将简化编译器开发。

目前,这些类型涉及特殊的语言特性,如"v.xyz"语法和"v.yzx"等重组操作。然而,在 HLSL 中这种操作不是必需的。例如,重组操作是汇编语言技巧的典型,例如用两个汇编命令表示叉积。但在 HLSL 中不需要这样的技巧,只需使用内置函数,或在真正需要时定义用户定义的函数。此外,在极少数确实需要这种操作的情况下,可以通过标准语言特性(如 vec3(v.y, v.z, v.x))轻松表达。

此外,计划添加 v[i][] 之类的语法作为特殊的语言特性。但需要组件的动态索引的情况非常罕见,可以通过内置访问函数轻松表达。

另一个方面是对相同组件使用不同的名称,例如 xyzw 与 rgba。

失去这个特性可能是将 vec3 和 vec4 视为普通结构体的唯一真正缺点。另一方面,具有唯一的名称 xyzw 也可能使着色器程序更清晰。

另一方面,向量和矩阵类型确实很特殊:
(A)大多数运算符对它们起作用。对结构体则不然。
(B)向量或矩阵的每个元素都是相同的类型。对结构体则不然。
(C)矩阵在概念上是二维的,而结构体在概念上是一维的。当前的语言在保持矩阵 2D 方面并不完美,考虑到我们有时必须知道它的列主序,但有时可以将其视为 2D。
(D)硬件可能有特殊的向量和矩阵类型硬件,将其映射到通用结构体比映射到内置类型更困难。

决议:向量和矩阵不是结构体,但会做一些更改。总结:

- R 值重组成为表达式上的操作。这是对初始规范的更改,初始规范说它是向量变量上的成员选择器。

- 我们删除空括号语法。

- 我们保留已有的向量重组语法。

- 我们为向量保留数组访问语法。

- 我们为矩阵 m 添加,m[i] 是第 i 列,类型为向量。L 值和 R 值都是如此。

- 因为 m[i] 是向量,而向量有数组语法,所以自然而然地,m[i][j] 是 m 的第 i 列,第 j 行。

2002 年 10 月 8 日结案。

## #77 gl_FragStencil 和 gl_FBStencil 的类型是否应更改为 int?

这些当前是浮点数。整数类型在语言中有了进展,因此应该重新考虑这一点。

决议:是的。但是,模板写入已推迟到未来版本,并且已删除帧缓冲区读取。

2002 年 12 月 10 日结案。

## #78 模板是否会自动限制为可以存储在模板缓冲区中的当前最小值和最大值?

决议:是的。但是,模板写入已推迟到未来版本。

2002 年 12 月 10 日结案。

## #79 我们是否需要在片段着色器中提供近裁剪平面和远裁剪平面

讨论:ARB_fragment_program 有这些。

决议:是的。

2002 年 12 月 10 日结案。

## #80 矩形(非 2 的幂)纹理不是由 0.0 到 1.0 索引的,而是由它们的实际尺寸索引的。这是个问题吗?

讨论:是的,这是一个问题。某些硬件需要在编译时知道正在访问哪种纹理。我们希望避免由于状态更改而重新编译着色器。可以使用更多纹理内置名称,以便在编译时知道正在访问哪种纹理。类似于 textureRect3 表示返回 3 个分量的矩形纹理。矩形纹理也不是 OpenGL 1.4 的一部分,因此这些函数可以作为扩展添加,而不是此版本语言规范的一部分。

决议:为添加更多函数以支持其他纹理类型提供空间,但推迟为矩形纹理执行此操作,直到它们成为 OpenGL 核心的一部分。

2002 年 12 月 17 日结案。

## #81 我们是否应该支持从着色器访问固定功能雾的方式,以利用固定功能硬件中可能存在的雾?

讨论:这听起来类似于支持我们所做的固定功能变换。但是,这是出于不变性的需要,这不是雾的问题。它也在 ARB_fragment_program 中提供,但本规范的精神是用可编程性取代固定功能和访问它的额外方式。

决议:不会提供对固定管线雾访问的特殊支持。

2002 年 12 月 17 日结案。

## #82 真的有必要要求写入 gl_Position、gl_FragColor 或 gl_FragDepth 吗?

讨论:当写入是有条件的时,这对于处理错误情况可能很困难。还有讨论说,要么应该调用 kill,要么应该在片段着色器中写入 gl\_ 输出。但是,这是无关紧要的,因为在片段着色器中既不调用 kill 也不写入任何输出都是可以的。对于顶点着色器,不写入位置仍然没有意义,因此这应该仍然是必需的。

决议:对于片段着色器,没有规则;可以调用 kill 也可以不调用,如果不调用,则不需要写入任何内容,将从管道中获取现有值。对于顶点着色器,仍然应该写入 gl_Position,如果可能的话,编译器会给出诊断信息。

2002 年 12 月 10 日结案。

2003 年 1 月 7 日重新打开,关注编译器认为有条件地写入默认 gl_FragColor 和 gl_FragDepth 时的性能影响。对于颜色,不写入 gl_FragColor 应该是未定义的。对于深度,情况更复杂,因为如果不写入深度,则应使用固定功能计算的深度。但是,如果有条件地写入深度,编译器将始终必须初始化深度,这可能会影响性能。对此的进一步讨论产生了替代方案,即总是必须从着色器写入 gl_FragColor,或者基于源代码看起来的更复杂的事情。

决议:说明如果着色器有条件地写入 gl_FragDepth,则它必须始终写入它。

关于不变性的问题,请参见问题 (95)。

2003 年 1 月 17 日结案。

## #83 我们应该如何修改模板?这会影响 push/pop 状态吗?

讨论:在片段着色器中写入模板值为 OpenGL 引入了新功能。就其本身而言,它的用处值得怀疑。另一方面,现有的操作(如模板的增量和减量)无法用当前规范明显地表示。可能只有当片段着色器不写入任何输出并且 API 已设置为渲染到模板时,才应更新模板。但是,这尚未进行彻底调查。

决议:模板修改推迟到未来版本。

结案:2002 年 12 月 17 日。

## #84 我们应该添加投影纹理查找吗?SHADOW 纹理呢?

讨论:投影纹理查找可以推迟。阴影纹理是 1.4 的一部分,因此应该使用类似 textureShadow\* 的方式添加。但是,如果添加这些,同时添加投影查找也很简单。如果未启用阴影模式会怎样?期望是模型看起来像硬件只是按照应用程序的设置执行任何操作,并且着色器中的阴影调用会获取这样指定的内容。将使用单独命名的函数,因为对于阴影查找,为同一目标输入的数据比非阴影查找更多。阴影可以称为"compare"而不是"shadow",但 renderman 称其为"shadow"。

决议:添加投影和阴影纹理,使用新名称 proj 和 shadow 来反映这一点。此外,投影纹理将接受两种大小的输入:所有输入都将接受 vec4,再加上 2D 投影将接受 vec3 等。还要确保规范说,如果在未设置比较器的纹理上使用阴影内置函数,或在设置了比较器的纹理上使用非阴影内置函数,结果是未定义的。

2003 年 1 月 17 日结案。

## #85 编译器如何知道正在访问的是 1D、2D、3D 还是 CUBE 纹理?根据参数的分量数来判断容易出错

讨论:使用哪个纹理是基于查找坐标参数的类型。但是,可以想象支持将许多纹理绑定到同一个纹理单元编号,并且在编写着色器时,作者应该考虑他们正在访问哪一个,并且他们不应该因为类型错误而获得错误的纹理。明确说明要使用哪个纹理可以通过将枚举传递给现有的纹理调用来完成,或者通过在名称中添加纹理类型来添加新名称。使用枚举的问题是,这意味着可以将参数传递给变量,而要求是在编译时知道。这说明需要更改名称。

决议:添加投影和阴影纹理,使用新名称 proj 和 shadow 来反映这一点。此外,投影纹理将接受两种大小的输入:所有输入都将接受 vec4,再加上 2D 投影将接受 vec3 等。还要确保规范说,如果在未设置比较器的纹理上使用阴影内置函数,或在设置了比较器的纹理上使用非阴影内置函数,结果是未定义的。

2003 年 1 月 17 日结案。

## #85 编译器如何知道正在访问的是 1D、2D、3D 还是 CUBE 纹理?根据参数的分量数来判断容易出错 

讨论:使用哪个纹理是基于查找坐标参数的类型。但是,可以想象支持将许多纹理绑定到同一个纹理单元编号,并且在编写着色器时,作者应该考虑他们正在访问哪一个,并且他们不应该因为类型错误而获得错误的纹理。明确说明要使用哪个纹理可以通过将枚举传递给现有的纹理调用来完成,或者通过在名称中添加纹理类型来添加新名称。使用枚举的问题是,这意味着可以将参数传递给变量,而要求是在编译时知道。这说明需要更改名称。

决议:添加更多纹理名称,以便在编译时明确正在进行什么类型的纹理查找。类似于 texture1D3、texture2D3、texture3D3 和 textureCube3,其中最后一个数字是返回的分量数(可以是 1、2、3 或 4)。

结案:2002 年 12 月 17 日。

## #86 规范中很少提及颜色索引。它提供了什么支持?

讨论:其他扩展(ARB_vertex_program、ARB_fragment_program、纹理应用)说,如果在 COLOR INDEX 模式下渲染,操作是未定义的。

决议:删除对此的引用,除了说 COLOR INDEX 操作未定义之外。

结案:2002 年 12 月 10 日。

## #87 辅助数据缓冲区是 OGL2 白皮书的一部分,但本规范需要基于当前的 OGL 核心

决议:从规范中删除辅助数据缓冲区。如果/当未来扩展在核心 GL 中提供其他要写入的缓冲区时,可以将它们添加回来。

结案:2002 年 12 月 17 日。

## #88 某些数组的变量数组索引可能难以实现

讨论:数组可以限制为仅 uniform。但是,这具有限制性,其他图形语言有局部变量数组。它还缺乏与包含数组的结构体的正交性,但可以用作 uniform 或局部变量。将非 uniform 数组的索引限制为 uniform 索引可能会有所帮助。着色器初始化整个非 uniform 数组然后用非 uniform 索引对其进行索引的可能性似乎不大。(非 uniform 索引更有可能应用于静态数组或纹理。)另一方面,由于这种情况不太可能,语言规范可以保持干净和完整功能,并且在接下来的一两年内,编译器抱怨某些东西太复杂是可以的(考虑到它是一个不太可能的需求)。

决议:将指定完整的数组支持。

结案:2003 年 1 月 7 日。

## #89 为什么 varying gl_TexCoord0...gl_TexCoordn 不是数组?

讨论:在编译时知道这些内容或不允许变量索引可能对性能或编译器方便有一些好处。这可能与问题(88)相互影响,如果对 varying 数组的索引必须是 uniform 的,那么将这些内容作为数组支持会更容易。

决议:这些将更改为数组。为上面列出的 2 个问题制定详细信息。

结案:2003 年 1 月 7 日。

重新打开:问题(88)已通过全面的数组支持得到解决。但是,这给纹理坐标留下了两个可能的问题。i)无法知道着色器中实际激活了多少坐标,以及 ii)无法在编译时知道正在访问哪些坐标。

讨论:备选方案:

1. Cg 对常量循环迭代说类似这样的话:"可以在编译时确定"定义如下:通过使用过程内常量传播和折叠,可以在编译时评估循环迭代表达式,其中传播常量值的变量不会作为任何类型的控制语句(if、for 或 while)或 ?: 构造中的左值出现。
2. 非常严格:它必须是编译时常量或具有编译时常量开始/结束/增量值的归纳变量。
3. 实际解决基本资源大小问题:如果违反 #2,让着色器编写者重新声明数组。也就是说,允许着色器声明 "varying gl_TexCoord[N]",其中 N 是他们要使用的坐标数量,如果不满足#2,则需要它。允许在语言中完全变量访问(当硬件/编译器变得太难时提早警告。)
4. 使用 #pragma 来做 #3。
5. 变体:要么 gl_TexCoord[] 在着色器中的所有索引都必须是常量表达式,要么着色器必须用大小声明 gl_TexCoord[]。内置应该声明为空数组,这样再次用大小声明它就与 C 一致了。多个模块可以用不同的大小声明它,链接时将使用最大值。

决议:使用 #5。

结案:2003 年 1 月 24 日。

## #90 内置函数指定了返回整数的纹理函数。但是,这样的纹理不在核心 OpenGL 中

决议:将删除这些内置函数。它们可以作为未来向 OpenGL 添加整数纹理的规范的一部分重新添加。

结案:2002 年 12 月 17 日。

## #91 片段着色器中似乎缺少雾信息。gl_EyeZ 还不够。应该有一些派生信息吗?

决议:将 float gl_EyeZ 扩展为 vec4 gl_FogFragCoord。

结案:2002 年 12 月 17 日。

## #92 我们需要一种方法来获取目标代码,就像主机处理器上的 C 模型一样

讨论:电子邮件中有很多内容。这是关于最低级别的、特定于机器的代码,甚至可能无法在同一供应商的一系列卡内移植。一个主要目标是节省编译时间。似乎普遍认为这有优点,但不影响语言定义。

决议:这是一个 API 问题,而不是语言问题。

2002 年 12 月 19 日结案,作为 API 问题。

## #93 实际上,顶点着色器的输出 varying 接口与片段着色器的输入 varying 接口不同,但本规范共享一个接口。这会导致雾的问题,并且缺乏与固定功能和 ARB_fragment_program 的混合和匹配的兼容性

讨论:分割这个接口是有意义的。片段输入应该与 ARB_fragment_program 兼容,顶点输出应该与 ARB_vertex_program 兼容。可以通过这种方式处理雾,规范中有反映

决议:应该这样做。

2003 年 1 月 17 日结案。

## #94 按照当前的语言规范,无法在编译时知道在哪些单元上使用哪些目标。如果认为硬件必须提前设置正确的目标在正确的单元上,这是一个大问题

讨论:可能的解决方案:

A. 使用传统的 OGL 启用、绑定和优先级,允许应用程序在运行着色器时传递需要激活的目标。

B. 更改语言,要求编译时检查足以查看在哪个单元上使用哪个目标。

C. 期望硬件能够动态访问请求的目标,而无需驱动程序事先设置。

D. 为设置活动纹理向 API 添加一个新的入口点:a)BindTextureGL2(GLenum textureUnit, GLenum GLuint texture)(可能没有 textureUnit 参数)但似乎没有人想要这样的函数,因为与标准管线不兼容。b)另一个选项可能是一个新的实用程序函数 UseTexture(GLenum textureUnit, GLenum GLuint texture),它完全等同于 i)绑定纹理,ii)启用此纹理的目标,iii)禁用所有其他目标。如果纹理为 NULL,则禁用所有目标。此解决方案将与标准管线 100% 兼容;新函数仅用于方便,不提供新功能(可能作为 glu 函数)。

E. 像 Cg 那样使用 'samplers'。

决议:使用 samplers。将添加类型 sampler1D、sampler2D、sampler3D 和 samplerCube。它们在着色器中不可写。它们只能是全局 uniform。它们可以是数组。它们是不透明的,不能在着色器内操作,只能引用。纹理调用的第一个参数将接受它们。API 必须以某种方式将纹理或纹理/单元绑定到它们。启用、活动纹理等被这些 API 绑定取代。在语言级别,它们将为未来可能的纹理虚拟化提供基础。

另请参阅问题(97)。

2003 年 1 月 17 日结案。

## #95 是否需要解决 gl_FragDepth 或着色语言其他方面的不变性问题?

讨论:有人在这里提出了一些担忧,但尚不清楚问题/解决方案是什么。

1 决议:对于 gl_FragDepth,说在固定功能深度和着色器计算深度之间没有不变性保证。但是,在同一着色器或不同着色器中多次使用相同的着色器深度计算将是不变的。

将其他方差问题推迟到下一个版本。

2003 年 1 月 17 日结案。

## #96 对于各向异性过滤,lod 内置函数没有意义

讨论:似乎我们需要 lod1D、lod2D、lod3D、lodCube。但是,考虑到当前纹理内置函数接受偏差而不是绝对的 lod,以及如果添加各向异性过滤,lod 的规范会有问题,lod() 内置函数似乎没有用处。

决议:删除 lod 内置函数。

2003 年 1 月 17 日结案。

## #97 我们是否需要阴影采样器?例如 sampler1DShadow、sampler2DShadow 类型?

讨论:这提供了更高级别的强制执行,可以将正确的纹理状态与正确的内置查找函数挂钩。另一方面,它为添加大量类型关键字设定了方向。

决议:是的,添加这些类型。

2003 年 1 月 24 日结案。

## #98 vec2、vec3、vec4 应该改为 float2、float3、float4,以与其他语言保持一致。kill 同样应该改为 discard。

讨论:应将 vec2、vec3、vec4 替换为 float2、float3、float4,以匹配 Stanford RSL、Cg 和 HLSL 的既定约定。

此外,这些名称允许更好地支持基于其他标量的数据类型(int2、int3、int4、half2、half3、double4 等)。

另一方面,似乎有一个一致的方案,其中向量由前缀类型缩写(默认为 float)、"vec" 和分量数量组成。

| 类型    | 4 分量向量 |
| ------- | ---------- |
| float   | vec4       |
| int     | ivec4      |
| bool    | bvec4      |
| half    | hvec4      |
| double  | dvec4      |
| float16 | f16vec4    |
| int32   | i32vec4    |

我们还应该将 "kill" 改为 "discard" 吗?理由是 OpenGL 规范在丢弃片段时始终使用术语 "discard",而从不使用 kill。KIL 指令确实出现在 ARB_fragment_program 和 NV_fragment_program 规范中(可能是因为它为三字母指令提供了一个不错的助记符),但这与核心 OpenGL 规范并不一致。另一个原因是 Cg/HLSL 将 "discard" 作为关键字,没有充分的理由为相同的功能提供冗余的关键字。

决议:将 kill 改为 discard,保留向量的原样。

2003 年 1 月 24 日结案。

## #99 缺少处理内存中非常大的表的机制

讨论:希望能够有效地将大量信息传递给顶点着色器以支持高级动画技术。对于适合 "uniform" 空间的数组,这在今天可以做到,但对于非常大的数据结构则不行。需要一种在语言中声明它们的方法,并需要 API 支持来初始化它们。可以说这个功能大部分可以通过纹理查找实现。但是,对于内存中的大型数组来说,这似乎不是正确的抽象。

决议:将此推迟到未来版本。在此问题中包含规范。

2003 年 1 月 24 日结案

## #100 如果将来在顶点着色器中添加 lod-bias 形式,或者(更可能)在片段着色器中添加绝对 lod 形式,我们将与纹理内置函数发生名称冲突

决议:将偏差形式称为我们现在使用的名称。将绝对 lod 形式称为我们现在使用的名称,后缀为 "Lod"。

2003 年 1 月 24 日结案。

## #101 添加 uniform 的初始化。例如,uniform vec3 Color = vec3(1.0, 1.0, 0.0);

讨论:ISV 和 NVIDIA 已经提出了要求。似乎没有必要,但如果大多数人想要这个,我们可以添加它。还需要考虑为采样器执行此操作(但不是数组?)。请注意,仅对于在渲染期间不变的 uniform 才有意义,const 全局变量可用,不能指定纹理,因此这可能没有某些人认为的那么有用。不过它还是有价值的。

决议:不做任何事情。为语言的未来版本保留向后兼容的增强功能。

结案:2003 年 12 月。

## #102 修复数组(多维数组、数组成员的静态初始化、可变大小数组、非大小数组参数)

讨论:函数参数声明 "vec4[ ]" 没有说明数组有多大,有点暗示按引用传递。调用约定是按值传递。此语法应保留用于将来可能添加的按引用传递数组。要按值传递数组,应该说明大小 "vec4[5]"。不幸的是,这会阻止使用两个不同大小的数组调用同一个函数。但是,也许在有一个数组按引用传递机制之前,应该等待执行此操作。静态初始化可以通过使用类型的构造函数名称后跟方括号来完成。例如 "vec4[5] vArray = vec4[ ](v1, v2, v3, v4, v5);"

一旦走上这条路,就可以进一步将数组作为一等对象。它可以主张声明如 "float[7] fArray",并允许复制和比较数组。这也意味着消除数组的自动大小调整,这在使保存插值资源变得容易方面具有其他用途。更进一步,可以添加多维数组,我们必须考虑数组的数组、typedef 和真正的多维数组,如 "float[7,3] fMultiArray"。

决议:保守地进行更改,但不要排除将来添加所有这些功能的向后兼容性。允许将来向后兼容的最小更改是:使函数参数声明需要数组大小,并使对同一全局数组使用不同大小的不同着色器成为链接错误。这允许我们保留现有的 gl_TexCoord 自动大小调整。要根据参数类型选择函数调用,数组大小必须匹配,否则会出现 "没有匹配的重载函数" 错误。其余部分推迟到未来版本。

结案:2003 年 12 月。

## #103 澄清规范中使用少于所需元素初始化的向量矩阵构造函数。是删除最后一行/列还是按顺序初始化元素?用户会期望前者。此外,限制构造函数并使它们以更合理的方式运行

讨论:规范说必须提供足够的组件。缺少有趣的构造函数,用于以非列主序构建矩阵。今天也没有对可以在有效程序中传递给构造函数的参数数量的上限。我们也可以列举所有有用的构造函数,并准确说明每个构造函数的作用。此外,在使用构造函数作为类型转换器和类型构建器之间存在一些模糊性。通过一个干净的原型列表,这将变得更加清晰。之前为了简化规范而避免使用完整列表。

决议:限制构造函数的参数数量。最后一个有用的参数可以具有比需要更多的组件,并且只是部分使用,但超出这个数量的参数是不允许的。此外,防止其他今天无用但将来可能被定义为有用的构造。这些将是从其他不同大小的矩阵构造的矩阵。例如 mat4(mat3) 或 mat2(mat4)。实际支持新类型的构造函数推迟到未来版本。

结案:2003 年 12 月。

## #104 应该有一个快速的 atan() 等吗?

讨论:有两个不同的方向。一个是添加性能与准确性的权衡。另一个是添加域限制函数。两者都可以支持,使用新的名称集。例如:

atanDom() 将是一个域限制反正切函数。

atanFast() 将是一个低精度、高性能的反正切函数。

决议:先作为扩展来做。

结案:2003 年 12 月。 

## #105 更改规范,以便可以通过连接跨着色器对象的所有着色器字符串来生成代码

讨论:仅在链接时进行完全编译和一步链接有一些实用性。一种方法是将所有着色器连接在一起(在顶点内或片段内,而不是跨顶点和片段),然后解析它们。它们仍然必须在编译时解析,以返回错误等,但会在链接时再次解析。

决议:不这样做。在进行此更改时很容易出现规范错误。

结案:2003 年 12 月。