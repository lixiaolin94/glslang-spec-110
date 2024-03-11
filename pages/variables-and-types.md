# Variables and Types

All variables and functions must be declared before being used. Variable and function names are identifiers.

There are no default types. All variable and function declarations must have a declared type, and optionally qualifiers. A variable is declared by specifying its type followed by one or more names separated by commas. In many cases, a variable can be initialized as part of its declaration by using the assignment operator (`=`). The grammar near the end of this document provides a full reference for the syntax of declaring variables.

User-defined types may be defined using **struct** to aggregate a list of existing types into a single name.

The OpenGL Shading Language is type safe. There are no implicit conversions between types.

## Basic Types

The OpenGL Shading Language supports the following basic data types.

| Type            | Description                                               |
| --------------- | --------------------------------------------------------- |
| void            | for functions that do not return a value                  |
| bool            | a conditional type, taking on values of `true` or `false` |
| int             | a signed integer                                          |
| float           | a single floating-point scalar                            |
| vec2            | a two component floating-point vector                     |
| vec3            | a three component floating-point vector                   |
| vec4            | a four component floating-point vector                    |
| bvec2           | a two component Boolean vector                            |
| bvec3           | a three component Boolean vector                          |
| bvec4           | a four component Boolean vector                           |
| ivec2           | a two component integer vector                            |
| ivec3           | a three component integer vector                          |
| ivec4           | a four component integer vector                           |
| mat2            | a 2×2 floating-point matrix                               |
| mat3            | a 3×3 floating-point matrix                               |
| mat4            | a 4×4 floating-point matrix                               |
| sampler1D       | a handle for accessing a 1D texture                       |
| sampler2D       | a handle for accessing a 2D texture                       |
| sampler3D       | a handle for accessing a 3D texture                       |
| samplerCube     | a handle for accessing a cube mapped texture              |
| sampler1DShadow | a handle for accessing a 1D depth texture with comparison |
| sampler2DShadow | a handle for accessing a 2D depth texture with comparison |

In addition, a shader can aggregate these using arrays and structures to build more complex types.

There are no pointer types.

### Void

Functions that do not return a value must be declared as `void`. There is no default function return type.

### Booleans

To make conditional execution of code easier to express, the type `bool` is supported. There is no expectation that hardware directly supports variables of this type. It is a genuine Boolean type, holding only one of two values meaning either true or false. Two keywords `true` and `false` can be used as Boolean constants. Booleans are declared and optionally initialized as in the follow example:

``` glsl
bool success;       // declare "success" to be a Boolean
bool done = false;  // declare and initialize "done"
```

The right side of the assignment operator (=) can be any expression whose type is `bool`.

Expressions used for conditional jumps (`if`, `for`, `?:`, `while`, `do-while`) must evaluate to the type `bool`.

### Integers

Integers are mainly supported as a programming aid. At the hardware level, real integers would aid efficient implementation of loops and array indices, and referencing texture units. However, there is no requirement that integers in the language map to an integer type in hardware. It is not expected that underlying hardware has full support for a wide range of integer operations. Because of their intended (limited) purpose, integers are limited to 16 bits of precision, plus a sign representation in both the vertex and fragment languages. An OpenGL Shading Language implementation may convert integers to floats to operate on them. An implementation is allowed to use more than 16 bits of precision to manipulate integers. Hence, there is no portable wrapping behavior. Shaders that overflow the 16 bits of precision may not be portable.

Integers are declared and optionally initialized with integer expressions as in the following example:

``` glsl
int i, j = 42;
```

Literal integer constants can be expressed in decimal (base 10), octal (base 8), or hexadecimal (base 16) as follows.

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

No white space is allowed between the digits of an integer constant, including after the leading `0` or after the leading `0x` or `0X` of a constant. A leading unary minus sign (`-`) is interpreted as an arithmetic unary negation, not as part of the constant. There are no letter suffixes.

### Floats

Floats are available for use in a variety of scalar calculations. Floating-point variables are defined as in the following example:

``` glsl
float a, b = 1.5;
```

As an input value to one of the processing units, a floating-point variable is expected to match the IEEE

single precision floating-point definition for precision and dynamic range. It is not required that the precision of internal processing match the IEEE floating-point specification for floating-point operations, but the guidelines for precision established by the OpenGL 1.4 specification must be met. Similarly, treatment of conditions such as divide by 0 may lead to an unspecified result, but in no case should such a condition lead to the interruption or termination of processing.

