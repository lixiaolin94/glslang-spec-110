# 内置函数

OpenGL 着色语言定义了一系列用于标量和向量运算的内置便利函数。其中许多内置函数可以在多种类型的着色器中使用，但有些函数旨在提供与硬件的直接映射，因此仅适用于特定类型的着色器。

内置函数基本上分为三类:

- 它们以方便的方式公开了一些必要的硬件功能，例如访问纹理贴图。语言中没有办法让着色器模拟这些函数。
- 它们表示一个微不足道的操作(clamp，mix 等)，对用户来说编写非常简单，但它们非常常见，可能有直接的硬件支持。将表达式映射到复杂的汇编指令对编译器来说是一个非常困难的问题。
- 它们代表了图形硬件可能在某个时候加速的操作。三角函数就属于这一类。

许多函数与常见 C 库中同名函数类似，但它们支持矢量输入以及更传统的标量输入。

应鼓励应用程序使用内置函数，而不是在自己的着色器代码中执行等效计算，因为内置函数被认为是最优的(例如，可能直接在硬件中得到支持)。

如果用户代码选择，可以通过简单地重新声明和定义相同的名称和参数列表来替换内置函数。

在下面指定内置函数时，如果输入参数(和相应的输出)可以是 `float`，`vec2`，`vec3` 或 `vec4`，则使用 `genType` 作为参数。对于函数的任何特定用法，实际类型必须对所有参数和返回类型相同。对于 `mat` 也是类似的，它可以是 `mat2`，`mat3` 或 `mat4`。

## 角度和三角函数

指定为 *angle* 的函数参数被假定为弧度单位。在任何情况下，这些函数都不会导致除以零错误。如果比率的除数为 0，则结果将是未定义的。

这些函数都是分量式操作。描述针对每个分量。

| 语法                                | 描述                                                                                                                              |
| ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| genType radians (genType degrees)   | 将角度转换为弧度，即 result = π/180 ⋅ degrees                                                                                      |
| genType degrees (genType radians)   | 将弧度转换为角度，即 result = 180/π ⋅ radians                                                                                      |
| genType sin (genType angle)         | 标准的正弦三角函数                                                                                                                |
| genType cos (genType angle)         | 标准的余弦三角函数                                                                                                                |
| genType tan (genType angle)         | 标准的正切三角函数                                                                                                                |
| genType asin (genType x)            | 反正弦。返回正弦值为 x 的角度。该函数返回值的范围是 [–π/2, π/2]。如果 \|x\| > 1，结果未定义                                        |
| genType acos (genType x)            | 反余弦。返回余弦值为 x 的角度。该函数返回值的范围是 [0, π]。如果 \|x\| > 1，结果未定义                                             |
| genType atan (genType y, genType x) | 反正切。返回正切值为 y/x 的角度。x 和 y 的符号用于确定角度所在的象限。该函数返回值的范围是 [–π, π]。如果 x 和 y 都为 0，结果未定义 |
| genType atan (genType y_over_x)     | 反正切。返回正切值为 y_over_x 的角度。该函数返回值的范围是 [–π/2, π/2]                                                            |

## 指数函数

这些函数都是分量式操作。描述针对每个分量。

| 语法                               | 描述                                                                                        |
| ---------------------------------- | ------------------------------------------------------------------------------------------- |
| genType pow (genType x, genType y) | 返回 x 的 y 次幂，即 x^y。<br><br>如果 x < 0，结果未定义。<br>如果 x = 0 且 y <= 0，结果未定义 |
| genType exp (genType x)            | 返回 x 的自然指数，即 e^x                                                                    |
| genType log (genType x)            | 返回 x 的自然对数，即返回满足方程 x = e^y 的 y 值。如果 x <= 0，结果未定义                    |
| genType exp2 (genType x)           | 返回 2 的 x 次幂，即 2^x                                                                     |
| genType log2 (genType x)           | 返回 x 的以 2 为底的对数，即返回满足方程 x = 2^y 的 y 值。如果 x <= 0，结果未定义             |
| genType sqrt (genType x)           | 返回 x 的正平方根。如果 x < 0，结果未定义                                                    |
| genType inversesqrt (genType x)    | 返回 x 的正平方根的倒数。<br><br>如果 x <= 0，结果未定义                                     |

## 常用函数

这些函数都是分量式操作。描述针对每个分量。

