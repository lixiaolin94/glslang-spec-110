# 内置变量

## 顶点着色器特殊变量

在顶点处理器和片段处理器之间，一些 OpenGL 操作仍然继续在固定功能中发生。片段处理器之后，其他 OpenGL 操作继续在固定功能中发生。着色器通过使用内置变量与 OpenGL 的固定功能进行通信。

变量 `gl_Position` 仅在顶点语言中可用，用于写入齐次顶点位置。所有格式正确的顶点着色器的执行都必须将值写入此变量。可以在着色器执行期间的任何时候写入它。写入后也可以由着色器读回。在顶点处理发生后，此值将被图元装配、裁剪、剔除和其他对图元进行操作的固定功能操作使用。如果编译器检测到未写入 `gl_Position`，或在写入之前读取，则可能会生成诊断消息，但并非所有这样的情况都可以检测到。如果执行顶点着色器但不写入 `gl_Position`，则结果是未定义的。

变量 `gl_PointSize` 仅在顶点语言中可用，用于顶点着色器写入要光栅化的点的大小。它以像素为单位测量。

变量 `gl_ClipVertex` 仅在顶点语言中可用，为顶点着色器提供了一个位置，用于写入要与用户裁剪平面一起使用的坐标。用户必须确保裁剪顶点和用户裁剪平面在同一坐标空间中定义。用户裁剪平面仅在线性变换下正常工作。在非线性变换下发生的情况是未定义的。

用于与固定功能通信的这些内置顶点着色器变量本质上具有以下类型声明：

``` glsl
vec4  gl_Position;    // 必须写入
float gl_PointSize;   // 可以写入
vec4  gl_ClipVertex;  // 可以写入
```

如果未写入 `gl_PointSize` 或 `gl_ClipVertex`，则它们的值是未定义的。写入这些变量后，着色器可以读回它们中的任何一个，以检索写入的内容。在写入之前读取它们会导致未定义的行为。如果多次写入它们，则后续操作使用的是最后一次写入的值。

这些内置变量具有全局作用域。

## 片段着色器特殊变量

片段着色器的输出由 OpenGL 管线后端的固定功能操作处理。除非执行 `discard` 关键字，否则片段着色器使用内置变量 `gl_FragColor`、`gl_FragData` 和 `gl_FragDepth` 将值输出到 OpenGL 管线。

这些变量可以在片段着色器中多次写入。如果是这样，则使用分配的最后一个值在后续的固定功能管线中使用。写入这些变量的值可以在写入后读回。在写入之前读取这些变量会导致未定义的值。

可以通过读取下面描述的 `gl_FragCoord.z` 获得固定功能为片段计算的深度。

写入 `gl_FragColor` 指定将在后续固定功能管线中使用的片段颜色。如果后续固定功能使用片段颜色，并且片段着色器的执行没有向 `gl_FragColor` 写入值，则使用的片段颜色是未定义的。

如果帧缓冲区配置为颜色索引缓冲区，则使用片段着色器时行为是未定义的。

写入 `gl_FragDepth` 将为正在处理的片段建立深度值。如果启用了深度缓冲，并且着色器未写入 `gl_FragDepth`，则固定功能深度值将用作片段的深度值。如果着色器静态地为 `gl_FragDepth` 分配值，并且存在未设置 `gl_FragDepth` 的着色器执行路径，则对于采用该路径的着色器执行，片段的深度值可能是未定义的。也就是说，如果着色器静态包含对 `gl_FragDepth` 的写入，则它负责始终写入它。

（如果在预处理之后，着色器包含会写入变量 `x` 的语句，则着色器包含对变量 `x` 的静态 `赋值`，无论运行时控制流是否会导致执行该语句。）

变量 `gl_FragData` 是一个数组。写入 `gl_FragData[n]` 指定后续固定功能管线将用于数据 n 的片段数据。如果后续固定功能使用片段数据，并且片段着色器的执行没有向其写入值，则使用的片段数据是未定义的。