Floating-point constants are defined as follows.

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

A decimal point (.) is not needed if the exponent part is present.

### Vectors

The OpenGL Shading Language includes data types for generic 2-, 3-, and 4-component vectors of floating-point values, integers, or Booleans. Floating-point vector variables can be used to store a variety of things that are very useful in computer graphics: colors, normals, positions, texture coordinates, texture lookup results and the like. Boolean vectors can be used for component-wise comparisons of numeric vectors. Defining vectors as part of the shading language allows for direct mapping of vector operations on graphics hardware that is capable of doing vector processing. In general, applications will be able to take better advantage of the parallelism in graphics hardware by doing computations on vectors rather than on scalar values. Some examples of vector declaration are:

``` glsl
vec2 texcoord1, texcoord2;
vec3 position;
vec4 myRGBA;
ivec2 textureLookup;
bvec3 lessThan;
```

Initialization of vectors can be done with constructors, which are discussed shortly.

### Matrices

Matrices are another useful data type in computer graphics, and the OpenGL Shading Language defines support for 2×2, 3×3, and 4×4 matrices of floating point numbers. Matrices are read from and written to in column major order. Example matrix declarations:

``` glsl
mat2 mat2D;
mat3 optMatrix;
mat4 view, projection;
```

Initialization of matrix values is done with constructors (described in Section 5.4 "Constructors" ).

### Samplers

Sampler types (e.g. `sampler2D`) are effectively opaque handles to textures. They are used with the built-in texture functions (described in Section 8.7 "Texture Lookup Functions") to specify which texture to access. They can only be declared as function parameters or uniforms (see Section 4.3.5 "Uniform" ).

Samplers are not allowed to be operands in expressions nor can they be assigned into. As uniforms, they are initialized with the OpenGL API. As function parameters, only samplers may be passed to samplers of matching type. This enables consistency checking between shader texture accesses and OpenGL texture state before a shader is run.

### Structures

User-defined types can be created by aggregating other already defined types into a structure using the `struct` keyword. For example,

``` glsl
struct light {
  float intensity;
  vec3 position;
} lightVar;
```

In this example, light becomes the name of the new type, and lightVar becomes a variable of type light.

To declare variables of the new type, use its name (without the keyword `struct`).

``` glsl
light lightVar2;
```

More formally, structures are declared as follows. However, the complete correct grammar is as given in

Section 9 "Shading Language Grammar".

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

where name becomes the user-defined type, and can be used to declare variables to be of this new type.

The name shares the same name space as other variables and types, with the same scoping rules. The optional qualifier only applies to any declarators, and is not part of the type being defined for name.

Structures must have at least one member declaration. Member declarators do not contain any qualifiers.

Nor do they contain any bit fields. Member types must be either already defined (there are no forward references), or defined in-place by embedding another struct definition. Member declarations cannot contain initializers. Member declarators can contain arrays. Such arrays must have a size specified, and the size must be an integral constant expression that's greater than zero (see Section 4.3.3 "Integral

Constant Expressions"). Each level of structure has its own namespace for names given in member declarators; such names need only be unique within that namespace.

Anonymous structures are not supported; so embedded structures must have a declarator. A name given to an embedded struct is scoped at the same level as the struct it is embedded in.

Structures can be initialized at declaration time using constructors, as discussed in Section 5.4.3"Structure Constructors".

### Arrays

Variables of the same type can be aggregated into arrays by declaring a name followed by brackets ( [ ] ) enclosing an optional size. When an array size is specified in a declaration, it must be an integral constant expression (see Section 4.3.3 "Integral Constant Expressions") greater than zero. If an array is indexed with an expression that is not an integral constant expression or passed as an argument to a function, then its size must be declared before any such use. It is legal to declare an array without a size and then later re-declare the same name as an array of the same type and specify a size. It is illegal to declare an array with a size, and then later (in the same shader) index the same array with an integral constant expression greater than or equal to the declared size. It is also illegal to index an array with a negative constant expression. Arrays declared as formal parameters in a function declaration must specify a size.

Undefined behavior results from indexing an array with a non-constant expression that’s greater than or equal to the array’s size or less than 0. Only one-dimensional arrays may be declared. All basic types and structures can be formed into arrays. Some examples are:

``` glsl
float frequencies[3];
uniform vec4 lightPosition[4];
light lights[];
const int numLights = 2;
light lights[numLights];
```

