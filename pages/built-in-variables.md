# Built-In Variables

## Vertex Shader Special Variables

Some OpenGL operations still continue to occur in fixed functionality in between the vertex processor and the fragment processor. Other OpenGL operations continue to occur in fixed functionality after the fragment processor. Shaders communicate with the fixed functionality of OpenGL through the use of built-in variables.

The variable `gl_Position` is available only in the vertex language and is intended for writing the homogeneous vertex position. All executions of a well-formed vertex shader must write a value into this variable. It can be written at any time during shader execution. It may also be read back by the shader after being written. This value will be used by primitive assembly, clipping, culling, and other fixed functionality operations that operate on primitives after vertex processing has occurred. Compilers may generate a diagnostic message if they detect `gl_Position` is not written, or read before being written, but not all such cases are detectable. Results are undefined if a vertex shader is executed and does not write `gl_Position`.

The variable `gl_PointSize` is available only in the vertex language and is intended for a vertex shader to write the size of the point to be rasterized. It is measured in pixels.

The variable `gl_ClipVertex` is available only in the vertex language and provides a place for vertex shaders to write the coordinate to be used with the user clipping planes. The user must ensure the clip vertex and user clipping planes are defined in the same coordinate space. User clip planes work properly only under linear transform. It is undefined what happens under non-linear transform.

These built-in vertex shader variables for communicating with fixed functionality are intrinsically declared with the following types:

``` glsl
vec4  gl_Position;    // must be written to
float gl_PointSize;   // may be written to
vec4  gl_ClipVertex;  // may be written to
```

If `gl_PointSize` or `gl_ClipVertex` are not written to, their values are undefined. Any of these variables can be read back by the shader after writing to them, to retrieve what was written. Reading them before writing them results in undefined behavior. If they are written more than once, it is the last value written that is consumed by the subsequent operations.

These built-in variables have global scope.

## Fragment Shader Special Variables

The output of the fragment shader is processed by the fixed function operations at the back end of the OpenGL pipeline. Fragment shaders output values to the OpenGL pipeline using the built-in variables `gl_FragColor`, `gl_FragData`, and `gl_FragDepth`, unless the `discard` keyword is executed.

These variables may be written more than once within a fragment shader. If so, the last value assigned is the one used in the subsequent fixed function pipeline. The values written to these variables may be read back after writing them. Reading from these variables before writing them results in an undefined value.

The fixed functionality computed depth for a fragment may be obtained by reading `gl_FragCoord.z`, described below.

Writing to `gl_FragColor` specifies the fragment color that will be used by the subsequent fixed functionality pipeline. If subsequent fixed functionality consumes fragment color and an execution of a fragment shader does not write a value to `gl_FragColor` then the fragment color consumed is undefined.

If the frame buffer is configured as a color index buffer then behavior is undefined when using a fragment shader.

Writing to `gl_FragDepth` will establish the depth value for the fragment being processed. If depth buffering is enabled, and a shader does not write `gl_FragDepth`, then the fixed function value for depth will be used as the fragment’s depth value. If a shader statically assigns a value to `gl_FragDepth`, and there is an execution path through the shader that does not set `gl_FragDepth`, then the value of the fragment’s depth may be undefined for executions of the shader that take that path. That is, if a shader statically contains a write to `gl_FragDepth`, then it is responsible for always writing it.

(A shader contains a static `assignment` to a variable `x` if, after pre-processing, the shader contains a statement that would write to `x`, whether or not run-time flow of control will cause that statement to be executed.)

The variable `gl_FragData` is an array. Writing to `gl_FragData[n]` specifies the fragment data that will be used by the subsequent fixed functionality pipeline for data n. If subsequent fixed functionality consumes fragment data and an execution of a fragment shader does not write a value to it, then the fragment data consumed is undefined.

If a shader statically assigns a value to `gl_FragColor`, it may not assign a value to any element of `gl_FragData`. If a shader statically writes a value to any element of `gl_FragData`, it may not assign a value to `gl_FragColor`. That is, a shader may assign values to either `gl_FragColor` or `gl_FragData`, but not both.