| 语法                                                                                                                     | 描述                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| genType abs (genType x)                                                                                                  | 如果 x >= 0 则返回 x，否则返回 –x                                                                                                                                                                                                                                       |
| genType sign (genType x)                                                                                                 | 如果 x > 0 返回 1.0，如果 x = 0 返回 0.0，如果 x < 0 返回 –1.0                                                                                                                                                                                                           |
| genType floor (genType x)                                                                                                | 返回小于或等于 x 的最近整数                                                                                                                                                                                                                                            |
| genType ceil (genType x)                                                                                                 | 返回大于或等于 x 的最近整数                                                                                                                                                                                                                                            |
| genType fract (genType x)                                                                                                | 返回 x – floor(x)                                                                                                                                                                                                                                                      |
| genType mod (genType x, genType y)<br>genType mod (genType x, float y)                                                   | 取模。返回 x – y \* floor(x/y)                                                                                                                                                                                                                                          |
| genType min (genType x, genType y)<br>genType min (genType x, float y)                                                   | 如果 y < x 返回 y，否则返回 x                                                                                                                                                                                                                                           |
| genType max (genType x, genType y)<br>genType max (genType x, float y)                                                   | 如果 x < y 返回 y，否则返回 x                                                                                                                                                                                                                                           |
| genType clamp (genType x, genType minVal, genType maxVal)<br>genType clamp (genType x, float minVal, float maxVal)       | 返回 min(max(x, minVal), maxVal)<br>注意，片段着色器写入的颜色和深度值在片段着色器运行后会被实现部分进行限制                                                                                                                                                            |
| genType mix (genType x, genType y, genType a)<br>genType mix (genType x, genType y, float a)                             | 返回 x \* (1 – a) + y \* a,即 x 和 y 的线性混合                                                                                                                                                                                                                          |
| genType step (genType edge, genType x)<br>genType step (float edge, genType x)                                           | 如果 x < edge 返回 0.0，否则返回 1.0                                                                                                                                                                                                                                    |
| genType smoothstep (genType edge0, genType edge1, genType x)<br>genType smoothstep (float edge0, float edge1, genType x) | 如果 x <= edge0 返回 0.0，如果 x >= edge1 返回 1.0，当 edge0 < x < edge1 时在 0 和 1 之间进行平滑的 Hermite 插值。这在需要具有平滑过渡的阈值函数的情况下很有用。等价于:<br>genType t;<br>t = clamp((x – edge0) / (edge1 – edge0), 0, 1);<br>return t \* t \* (3 – 2 \* t); |

## 几何函数

这些函数将向量作为向量进行操作，而不是分量式操作。

| 语法                                                     | 描述                                                                                                                                                                                                                                                                                                                                      |
| -------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| float length (genType x)                                 | 返回向量 x 的长度，即<br><br>sqrt(x[0] \* x[0] + x[1] \* x[1] + ...)                                                                                                                                                                                                                                                                         |
| float distance (genType p0, genType p1)                  | 返回 p0 和 p1 之间的距离，即<br><br>length(p0 – p1)                                                                                                                                                                                                                                                                                        |
| float dot (genType x, genType y)                         | 返回 x 和 y 的点积，即<br><br>result = x[0] \* y[0] + x[1] \* y[1] + ...                                                                                                                                                                                                                                                                     |
| vec3 cross (vec3 x, vec3 y)                              | 返回 x 和 y 的叉积，即<br><br>result.0 = x[1] \* y[2] - y[1] \* x[2] <br>result.1 = x[2] \* y[0] - y[2] \* x[0] <br>result.2 = x[0] \* y[1] - y[0] \* x[1]                                                                                                                                                                                       |
| genType normalize (genType x)                            | 返回与 x 方向相同但长度为 1 的向量                                                                                                                                                                                                                                                                                                        |
| vec4 ftransform()                                        | 仅对顶点着色器有效。该函数将确保输入顶点值以一种与 OpenGL 固定功能变换完全相同的方式进行变换。它用于计算 gl_Position,例如:<br><br>gl_Position = ftransform()<br><br>例如，当应用程序在单独的通道中渲染相同的几何体时，应使用此函数，一个通道使用固定功能路径进行渲染，另一个通道使用可编程着色器                                              |
| genType faceforward (genType N, genType I, genType Nref) | 如果 dot(Nref, I) < 0 返回 N，否则返回 –N                                                                                                                                                                                                                                                                                                  |
| genType reflect (genType I, genType N)                   | 对于入射向量 I 和表面法向量 N，返回反射方向:<br><br>result = I – 2 \* dot(N, I) \* N<br><br>为了获得所需的结果，N 必须已经被归一化                                                                                                                                                                                                            |
| genType refract(genType I, genType N, float eta)         | 对于入射向量 I 和表面法向量 N，以及折射率之比 eta，返回折射向量。返回结果通过以下方式计算:<br><br>k = 1.0 - eta \* eta \* (1.0 - dot(N, I) \* dot(N, I))<br>if (k < 0.0)<br> result = genType(0.0)<br>else<br> result = eta \* I - (eta \* dot(N, I) + sqrt(k)) \* N<br><br>为了获得所需的结果，输入参数入射向量 I 和表面法向量 N 必须已经被归一化 |