There is no mechanism for initializing arrays at declaration time from within a shader.

## Scoping

The scope of a variable is determined by where it is declared. If it is declared outside all function definitions, it has global scope, which starts from where it is declared and persists to the end of the shader it is declared in. If it is declared in a `while` test or a `for` statement, then it is scoped to the end of the following sub-statement. Otherwise, if it is declared as a statement within a compound statement, it is scoped to the end of that compound statement. If it is declared as a parameter in a function definition, it is scoped until the end of that function definition. A function body has a scope nested inside the function’s definition. The `if` statement’s expression does not allow new variables to be declared, hence does not form a new scope.

A variable declared as an empty array can be re-declared as an array of the same base type. Otherwise, within one compilation unit, a variable with the same name cannot be re-declared in the same scope.

However, a nested scope can override an outer scope’s declaration of a particular variable name.

Declarations in a nested scope provide separate storage from the storage associated with an overridden name.

All variables in the same scope share the same name space. Functions names are always identifiable as function names based on context, and they have their own name space.

Shared globals are global variables declared with the same name in independently compiled units (shaders) of the same language (vertex or fragment) that are linked together to make a single program.

Shared globals share the same namespace, and must be declared with the same type. They will share the same storage. Shared global arrays must have the same base type and the same size. Scalars must have exactly the same type name and type definition. Structures must have the same name, sequence of type names, and type definitions, and field names to be considered the same type. This rule applies recursively for nested or embedded types. All initializers for a shared global must have the same value, or a link error will result.

## Type Qualifiers

Variable declarations may have one or more qualifiers, specified in front of the type. These are summarized as

|                   |                                                                                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| < none: default > | local read/write memory, or an input parameter to a function                                                                        |
| const             | a compile-time constant, or a function parameter that is read-only                                                                  |
| attribute         | linkage between a vertex shader and OpenGL for per-vertex data                                                                      |
| uniform           | value does not change across the primitive being processed, uniforms form the linkage between a shader, OpenGL, and the application |
| varying           | linkage between a vertex shader and a fragment shader for interpolated data                                                         |
| in                | for function parameters passed into a function                                                                                      |
| out               | for function parameters passed back out of a function, but not initialized for use when passed in                                   |
| inout             | for function parameters passed both into and out of a function                                                                      |

Global variables can only use the qualifiers `const`, `attribute`, `uniform`, or `varying`. Only one may be specified.

Local variables can only use the qualifier `const`.

Function parameters can only use the `in`, `out`, `inout`, or `const` qualifiers. Parameter qualifiers are discussed in more detail in Section 6.1.1 "Function Calling Conventions".

Function return types and structure fields do not use qualifiers.

Data types for communication from one run of a shader to its next run (to communicate between fragments or between vertices) do not exist. This would prevent parallel execution of the same shader on multiple vertices or fragments.

Declarations of globals without a qualifier, or with just the `const` qualifier may include initializers, in which case they will be initialized before the first line of `main()` is executed. Such initializers must have constant type. Global variables without qualifiers that are not initialized in their declaration or by the application will not be initialized by OpenGL, but rather will enter `main()` with undefined values.

### Default Qualifiers

If no qualifier is present on a global variable, then the variable has no linkage to the application or shaders running on other processors. For either global or local unqualified variables, the declaration will appear to allocate memory associated with the processor it targets. This variable will provide read/write access to this allocated memory.

### Const

Named compile-time constants can be declared using the `const` qualifier. Any variables qualified as constant are read-only variables for that shader. Declaring variables as constant allows more descriptive shaders than using hard-wired numerical constants. The `const` qualifier can be used with any of the basic data types. It is an error to write to a `const` variable outside of its declaration, so they must be initialized when declared. For example,

``` glsl
const vec3 zAxis = vec3 (0.0, 0.0, 1.0);
```

Structure fields may not be qualified with `const`. Structure variables can be declared as `const`, and initialized with a structure constructor.

Initializers for `const` declarations must be formed from literal values, other `const` variables (not including function call paramaters), or expressions of these.

Constructors may be used in such expressions, but function calls may not.

### Integral Constant Expressions

An integral constant expression can be one of

- a literal integer value
- a global or local scalar integer variable qualified as `const`, not including function parameters qualified as `const`
- an expression whose operands are integral constant expressions, including constructors, but excluding function calls.

