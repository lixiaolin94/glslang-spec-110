# 变量和类型

所有变量和函数必须在使用前声明。变量和函数名是标识符。

没有默认类型。所有变量和函数声明都必须具有声明的类型，并可选择限定符。通过指定类型后跟一个或多个以逗号分隔的名称来声明变量。在许多情况下，可以在声明变量时使用赋值运算符（`=`）对其进行初始化。本文档末尾附近的语法提供了声明变量语法的完整参考。

可以使用 **struct** 将现有类型的列表聚合到单个名称下，从而定义用户定义的类型。

OpenGL 着色语言是类型安全的。类型之间没有隐式转换。

## 基本类型

OpenGL 着色语言支持以下基本数据类型。

| 类型            | 描述                               |
| --------------- | ---------------------------------- |
| void            | 用于不返回值的函数                 |
| bool            | 条件类型，取值为 `true` 或 `false` |
| int             | 有符号整数                         |
| float           | 单精度浮点标量                     |
| vec2            | 两分量浮点向量                     |
| vec3            | 三分量浮点向量                     |
| vec4            | 四分量浮点向量                     |
| bvec2           | 两分量布尔向量                     |
| bvec3           | 三分量布尔向量                     |
| bvec4           | 四分量布尔向量                     |
| ivec2           | 两分量整数向量                     |
| ivec3           | 三分量整数向量                     |
| ivec4           | 四分量整数向量                     |
| mat2            | 2×2 浮点矩阵                       |
| mat3            | 3×3 浮点矩阵                       |
| mat4            | 4×4 浮点矩阵                       |
| sampler1D       | 用于访问 1D 纹理的句柄             |
| sampler2D       | 用于访问 2D 纹理的句柄             |
| sampler3D       | 用于访问 3D 纹理的句柄             |
| samplerCube     | 用于访问立方体贴图纹理的句柄       |
| sampler1DShadow | 用于带比较的访问 1D 深度纹理的句柄 |
| sampler2DShadow | 用于带比较的访问 2D 深度纹理的句柄 |

此外，着色器可以使用数组和结构体来构建更复杂的类型。

没有指针类型。

### Void

不返回值的函数必须声明为 `void`。没有默认的函数返回类型。

### 布尔值

为了更容易表达代码的条件执行，支持 `bool` 类型。不期望硬件直接支持此类型的变量。它是一个真正的布尔类型，只能取两个值之一，表示真或假。可以使用两个关键字 `true` 和 `false` 作为布尔常量。布尔值的声明和可选初始化如以下示例所示：

``` glsl
bool success;       // 声明"success"为布尔值
bool done = false;  // 声明和初始化"done"
```

赋值运算符（=）右侧可以是任何类型为 `bool` 的表达式。

用于条件跳转（`if`、`for`、`?:`、`while`、`do-while`）的表达式必须求值为 `bool` 类型。

### 整数

整数主要作为编程辅助工具支持。在硬件层面，真正的整数有助于高效实现循环和数组索引，以及引用纹理单元。然而，语言中的整数不要求映射到硬件中的整数类型。不期望底层硬件完全支持广泛的整数操作。由于其预期（有限）目的，在顶点和片段语言中，整数都限制为 16 位精度，外加一个符号表示。OpenGL 着色语言实现可以将整数转换为浮点数来对其进行操作。实现允许使用超过 16 位精度来操纵整数。因此，没有可移植的环绕行为。溢出 16 位精度的着色器可能不具有可移植性。

整数的声明和可选的整数表达式初始化如以下示例所示：

``` glsl
int i, j = 42;
```

整数字面量常量可以用十进制（基数 10）、八进制（基数 8）或十六进制（基数 16）表示，如下所示。