If a shader executes the `discard` keyword, the fragment is discarded, and the values of `gl_FragDepth`, `gl_FragColor`, and `gl_FragData` become irrelevant.

The variable `gl_FragCoord` is available as a read-only variable from within fragment shaders and it holds the window relative coordinates x, y, z, and 1/w values for the fragment. This value is the result of the fixed functionality that interpolates primitives after vertex processing to generate fragments. The z component is the depth value that would be used for the fragment’s depth if a shader contained no writes to `gl_FragDepth`. This is useful for invariance if a shader conditionally computes `gl_FragDepth` but otherwise wants the fixed functionality fragment depth.

The fragment shader has access to the read-only built-in variable `gl_FrontFacing` whose value is true if the fragment belongs to a front-facing primitive. One use of this is to emulate two-sided lighting by selecting one of two colors calculated by the vertex shader.

The built-in variables that are accessible from a fragment shader are intrinsically given types as follows:

``` glsl
vec4  gl_FragCoord;
bool  gl_FrontFacing;
vec4  gl_FragColor;
vec4  gl_FragData[gl_MaxDrawBuffers];
float gl_FragDepth;
```

However, they do not behave like variables with no qualifier; their behavior is as described above. These built-in variables have global scope.

## Vertex Shader Built-In Attributes

The following attribute names are built into the OpenGL vertex language and can be used from within a vertex shader to access the current values of attributes declared by OpenGL. All page numbers and notations are references to the OpenGL 1.4 specification.

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

## Built-In Constants

The following built-in constants are provided to vertex and fragment shaders.

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

## Built-In Uniform State

As an aid to accessing OpenGL processing state, the following uniform variables are built into the OpenGL Shading Language. All page numbers and notations are references to the 1.4 specification.

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

## Varying Variables

Unlike user-defined varying variables, the built-in varying variables don’t have a strict one-to-one correspondence between the vertex language and the fragment language. Two sets are provided, one for each language. Their relationship is described below.

The following built-in varying variables are available to write to in a vertex shader. A particular one should be written to if any functionality in a corresponding fragment shader or fixed pipeline uses it or state derived from it. Otherwise, behavior is undefined.

``` glsl
varying vec4  gl_FrontColor;
varying vec4  gl_BackColor;
varying vec4  gl_FrontSecondaryColor;
varying vec4  gl_BackSecondaryColor;
varying vec4  gl_TexCoord[];// at most will be gl_MaxTextureCoords
varying float gl_FogFragCoord;
```

For `gl_FogFragCoord`, the value written will be used as the "c" value on page 160 of the OpenGL 1.4

Specification by the fixed functionality pipeline. For example, if the z-coordinate of the fragment in eye space is desired as "c", then that's what the vertex shader should write into `gl_FogFragCoord`.

As with all arrays, indices used to subscript `gl_TexCoord` must either be an integral constant expressions, or this array must be re-declared by the shader with a size. The size can be at most `gl_MaxTextureCoords`.

Using indexes close to 0 may aid the implementation in preserving varying resources.

The following varying variables are available to read from in a fragment shader. The `gl_Color` and `gl_SecondaryColor` names are the same names as attributes passed to the vertex shader. However, there is no name conflict, because attributes are visible only in vertex shaders and the following are only visible in a fragment shader.

``` glsl
varying vec4 gl_Color;
varying vec4 gl_SecondaryColor;
varying vec4 gl_TexCoord[];   // at most will be gl_MaxTextureCoords
varying float gl_FogFragCoord;
```

The values in `gl_Color` and `gl_SecondaryColor` will be derived automatically by the system from `gl_FrontColor`, `gl_BackColor`, `gl_FrontSecondaryColor`, and `gl_BackSecondaryColor` based on which face is visible. If fixed functionality is used for vertex processing, then `gl_FogFragCoord` will either be the z-coordinate of the fragment in eye space, or the interpolation of the fog coordinate, as described in section 3.10 of the OpenGL 1.4 Specification. The `gl_TexCoord[]` values are the interpolated `gl_TexCoord[]` values from a vertex shader or the texture coordinates of any fixed pipeline based vertex functionality.

Indices to the fragment shader `gl_TexCoord` array are as described above in the vertex shader text.