## 矩阵函数

| 语法                              | 描述                                                                                                                                     |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| mat matrixCompMult (mat x, mat y) | 矩阵 x 和矩阵 y 的分量式乘积，即 result\[i\]\[j\] 是标量 x\[i\]\[j\] 和 y\[i\]\[j\] 的乘积。<br><br>注意:要得到线性代数矩阵乘法，请使用乘法运算符(`*`) |

## 向量关系函数

关系和相等运算符(`<`、`<=`、`>`、`>=`、`==`、`!=`)定义(或保留)为产生标量布尔结果。对于向量结果，请使用以下内置函数。下面，"bvec"是 `bvec2`、`bvec3` 或 `bvec4` 之一的占位符，"ivec"是 `ivec2`、`ivec3` 或 `ivec4` 之一的占位符，"vec"是 `vec2`、`vec3` 或 `vec4` 的占位符。在所有情况下，任何特定调用的输入和返回向量的大小必须匹配。

| 语法                                                                                            | 描述                                     |
| ----------------------------------------------------------------------------------------------- | ---------------------------------------- |
| bvec lessThan(vec x, vec y) <br>bvec lessThan(ivec x, ivec y)                                   | 返回 x < y 的分量式比较结果              |
| bvec lessThanEqual(vec x, vec y) <br>bvec lessThanEqual(ivec x, ivec y)                         | 返回 x <= y 的分量式比较结果             |
| bvec greaterThan(vec x, vec y) <br>bvec greaterThan(ivec x, ivec y)                             | 返回 x > y 的分量式比较结果              |
| bvec greaterThanEqual(vec x, vec y) <br>bvec greaterThanEqual(ivec x, ivec y)                   | 返回 x >= y 的分量式比较结果             |
| bvec equal(vec x, vec y) <br>bvec equal(ivec x, ivec y) <br>bvec equal(bvec x, bvec y)          | 返回 x == y 的分量式比较结果             |
| bvec notEqual(vec x, vec y) <br>bvec notEqual(ivec x, ivec y) <br>bvec notEqual(bvec x, bvec y) | 返回 x != y 的分量式比较结果             |
| bool any(bvec x)                                                                                | 如果 x 的任一分量为 true 则返回 true     |
| bool all(bvec x)                                                                                | 仅当 x 的所有分量都为 true 时才返回 true |
| bvec not(bvec x)                                                                                | 返回 x 的分量式逻辑补                    |

## 纹理查找函数

纹理查找函数对顶点着色器和片段着色器都可用。但是，对于顶点着色器，固定功能不计算细节级别，因此顶点纹理查找和片段纹理查找在操作上有一些差异。下表中的函数通过 OpenGL API 设置的采样器提供对纹理的访问。纹理属性如大小、像素格式、维度数、过滤方法、mip-map 级别数、深度比较等也由 OpenGL API 调用定义。在通过下面定义的内置函数访问纹理时，会考虑这些属性。

如果对启用深度比较的深度纹理采样器进行非阴影纹理调用，则结果未定义。如果对表示关闭深度比较的深度纹理采样器进行阴影纹理调用，则结果未定义。如果对不表示深度纹理的采样器进行阴影纹理调用，则结果未定义。

在下面的所有函数中，bias 参数对于片段着色器是可选的。顶点着色器不接受 bias 参数。对于片段着色器，如果存在 bias、则在执行纹理访问操作之前，它将被添加到计算得到的细节级别(LOD)中。如果未提供 bias 参数，则实现会自动选择细节级别:对于未使用 mip-map 的纹理，直接使用该纹理。如果在片段着色器中运行 mip-map、则使用实现计算的 LOD 进行纹理查找。如果在顶点着色器上运行 mip-map、则使用基本纹理。