``` glsl
integer-constant:
  decimal-constant
  octal-constant
  hexadecimal-constant

decimal-constant:
  nonzero-digit
  decimal-constant digit

octal-constant:
  0
  octal-constant octal-digit

hexadecimal-constant:
  0x hexadecimal-digit
  0X hexadecimal-digit
  hexadecimal-constant hexadecimal-digit

digit:
  0
  nonzero-digit

nonzero-digit: one of
  1 2 3 4 5 6 7 8 9

octal-digit : one of
  0 1 2 3 4 5 6 7

hexadecimal-digit: one of
  0 1 2 3 4 5 6 7 8 9
  a b c d e f
  A B C D E F
```

在整数常量的数字之间不允许有空格，包括前导的 `0` 之后或前导的 `0x` 或 `0X` 之后。前导的一元负号（`-`）被解释为算术一元负号，而不是常量的一部分。没有字母后缀。

### 浮点数

浮点数可用于各种标量计算。浮点变量定义如下例所示：

``` glsl
float a, b = 1.5;
```

作为处理单元的输入值，浮点变量在精度和动态范围方面应与 IEEE 单精度浮点定义相匹配。不要求内部处理的精度与 IEEE 浮点规范中的浮点运算相匹配，但必须满足 OpenGL 1.4 规范确定的精度准则。类似地，对于除以 0 等条件的处理可能导致未指定的结果，但在任何情况下，这种情况都不应导致处理中断或终止。

浮点常量定义如下。

``` glsl
floating-constant:
  fractional-constant exponent-partopt
  digit-sequence exponent-part

fractional-constant:
  digit-sequence . digit-sequence
  digit-sequence .
  . digit-sequence

exponent-part:
  e signopt digit-sequence
  E signopt digit-sequence

sign: one of
  + –

digit-sequence:
  digit
  digit-sequence digit
```

如果存在指数部分，则不需要小数点（.）。

### 向量

OpenGL 着色语言包括用于浮点值、整数或布尔值的通用 2、3 和 4 分量向量的数据类型。浮点向量变量可用于存储计算机图形中非常有用的各种内容：颜色、法线、位置、纹理坐标、纹理查找结果等。布尔向量可用于数值向量的分量比较。将向量定义为着色语言的一部分，允许将向量操作直接映射到能够进行向量处理的图形硬件上。通常，通过对向量而不是标量值进行计算，应用程序将能够更好地利用图形硬件中的并行性。向量声明的一些示例如下：

``` glsl
vec2 texcoord1, texcoord2;
vec3 position;
vec4 myRGBA;
ivec2 textureLookup;
bvec3 lessThan;
```

向量的初始化可以通过构造函数完成，这将很快讨论。

### 矩阵

矩阵是计算机图形学中另一个有用的数据类型，OpenGL 着色语言定义了对 2×2、3×3 和 4×4 浮点数矩阵的支持。矩阵以列主序读取和写入。矩阵声明示例：

``` glsl
mat2 mat2D;
mat3 optMatrix;
mat4 view, projection;
```

矩阵值的初始化通过构造函数完成（在第 5.4 节“构造函数”中描述）。

### 采样器

采样器类型（例如 `sampler2D`）实际上是纹理的不透明句柄。它们与内置纹理函数（在第 8.7 节“纹理查找函数”中描述）一起使用，以指定要访问的纹理。它们只能声明为函数参数或 uniform（见第 4.3.5 节"Uniform"）。

采样器不允许作为表达式中的操作数，也不能被赋值。作为 uniform，它们使用 OpenGL API 进行初始化。作为函数参数，只能将采样器传递给匹配类型的采样器。这使得在运行着色器之前，可以在着色器纹理访问和 OpenGL 纹理状态之间进行一致性检查。

### 结构体

可以使用 `struct` 关键字将其他已定义的类型聚合到一个结构中，从而创建用户定义的类型。例如，

``` glsl
struct light {
  float intensity;
  vec3 position;
} lightVar;
```

在此示例中，light 成为新类型的名称，lightVar 成为 light 类型的变量。

要声明新类型的变量，请使用其名称（不带 `struct` 关键字）。

``` glsl
light lightVar2;
```