如果着色器静态地为 `gl_FragColor` 分配值，则它可能不会为 `gl_FragData` 的任何元素分配值。如果着色器静态地为 `gl_FragData` 的任何元素写入值，则它可能不会为 `gl_FragColor` 分配值。也就是说，着色器可以为 `gl_FragColor` 或 `gl_FragData` 分配值，但不能同时分配。

如果着色器执行 `discard` 关键字，则片段被丢弃，`gl_FragDepth`、`gl_FragColor` 和 `gl_FragData` 的值变得无关紧要。

变量 `gl_FragCoord` 可以作为片段着色器中的只读变量使用，它保存片段的窗口相对坐标 x、y、z 和 1/w 值。此值是在顶点处理后插值图元以生成片段的固定功能的结果。如果着色器不包含对 `gl_FragDepth` 的写入，则 z 分量是将用于片段深度的深度值。如果着色器有条件地计算 `gl_FragDepth`，但在其他情况下希望使用固定功能片段深度，则这对于不变性很有用。

片段着色器可以访问只读内置变量 `gl_FrontFacing`，如果片段属于面向前面的图元，则其值为 true。其中一个用途是通过选择顶点着色器计算的两种颜色之一来模拟双面照明。

可从片段着色器访问的内置变量本质上具有以下类型：

``` glsl
vec4  gl_FragCoord;
bool  gl_FrontFacing;
vec4  gl_FragColor;
vec4  gl_FragData[gl_MaxDrawBuffers];
float gl_FragDepth;
```

但是，它们的行为不像没有限定符的变量；它们的行为如上所述。这些内置变量具有全局作用域。

7.3 顶点着色器内置属性
以下属性名称内置于 OpenGL 顶点语言中，可以在顶点着色器中使用，以访问由 OpenGL 声明的属性的当前值。所有页码和符号都是对 OpenGL 1.4 规范的引用。

``` glsl
//
// Vertex Attributes, p. 19.
//
attribute vec4  gl_Color;
attribute vec4  gl_SecondaryColor;
attribute vec3  gl_Normal;
attribute vec4  gl_Vertex;
attribute vec4  gl_MultiTexCoord0;
attribute vec4  gl_MultiTexCoord1;
attribute vec4  gl_MultiTexCoord2;
attribute vec4  gl_MultiTexCoord3;
attribute vec4  gl_MultiTexCoord4;
attribute vec4  gl_MultiTexCoord5;
attribute vec4  gl_MultiTexCoord6;
attribute vec4  gl_MultiTexCoord7;
attribute float gl_FogCoord;
```

## 内置常量

以下内置常量可提供给顶点着色器和片段着色器。

``` glsl
//
// Implementation dependent constants. The example values below
// are the minimum values allowed for these maximums.
//
const int gl_MaxLights = 8;                       // GL 1.0
const int gl_MaxClipPlanes = 6;                   // GL 1.0
const int gl_MaxTextureUnits = 2;                 // GL 1.3
const int gl_MaxTextureCoords = 2;                // ARB_fragment_program
const int gl_MaxVertexAttribs = 16;               // ARB_vertex_shader
const int gl_MaxVertexUniformComponents = 512;    // ARB_vertex_shader
const int gl_MaxVaryingFloats = 32;               // ARB_vertex_shader
const int gl_MaxVertexTextureImageUnits = 0;      // ARB_vertex_shader
const int gl_MaxCombinedTextureImageUnits = 2;    // ARB_vertex_shader
const int gl_MaxTextureImageUnits = 2;            // ARB_fragment_shader
const int gl_MaxFragmentUniformComponents = 64;   // ARB_fragment_shader
const int gl_MaxDrawBuffers = 1;                  // proposed ARB_draw_buffers
```

## 内置 Uniform 状态

为了便于访问 OpenGL 处理状态，以下 uniform 变量内置于 OpenGL 着色语言中。所有页码和注释都引用自 1.4 规范。