### Attribute

The `attribute` qualifier is used to declare variables that are passed to a vertex shader from OpenGL on a per-vertex basis. It is an error to declare an attribute variable in any type of shader other than a vertex shader. Attribute variables are read-only as far as the vertex shader is concerned. Values for attribute variables are passed to a vertex shader through the OpenGL vertex API or as part of a vertex array. They convey vertex attributes to the vertex shader and are expected to change on every vertex shader run. The attribute qualifier can be used only with the data types `float`, `vec2`, `vec3`, `vec4`, `mat2`, `mat3`, and `mat4`.

Attribute variables cannot be declared as arrays or structures.

Example declarations:

``` glsl
attribute vec4 position;
attribute vec3 normal;
attribute vec2 texCoord;
```

All the standard OpenGL vertex attributes have built-in variable names to allow easy integration between user programs and OpenGL vertex functions. See Section 7 "Built-in Variables" for a list of the built-in attribute names.

It is expected that graphics hardware will have a small number of fixed locations for passing vertex attributes. Therefore, the OpenGL Shading language defines each non-matrix attribute variable as having space for up to four floating-point values (i.e., a vec4). There is an implementation dependent limit on the number of attribute variables that can be used and if this is exceeded it will cause a link error. (Declared attribute variables that are not used do not count against this limit.) A float attribute counts the same amount against this limit as a vec4, so applications may want to consider packing groups of four unrelated float attributes together into a vec4 to better utilize the capabilities of the underlying hardware. A mat4 attribute will use up the equivalent of 4 vec4 attribute variable locations, a mat3 will use up the equivalent of 3 attribute variable locations, and a mat2 will use up 2 attribute variable locations. How this space is utilized by the matrices is hidden by the implementation through the API and language.

Attribute variables are required to have global scope, and must be declared outside of function bodies, before their first use.

### Uniform

The `uniform` qualifier is used to declare global variables whose values are the same across the entire primitive being processed. All `uniform` variables are read-only and are initialized either directly by an application via API commands, or indirectly by OpenGL.

An example declaration is:

``` glsl
uniform vec4 lightPosition;
```

The `uniform` qualifier can be used with any of the basic data types, or when declaring a variable whose type is a structure, or an array of any of these.

There is an implementation dependent limit on the amount of storage for uniforms that can be used for each type of shader and if this is exceeded it will cause a compile-time or link-time error. Uniform variables that are declared but not used do not count against this limit. The number of user-defined uniform variables and the number of built-in uniform variables that are used within a shader are added together to determine whether available uniform storage has been exceeded.

If multiple shaders are linked together, then they will share a single global uniform name space. Hence, types of uniforms with the same name must match across all shaders that are linked into a single executable.

### Varying

Varying variables provide the interface between the vertex shader, the fragment shader, and the fixed functionality between them. The vertex shader will compute values per vertex (such as color, texture coordinates, etc.) and write them to variables declared with the `varying` qualifier. A vertex shader may also read `varying` variables, getting back the same values it has written. Reading a `varying` variable in a vertex shader returns undefined values if it is read before being written.

By definition, varying variables are set per vertex and are interpolated in a perspective-correct manner over the primitive being rendered. If single-sampling, the interpolated value is for the fragment center. If multi-sampling, the interpolated value can be anywhere within the pixel, including the fragment center or one of the fragment samples.

A fragment shader may read from varying variables and the value read will be the interpolated value, as a function of the fragment's position within the primitive. A fragment shader can not write to a varying variable.

The type of varying variables with the same name declared in both the vertex and fragments shaders must match, otherwise the link command will fail. Only those varying variables used (i.e. read) in the fragment shader must be written to by the vertex shader; declaring superfluous varying variables in the vertex shader is permissible.

Varying variables are declared as in the following example:

``` glsl
varying vec3 normal;
```

The `varying` qualifier can be used only with the data types `float`, `vec2`, `vec3`, `vec4`, `mat2`, `mat3`, and `mat4`, or arrays of these. Structures cannot be `varying`.

If no vertex shader is active, the fixed functionality pipeline of OpenGL will compute values for the built-in varying variables that will be consumed by the fragment shader. Similarly, if no fragment shader is active, the vertex shader is responsible for computing and writing to the varying variables that are needed for OpenGL’s fixed functionality fragment pipeline.

Varying variables are required to have global scope, and must be declared outside of function bodies, before their first use.