更正式地说，结构体声明如下。但是，完整的正确语法在第 9 节“着色语言语法”中给出。

``` glsl
struct-definition:
  qualifier(opt) struct name(opt) { member-list } declarators(opt);

member-list:
  member-declaration;
  member-declaration member-list;

member-declaration:
  basic-type declarators;
  embedded-struct-definition

embedded-struct-definition:
  struct name(opt) { member-list } declarator;
```

其中 name 成为用户定义的类型，可用于声明此新类型的变量。

该名称与其他变量和类型共享相同的名称空间，具有相同的作用域规则。可选的 qualifier 仅适用于任何声明符，而不是为 name 定义的类型的一部分。

结构体必须至少有一个成员声明。成员声明符不包含任何限定符。它们也不包含任何位字段。成员类型必须已经定义（没有前向引用），或通过嵌入另一个结构体定义来就地定义。成员声明不能包含初始化器。成员声明符可以包含数组。这样的数组必须指定一个大小，并且该大小必须是一个大于零的整型常量表达式（参见第 4.3.3 节“整型常量表达式”）。结构体的每个级别都有自己的名称空间，用于在成员声明符中给出的名称；这些名称只需在该命名空间内唯一即可。

不支持匿名结构体；因此嵌入式结构体必须有一个声明符。给嵌入式结构体的名称的作用域与其嵌入的结构体的作用域相同。

结构体可以在声明时使用构造函数进行初始化，如第 5.4.3 节“结构体构造函数”中所述。

### 数组

可以通过声明一个名称，后跟括号（[ ]）并括住一个可选的大小，将相同类型的变量聚合到数组中。在声明中指定数组大小时，它必须是一个大于零的整型常量表达式（参见第 4.3.3 节“整型常量表达式”）。如果使用非整型常量表达式索引数组或将其作为参数传递给函数，则必须在任何此类使用之前声明其大小。可以在不指定大小的情况下声明数组，然后将相同的名称重新声明为具有相同类型和指定大小的数组，这是合法的。在声明了一个带有大小的数组之后，在（同一着色器中）使用大于或等于声明大小的整型常量表达式索引同一数组是非法的。使用负常量表达式索引数组也是非法的。声明为函数声明中的形式参数的数组必须指定大小。

使用大于或等于数组大小或小于 0 的非常量表达式索引数组会导致未定义的行为。只能声明一维数组。所有基本类型和结构体都可以形成数组。一些例子是：

``` glsl
float frequencies[3];
uniform vec4 lightPosition[4];
light lights[];
const int numLights = 2;
light lights[numLights];
```

没有机制可以在着色器内部的声明时初始化数组。

## 作用域

变量的作用域由其声明位置决定。如果在所有函数定义之外声明，则具有全局作用域，从声明处开始，持续到声明它的着色器的末尾。如果在 `while` 测试或 `for` 语句中声明，则作用域到下一个子语句的末尾。否则，如果在复合语句中作为语句声明，则作用域到该复合语句的末尾。如果在函数定义中声明为参数，则作用域到该函数定义的末尾。函数体具有嵌套在函数定义内部的作用域。`if` 语句的表达式不允许声明新变量，因此不形成新的作用域。

声明为空数组的变量可以重新声明为具有相同基本类型的数组。否则，在一个编译单元内，具有相同名称的变量不能在相同的作用域内重新声明。但是，嵌套作用域可以覆盖外部作用域对特定变量名称的声明。

嵌套作用域中的声明提供了与被覆盖名称关联的存储分离的存储。

同一作用域内的所有变量共享同一个名称空间。函数名称始终可以根据上下文识别为函数名称，并且它们有自己的名称空间。

共享全局变量是在链接在一起形成单个程序的相同语言（顶点或片段）的独立编译单元（着色器）中以相同名称声明的全局变量。