以"**Lod**"为后缀的内置函数只允许在顶点着色器中使用。对于"**Lod**"函数，lod 直接用作细节级别。

| 语法                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 描述                                                                                                                                                                                                                                                                                                                         |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| vec4 texture1D (sampler1D sampler, float coord [, float bias] )<br><br>vec4 texture1DProj (sampler1D sampler, vec2 coord [, float bias] )<br><br>vec4 texture1DProj (sampler1D sampler, vec4 coord [, float bias] )<br><br>vec4 texture1DLod (sampler1D sampler, float coord, float lod)<br><br>vec4 texture1DProjLod (sampler1D sampler, vec2 coord, float lod)<br><br>vec4 texture1DProjLod (sampler1D sampler, vec4 coord, float lod)                                                                                                                                                                 | 使用纹理坐标 coord 在当前绑定到 sampler 的 1D 纹理上进行纹理查找。对于投影("Proj")版本，纹理坐标 coord.s 除以 coord 的最后一个分量。                                                                                                                                                                                          |
| vec4 texture2D (sampler2D sampler, vec2 coord [, float bias] )<br><br>vec4 texture2DProj (sampler2D sampler, vec3 coord [, float bias] )<br><br>vec4 texture2DProj (sampler2D sampler, vec4 coord [, float bias] )<br><br>vec4 texture2DLod (sampler2D sampler, vec2 coord, float lod)<br><br>vec4 texture2DProjLod (sampler2D sampler, vec3 coord, float lod)<br><br>vec4 texture2DProjLod (sampler2D sampler, vec4 coord, float lod)                                                                                                                                                                   | 使用纹理坐标 coord 在当前绑定到 sampler 的 2D 纹理上进行纹理查找。对于投影("Proj")版本，纹理坐标 (coord.s, coord.t) 除以 coord 的最后一个分量。vec4 coord 变体忽略 coord 的第三个分量。                                                                                                                                       |
| vec4 texture3D (sampler3D sampler, vec3 coord [, float bias] )<br><br>vec4 texture3DProj (sampler3D sampler, vec4 coord [, float bias] )<br><br>vec4 texture3DLod (sampler3D sampler, vec3 coord, float lod)<br><br>vec4 texture3DProjLod (sampler3D sampler, vec4 coord, float lod)                                                                                                                                                                                                                                                                                                                     | 使用纹理坐标 coord 在当前绑定到 sampler 的 3D 纹理上进行纹理查找。对于投影("Proj")版本，纹理坐标除以 coord.q。                                                                                                                                                                                                                |
| vec4 textureCube (samplerCube sampler, vec3 coord [, float bias] )<br><br>vec4 textureCubeLod (samplerCube sampler, vec3 coord, float lod)                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 使用纹理坐标 coord 在当前绑定到 sampler 的立方体贴图纹理上进行纹理查找。coord 的方向用于选择在哪个面进行 2 维纹理查找，如 OpenGL 规范 1.4 版本第 3.8.6 节所述。                                                                                                                                                               |
| vec4 shadow1D (sampler1DShadow sampler, vec3 coord [, float bias] )<br><br>vec4 shadow2D (sampler2DShadow sampler, vec3 coord [, float bias] ) vec4 shadow1DProj (sampler1DShadow sampler,<br><br>vec4 coord [, float bias] ) vec4 shadow2DProj (sampler2DShadow sampler,<br><br>vec4 coord [, float bias] ) vec4 shadow1DLod (sampler1DShadow sampler,<br><br>vec3 coord, float lod) vec4 shadow2DLod (sampler2DShadow sampler,<br><br>vec3 coord, float lod) vec4 shadow1DProjLod(sampler1DShadow sampler, vec4 coord, float lod) vec4 shadow2DProjLod(sampler2DShadow sampler, vec4 coord, float lod) | 使用纹理坐标 coord 在绑定到 sampler 的深度纹理上进行深度比较查找，如 OpenGL 规范 1.4 版本第 3.8.14 节所述。coord 的第 3 个分量(coord.p)用作 R 值。绑定到采样器的纹理必须是深度纹理，否则结果未定义。对于每个内置函数的投影("Proj")版本，纹理坐标除以 coord.q,给出深度值 R 为 coord.p/coord.q。"1D"变体忽略 coord 的第二个分量。 |