``` glsl
//
// Matrix state. p. 31, 32, 37, 39, 40.
//
uniform mat4 gl_ModelViewMatrix;
uniform mat4 gl_ProjectionMatrix;
uniform mat4 gl_ModelViewProjectionMatrix;
uniform mat4 gl_TextureMatrix[gl_MaxTextureCoords];

//
// Derived matrix state that provides inverse and transposed versions
// of the matrices above. Poorly conditioned matrices may result
// in unpredictable values in their inverse forms.
//
uniform mat3 gl_NormalMatrix; // transpose of the inverse of the
                              // upper leftmost 3x3 of gl_ModelViewMatrix

uniform mat4 gl_ModelViewMatrixInverse;
uniform mat4 gl_ProjectionMatrixInverse;
uniform mat4 gl_ModelViewProjectionMatrixInverse;
uniform mat4 gl_TextureMatrixInverse[gl_MaxTextureCoords];

uniform mat4 gl_ModelViewMatrixTranspose;
uniform mat4 gl_ProjectionMatrixTranspose;
uniform mat4 gl_ModelViewProjectionMatrixTranspose;
uniform mat4 gl_TextureMatrixTranspose[gl_MaxTextureCoords];

uniform mat4 gl_ModelViewMatrixInverseTranspose;
uniform mat4 gl_ProjectionMatrixInverseTranspose;
uniform mat4 gl_ModelViewProjectionMatrixInverseTranspose;
uniform mat4 gl_TextureMatrixInverseTranspose[gl_MaxTextureCoords];

//
// Normal scaling p. 39.
//
uniform float gl_NormalScale;

//
// Depth range in window coordinates, p. 33
//
struct gl_DepthRangeParameters {
  float near;// n
  float far;// f
  float diff;// f - n
};
uniform gl_DepthRangeParameters gl_DepthRange;

//
// Clip planes p. 42.
//
uniform vec4 gl_ClipPlane[gl_MaxClipPlanes];

//
// Point Size, p. 66, 67.
//
struct gl_PointParameters {
  float size;
  float sizeMin;
  float sizeMax;
  float fadeThresholdSize;
  float distanceConstantAttenuation;
  float distanceLinearAttenuation;
  float distanceQuadraticAttenuation;
};

uniform gl_PointParameters gl_Point;

//
// Material State p. 50, 55.
//
struct gl_MaterialParameters {
  vec4 emission;    // Ecm
  vec4 ambient;     // Acm
  vec4 diffuse;     // Dcm
  vec4 specular;    // Scm
  float shininess;  // Srm
};
uniform gl_MaterialParameters gl_FrontMaterial;
uniform gl_MaterialParameters gl_BackMaterial;

//
// Light State p 50, 53, 55.
//
struct gl_LightSourceParameters {
  vec4 ambient;                 // Acli
  vec4 diffuse;                 // Dcli
  vec4 specular;                // Scli
  vec4 position;                // Ppli
  vec4 halfVector;              // Derived: Hi
  vec3 spotDirection;           // Sdli
  float spotExponent;           // Srli
  float spotCutoff;             // Crli
                                // (range: [0.0,90.0], 180.0)
  float spotCosCutoff;          // Derived: cos(Crli)
                                // (range: [1.0,0.0],-1.0)
  float constantAttenuation;    // K0
  float linearAttenuation;      // K1
  float quadraticAttenuation;   // K2
};

uniform gl_LightSourceParameters gl_LightSource[gl_MaxLights];

struct gl_LightModelParameters {
  vec4 ambient;     // Acs
};

uniform gl_LightModelParameters gl_LightModel;

//
// Derived state from products of light and material.
//
struct gl_LightModelProducts {
  vec4 sceneColor;  // Derived. Ecm + Acm * Acs
};

uniform gl_LightModelProducts gl_FrontLightModelProduct;
uniform gl_LightModelProducts gl_BackLightModelProduct;

struct gl_LightProducts {
  vec4 ambient;     // Acm * Acli
  vec4 diffuse;     // Dcm * Dcli
  vec4 specular;    // Scm * Scli
};

uniform gl_LightProducts gl_FrontLightProduct[gl_MaxLights];
uniform gl_LightProducts gl_BackLightProduct[gl_MaxLights];

//
// Texture Environment and Generation, p. 152, p. 40-42.
//
uniform vec4 gl_TextureEnvColor[gl_MaxTextureImageUnits];
uniform vec4 gl_EyePlaneS[gl_MaxTextureCoords];
uniform vec4 gl_EyePlaneT[gl_MaxTextureCoords];
uniform vec4 gl_EyePlaneR[gl_MaxTextureCoords];
uniform vec4 gl_EyePlaneQ[gl_MaxTextureCoords];
uniform vec4 gl_ObjectPlaneS[gl_MaxTextureCoords];
uniform vec4 gl_ObjectPlaneT[gl_MaxTextureCoords];
uniform vec4 gl_ObjectPlaneR[gl_MaxTextureCoords];
uniform vec4 gl_ObjectPlaneQ[gl_MaxTextureCoords];

//
// Fog p. 161
//
struct gl_FogParameters {
  vec4 color;
  float density;
  float start;
  float end;
  float scale;// Derived: 1.0 / (end - start)
};

uniform gl_FogParameters gl_Fog;
```