共享全局变量共享同一个命名空间，并且必须声明为相同的类型。它们将共享相同的存储。共享全局数组必须具有相同的基本类型和相同的大小。标量必须具有完全相同的类型名称和类型定义。结构体必须具有相同的名称、类型名称序列、类型定义和字段名称才能被视为相同的类型。此规则递归适用于嵌套或嵌入的类型。共享全局变量的所有初始化器必须具有相同的值，否则将导致链接错误。

## 类型限定符

变量声明可以在类型前面有一个或多个限定符。这些总结如下：

|                   |                                                                       |
| ----------------- | --------------------------------------------------------------------- |
| < none: default > | 局部读/写内存，或函数的输入参数                                       |
| const             | 编译时常量，或只读的函数参数                                          |
| attribute         | 顶点着色器和 OpenGL 之间的链接，用于逐顶点数据                        |
| uniform           | 在处理的图元中值不变，uniform 形成着色器、OpenGL 和应用程序之间的链接 |
| varying           | 顶点着色器和片段着色器之间的链接，用于插值数据                        |
| in                | 用于传递到函数中的函数参数                                            |
| out               | 用于从函数中传回的函数参数，但在传入时不会初始化                      |
| inout             | 用于传入和传出函数的函数参数                                          |

全局变量只能使用限定符 `const`、`attribute`、`uniform` 或 `varying`。只能指定一个。

局部变量只能使用限定符 `const`。

函数参数只能使用 `in`、`out`、`inout` 或 `const` 限定符。参数限定符在第 6.1.1 节“函数调用约定”中有更详细的讨论。

函数返回类型和结构体字段不使用限定符。

用于将着色器的一次运行与下一次运行进行通信的数据类型（在片段之间或顶点之间进行通信）不存在。这将阻止在多个顶点或片段上并行执行同一着色器。

没有限定符或只有 `const` 限定符的全局变量声明可以包含初始化器，在这种情况下，它们将在执行 `main()` 的第一行之前初始化。这样的初始化器必须具有常量类型。没有限定符且未在声明中或应用程序中初始化的全局变量不会由 OpenGL 初始化，而是将以未定义的值进入 `main()`。

### 默认限定符

如果全局变量没有限定符，则该变量与应用程序或在其他处理器上运行的着色器没有链接。对于未限定的全局变量或局部变量，声明将显示为分配与其目标处理器相关的内存。该变量将提供对此分配内存的读/写访问。

### Const

可以使用 `const` 限定符声明命名的编译时常量。任何限定为常量的变量对于该着色器都是只读变量。将变量声明为常量允许比使用硬编码的数值常量更具描述性的着色器。`const` 限定符可以与任何基本数据类型一起使用。在声明之外写入 `const` 变量是错误的，因此必须在声明时初始化它们。例如，

``` glsl
const vec3 zAxis = vec3 (0.0, 0.0, 1.0);
```

结构体字段不能使用 `const` 限定。结构体变量可以声明为 `const`，并使用结构体构造函数初始化。

`const` 声明的初始化器必须由字面值、其他 `const` 变量（不包括限定为 `const` 的函数调用参数）或这些表达式组成。

构造函数可以在此类表达式中使用，但不能使用函数调用。

### 整型常量表达式

整型常量表达式可以是以下之一：

- 字面整数值
- 限定为 `const` 的全局或局部标量整数变量，不包括限定为 `const` 的函数参数
- 操作数为整型常量表达式的表达式，包括构造函数，但不包括函数调用。

### Attribute

`attribute` 限定符用于声明从 OpenGL 逐顶点传递给顶点着色器的变量。在顶点着色器以外的任何类型的着色器中声明 attribute 变量都是错误的。就顶点着色器而言，attribute 变量是只读的。attribute 变量的值通过 OpenGL 顶点 API 或作为顶点数组的一部分传递给顶点着色器。它们将顶点属性传递给顶点着色器，并且预期在每次顶点着色器运行时都会改变。attribute 限定符只能与数据类型 `float`、`vec2`、`vec3`、`vec4`、`mat2`、`mat3` 和 `mat4` 一起使用。

attribute 变量不能声明为数组或结构体。