## 片段处理函数

片段处理函数仅在用于片段处理器的着色器中可用。

导数的计算可能代价高昂和/或数值不稳定。因此，OpenGL 实现可以通过使用快速但不完全精确的导数计算来近似真实导数。

使用前向/后向差分指定导数的预期行为。

前向差分:

``` glsl
F(x+dx) - F(x) ~ dFdx(x) * dx     1a
dFdx(x) ~ (F(x+dx) - F(x)) / dx   1b
```

后向差分:

``` glsl
F(x-dx) - F(x) ~ -dFdx(x) * dx    2a
dFdx(x) ~ (F(x) - F(x-dx)) / dx   2b
```

对于单样本光栅化，在等式 1b 和 2b 中 dx <= 1.0。对于多样本光栅化，在等式 1b 和 2b 中 dx < 2.0。

`dFdy` 的近似方法类似，用 y 替换 x。

GL 实现可以使用上述或其他方法来执行计算，但须符合以下条件:

1. 该方法可以使用分段线性近似。这种线性近似意味着更高阶导数 `dFdx(dFdx(x))` 及以上是未定义的。

2. 该方法可以假设被求值的函数是连续的。因此，非统一条件主体内的导数是未定义的。

3. 该方法可以因片段而异，但须遵守该方法可能因窗口坐标而异，而不是屏幕坐标的约束。OpenGL 1.4 规范第 3.1 节中描述的不变性要求对于导数计算是放松的，因为该方法可能是片段位置的函数。

其他理想但不是必需的属性是:

4. 应在图元的内部(插值，而不是外推)对函数进行求值。

5. 对 `dFdx` 的函数在保持 y 不变时求值。对 `dFdy` 的函数在保持 x 不变时求值。但是，混合的高阶导数，如 `dFdx(dFdy(y))` 和 `dFdy(dFdx(x))` 是未定义的。

在某些实现中，可以通过提供 GL 提示(OpenGL 1.4 规范第 5.6 节)来获得不同程度的导数精度，允许用户在图像质量和速度之间进行权衡。

| 语法                       | 描述                                                                                                                                                                                                                                                                            |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| genType dFdx (genType p)   | 使用局部差分返回输入参数 p 在 x 方向上的导数。                                                                                                                                                                                                                                  |
| genType dFdy (genType p)   | 使用局部差分返回输入参数 p 在 y 方向上的导数。<br>这两个函数通常用于估计用于抗锯齿程序化纹理的滤波器宽度。我们假设表达式在 SIMD 数组上并行求值，因此在任何给定时间点，函数值在 SIMD 数组所表示的网格点上是已知的。因此，可以使用 SIMD 数组元素之间的局部差分来推导 dFdx、dFdy 等。 |
| genType fwidth (genType p) | 使用局部差分返回输入参数 p 在 x 和 y 方向上的绝对导数之和，即<br><br>return = abs (dFdx (p)) + abs (dFdy (p));                                                                                                                                                                   |

## 噪声函数

噪声函数可用于片段和顶点着色器。它们是可用于增加视觉复杂性的随机函数。下列噪声函数返回的值呈现随机性，但并非真正随机。下面定义的噪声函数具有以下特点:

- 返回值总是在 [-1.0,1.0] 范围内，并且至少覆盖 [-0.6, 0.6] 范围，呈高斯分布。
- 返回值的总体平均值为 0.0
- 它们是可重复的，即特定的输入值总是产生相同的返回值
- 它们在旋转下具有统计不变性(即，无论域如何旋转，它都具有相同的统计特性)
- 它们在平移下具有统计不变性(即，无论域如何平移，它都具有相同的统计特性)
- 它们在平移下通常会给出不同的结果。
- 空间频率集中在 0.5 到 1.0 之间的某个位置。
- 它们在任何地方都是 C<sup>1</sup> 连续的(即一阶导数是连续的)

| 语法                     | 描述                              |
| ------------------------ | --------------------------------- |
| float noise1 (genType x) | 根据输入值 x 返回一个 1D 噪声值。 |
| vec2 noise2 (genType x)  | 根据输入值 x 返回一个 2D 噪声值。 |
| vec3 noise3 (genType x)  | 根据输入值 x 返回一个 3D 噪声值。 |
| vec4 noise4 (genType x)  | 根据输入值 x 返回一个 4D 噪声值。 |