## 可变量(Varying Variables)

与用户定义的可变量不同，内置的可变量在顶点语言和片段语言之间没有严格的一一对应关系。为每种语言提供了两组变量。它们之间的关系如下所述。

以下内置的可变量可在顶点着色器中写入。如果片段着色器或固定管线中的任何功能使用了特定可变量或由其衍生的状态，就应该对其进行写入。否则，行为是未定义的。

``` glsl
varying vec4  gl_FrontColor;
varying vec4  gl_BackColor;
varying vec4  gl_FrontSecondaryColor;
varying vec4  gl_BackSecondaryColor;
varying vec4  gl_TexCoord[]; // 最多为 gl_MaxTextureCoords
varying float gl_FogFragCoord;
```

对于 `gl_FogFragCoord`，写入的值将作为 OpenGL 1.4 规范 160 页中固定功能管线的 "c" 值。例如，如果片段在视觉空间中的 z 坐标被期望为 "c"，那么顶点着色器应将其写入 `gl_FogFragCoord`。

与所有数组一样，用于对 `gl_TexCoord` 进行索引的下标必须是整型常量表达式，或者着色器必须使用一个大小重新声明此数组。大小最多可以是 `gl_MaxTextureCoords`。

使用接近 0 的索引可能有助于实现保留可变资源。

以下可变量可在片段着色器中读取。`gl_Color` 和 `gl_SecondaryColor` 名称与传递给顶点着色器的属性名称相同。但是，不会有名称冲突，因为属性仅在顶点着色器中可见，而以下内容仅在片段着色器中可见。

``` glsl
varying vec4 gl_Color;
varying vec4 gl_SecondaryColor;
varying vec4 gl_TexCoord[]; // 最多为 gl_MaxTextureCoords
varying float gl_FogFragCoord;
```

`gl_Color` 和 `gl_SecondaryColor` 中的值将根据可见面，由系统从 `gl_FrontColor`，`gl_BackColor`，`gl_FrontSecondaryColor` 和 `gl_BackSecondaryColor` 自动派生。如果使用固定功能进行顶点处理，则 `gl_FogFragCoord` 将是视觉空间中片段的 z 坐标，或雾坐标的插值，如 OpenGL 1.4 规范第 3.10 节所述。`gl_TexCoord[]` 值是来自顶点着色器的插值后的 `gl_TexCoord[]` 值，或任何基于固定管线的顶点功能的纹理坐标。

片段着色器 `gl_TexCoord` 数组的索引如上文顶点着色器文本中所述。