声明示例：

``` glsl
attribute vec4 position;
attribute vec3 normal;
attribute vec2 texCoord;
```

所有标准 OpenGL 顶点属性都有内置变量名，以允许用户程序和 OpenGL 顶点函数之间的轻松集成。有关内置 attribute 名称的列表，请参见第 7 节“内置变量”。

预期图形硬件将具有用于传递顶点属性的少量固定位置。因此，OpenGL 着色语言将每个非矩阵 attribute 变量定义为具有最多四个浮点值（即 vec4）的空间。可以使用的 attribute 变量数量有一个实现相关的限制，如果超过此限制，将导致链接错误。（未使用的已声明 attribute 变量不计入此限制。）float attribute 与 vec4 相比，对此限制的影响相同，因此应用程序可能需要考虑将四个不相关的 float attribute 打包在一起成为 vec4，以更好地利用底层硬件的功能。mat4 attribute 将使用相当于 4 个 vec4 attribute 变量位置的空间，mat3 将使用相当于 3 个 attribute 变量位置的空间，而 mat2 将使用 2 个 attribute 变量位置的空间。矩阵如何利用此空间由实现通过 API 和语言隐藏。

attribute 变量必须具有全局作用域，并且必须在函数体外部、第一次使用之前声明。

### Uniform

`uniform` 限定符用于声明整个正在处理的图元中值相同的全局变量。所有 `uniform` 变量都是只读的，并且由应用程序通过 API 命令直接初始化，或由 OpenGL 间接初始化。

一个声明示例是：

``` glsl
uniform vec4 lightPosition;
```

`uniform` 限定符可以与任何基本数据类型一起使用，或在声明类型为结构体或这些类型的数组的变量时使用。

对于每种类型的着色器可以使用的 uniform 存储量有一个实现相关的限制，如果超过此限制，将导致编译时或链接时错误。声明但未使用的 uniform 变量不计入此限制。着色器中使用的用户定义 uniform 变量的数量和内置 uniform 变量的数量相加，以确定是否超过了可用的 uniform 存储。

如果将多个着色器链接在一起，则它们将共享一个单一的全局 uniform 名称空间。因此，具有相同名称的 uniform 类型必须在链接到单个可执行文件的所有着色器之间匹配。

### Varying

varying 变量提供顶点着色器、片段着色器以及它们之间的固定功能之间的接口。顶点着色器将计算每个顶点的值（如颜色、纹理坐标等），并将它们写入使用 `varying` 限定符声明的变量。顶点着色器也可以读取 `varying` 变量，获取它写入的相同值。如果在写入之前读取顶点着色器中的 `varying` 变量，将返回未定义的值。

根据定义，varying 变量是按顶点设置的，并以透视正确的方式在正在渲染的图元上进行插值。如果是单采样，插值值用于片段中心。如果是多采样，插值值可以位于像素内的任何位置，包括片段中心或片段样本之一。

片段着色器可以从 varying 变量中读取，读取的值将是插值值，作为片段在图元内位置的函数。片段着色器不能写入 varying 变量。

在顶点和片段着色器中声明的同名 varying 变量的类型必须匹配，否则链接命令将失败。只有在片段着色器中使用（即读取）的 varying 变量必须由顶点着色器写入；在顶点着色器中声明多余的 varying 变量是允许的。

varying 变量的声明如下例所示：

``` glsl
varying vec3 normal;
```

`varying` 限定符只能与数据类型 `float`、`vec2`、`vec3`、`vec4`、`mat2`、`mat3` 和 `mat4` 或它们的数组一起使用。结构体不能是 `varying`。

如果没有活动的顶点着色器，OpenGL 的固定功能管线将计算内置 varying 变量的值，这些值将被片段着色器使用。类似地，如果没有活动的片段着色器，顶点着色器负责计算和写入 OpenGL 的固定功能片段管线所需的 varying 变量。

varying 变量必须具有全局作用域，并且必须在函数体外部、第一次使用之前声明。