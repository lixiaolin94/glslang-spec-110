# 致谢

如果说我看得更远,那是因为我站在巨人的肩膀上。——艾萨克·牛顿

计算机科学和计算机图形学文献中可以找到所提议语言中的绝大多数思想和元素。3Dlabs 的 Dave Baldwin 撰写了构成 OpenGL 着色语言基础的白皮书。他最初的致谢包括:

- AT&T 的 C 语言,
- Pixar 的 RenderMan,
- UNC 的 Pixel Flow 语言和他们可编程的 OpenGL 接口,
- Stanford 的着色语言工作,
- SGI 的 OpenGL(显然)和他们的着色语言研究,
- 3Dlabs 的同事们频繁而积极的讨论,有助于澄清许多观点

随着着色语言的发展,Dave Baldwin 一直参与着色语言的设计。

Randi Rost 和 John Kessenich 编辑了着色语言白皮书的几个公开版本,并创建了本规范文档的第一个版本。3Dlabs 的 Antonio Tejada 编写了该语言的第一个解析器,以帮助解决一些基本的语言设计问题。

3Dlabs 的 Randi Rost、John Kessenich、Barthold Lichtenbelt 和 Steve Koren 组成了一个团队,在初步确定方向后,负责 OpenGL 着色语言的设计和实现。该小组负责为 OpenGL 着色语言生成公开的规范和源代码,负责生成语言编译器和支持 OpenGL 扩展的初始实现,并负责修改语言的设计。

3Dlabs 的 Dave Baldwin、Dale Kirkland、Jeremy Morris、Phil Huxley 和 Antonio Tejada 参与了许多 OpenGL 着色语言的讨论,并提供了大量好的想法和鼓励,让我们不断前进。位于英国 Egham、阿拉巴马州 Huntsville 和德克萨斯州 Austin 的 3Dlabs 驱动程序开发团队的其他成员也做出了贡献。3Dlabs 的管理层应该因为有远见地推进 OpenGL 着色语言提案,并有勇气为其开发分配资源而受到赞扬。特别感谢 Osman Kent、Neil Trevett、Jerry Peterson 和 John Schimpf。

还有许多其他人参与了 OpenGL 着色语言的讨论。我们要感谢 ATI、SGI、NVIDIA、Intel、Microsoft、Evans & Sutherland、IBM、Sun、Apple、Imagination Technologies、Dell、Compaq 和 HP 的同事和 ARB 代表,感谢他们为讨论做出的贡献,感谢他们帮助推进这一过程。特别是 ATI Research 的 Bill Licea-Kane 和 Evan Hart,他们通过富有洞察力的评论和认真的评论帮助改进了规范和语言本身。

我们要特别感谢那些花时间与我们交谈、给我们发邮件或在 OpenGL.org 上回答调查问题的软件开发人员。我们的最终目标是为您提供尽可能最好的 API 来进行图形应用程序开发,您花时间告诉我们您的需求的时间是无价的。一些 ISV 长期以来一直在为某些功能进行游说,他们说服我们对最初的 OpenGL 2.0 提案做出了一些重大改变。谢谢所有的软件开发人员,请继续告诉我们您的需求!

其他为着色语言提供有用评论或评审的人包括 Alias|Wavefront 的 Christian Laforte 和 Ian Ameline;Imagination Technologies 的 Jonathan Putsman;LightWork Design 的 Darren Roberts 和 Slawek Kilanowski;ID software 的 John Carmack;Epic Games 的 Tim Sweeney 和 Daniel Vogel;马格德堡大学的 Bert Freudenberg;Spinor 的 Folker Schamel;Vital Images 的 Karel Zuiderveld 和 Steve Demlow;Volume Graphics 的 Christoph Poliwoda、Christof Reinhart 和 Wolfgang Roemer;Pinnacle 的 Christian Schormann;Whatif Productions 的 Jake Kolb V;Softimage 的 Mik Wells;5D Solutions 的 Delwyn Holroyd 和 Gerk Huisma;斯坦福大学的 Kurt Akeley、Pat Hanrahan 和 Bill Mark;以及苹果公司的 John Stauffer。

帮助塑造着色器对象和程序对象方向的人包括 Alias|Wavefront 的 Christian Laforte;Discreet 的 Pierre Tremblay;Intel 的 Bimal Poddar;Matrox 的 Jon Paul Schelter;Imagination Technologies 的 Jonathan Putsman;Softimage 的 Mik Wells;Vital Images 的 Karel Zuiderveld 和 Steve Demlow;以及 Epic Games 的 Tim Sweeney 和 Daniel Vogel。

2002 年 6 月,这份规范文件正式提交给了 OpenGL 架构评审委员会的 GL2 工作组。该小组负责识别问题、解决问题,并最终确定规范供 OpenGL 架构评审委员会批准。以下 GL2 工作组成员为本规范的定稿做出了贡献:3Dlabs 公司的 Dave Baldwin、John Kessenich、Steve Koren、Barthold Lichtenbelt 和 Randi Rost;ATI Research 公司的 Evan Hart、Bill Licea-Kane(ARB-GL2 工作组主席)和 Victor Vedovato;戴尔的 Dave Zenz;Intel 的 Brandon Fiflet 和 Kent Lin;NVIDIA 的 Pat Brown、Matt Craighead、Cass Everett、Steve Glanville、Jayant Kolhe 和 Nick Triantos;SGI 的 Jon Leech;Spinor 的 Folker Schamel;Tungsten Graphics 的 Brian Paul;Quel solaar 的 Eskil Steenburg;马里兰大学巴尔的摩分校的 Marc Olano;滑铁卢大学的 Michael McCool;以及 Vital Images 的 Matt Cruikshank、Steve Demlow 和 Karel Zuiderveld。

最后,我们要感谢 C 语言、RenderMan 和 OpenGL 的设计者,他们是对我们工作影响最大的三个标准。希望 OpenGL 着色语言能延续他们的成功和卓越传统。
