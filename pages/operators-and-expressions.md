# Operators and Expressions

## Operators

The OpenGL Shading Language has the following operators. Those marked reserved are illegal.

| Precedence  | Operator class                                                                                                          | Operators                                      | Associativity |
| ----------- | ----------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- | ------------- |
| 1 (highest) | parenthetical grouping                                                                                                  | ()                                             | NA            |
| 2           | array subscript function call and constructor structure field selector, swizzler post fix increment and decrement | [] <br />() <br />. <br />++ --                      | Left to Right |
| 3           | prefix increment and decrement unary (tilde is reserved)                                                                | ++ -- +-~!                                     | Right to Left |
| 4           | multiplicative (modulus reserved)                                                                                       | \*/%                                           | Left to Right |
| 5           | additive                                                                                                                | +-                                             | Left to Right |
| 6           | bit-wise shift (reserved)                                                                                               | << >>                                          | Left to Right |
| 7           | relational                                                                                                              | < > <= >=                                      | Left to Right |
| 8           | equality                                                                                                                | == !=                                          | Left to Right |
| 9           | bit-wise and (reserved)                                                                                                 | &                                              | Left to Right |
| 10          | bit-wise exclusive or (reserved)                                                                                        | ^                                              | Left to Right |
| 11          | bit-wise inclusive or (reserved)                                                                                        | \|                                             | Left to Right |
| 12          | logical and                                                                                                             | &&                                             | Left to Right |
| 13          | logical exclusive or                                                                                                    | ^^                                             | Left to Right |
| 14          | logical inclusive or                                                                                                    | \|                                             | Left to Right |
| 15          | selection                                                                                                               | ?:                                             | Right to Left |
| 16          | assignment<br />arithmetic assignments<br />(modulus, shift, and bit-wise are reserved)                                     | = <br />+= -= <br />\*= /= %= <<= >>=<br />&= ^= \|= | Right to Left |
| 17 (lowest) | sequence                                                                                                                | ,                                              | Left to Right |

There is no address-of operator nor a dereference operator. There is no typecast operator, constructors are used instead.

## Array Subscripting

Array elements are accessed using the array subscript operator ( [ ] ). This is the only operator that operates on arrays. An example of accessing an array element is

``` glsl
diffuseColor += lightIntensity[3] * NdotL;
```

Array indices start at zero. Arrays elements are accessed using an expression whose type is an integer.

Behavior is undefined if a shader subscripts an array with an index less than 0 or greater than or equal to the size the array was declared with.

## Function Calls

If a function returns a value, then a call to that function may be used as an expression, whose type will be the type that was used to declare or define the function.

Function definitions and calling conventions are discussed in Section 6.1 "Function Definitions".

## Constructors

Constructors use the function call syntax, where the function name is a basic-type keyword or structure name, to make values of the desired type for use in an initializer or an expression. (See Section 9 "Shading Language Grammar" for details.) The parameters are used to initialize the constructed value. Constructors can be used to request a data type conversion to change from one scalar type to another scalar type, or to build larger types out of smaller types, or to reduce a larger type to a smaller type.

There is no fixed list of constructor prototypes. Constructors are not built-in functions. Syntactically, all lexically correct parameter lists are valid. Semantically, the number of parameters must be of sufficient size and correct type to perform the initialization. It is an error to include so many arguments to a constructor that they cannot all be used. Detailed rules follow. The prototypes actually listed below are merely a subset of examples.

### Conversion and Scalar Constructors

Converting between scalar types is done as the following prototypes indicate:

``` glsl
int(bool)     // converts a Boolean value to an int
int(float)    // converts a float value to an int
float(bool)   // converts a Boolean value to a float
float(int)    // converts an integer value to a float
bool(float)   // converts a float value to a Boolean
bool(int)     // converts an integer value to a Boolean
```

When constructors are used to convert a float to an int, the fractional part of the floating-point value is dropped.

When a constructor is used to convert an `int` or a `float` to `bool`, 0 and 0.0 are converted to `false`, and nonzero values are converted to `true`. When a constructor is used to convert a `bool` to an `int` or `float`, `false` is converted to 0 or 0.0, and `true` is converted to 1 or 1.0.

Identity constructors, like float(float) are also legal, but of little use.

Scalar constructors with non-scalar parameters can be used to take the first element from a non-scalar.

For example, the constructor float(vec3) will select the first component of the vec3 parameter.

### Vector and Matrix Constructors

Constructors can be used to create vectors or matrices from a set of scalars, vectors, or matrices. This includes the ability to shorten vectors.

If there is a single scalar parameter to a vector constructor, it is used to initialize all components of the constructed vector to that scalar’s value. If there is a single scalar parameter to a matrix constructor, it is used to initialize all the components on the matrix’s diagonal, with the remaining components initialized to 0.0. If there are non-scalar parameters, and/or multiple scalar parameters, they will be assigned in order, from left to right, to the components of the constructed value. In this case, there must be enough components provided in the parameters to provide an initializer for every component in the constructed value. If more components are provided in the last used argument to a constructor than are needed to initialize the constructed value, the left most components of that argument are used, and the remaining ones are ignored. It is an error to provide extra arguments beyond this last used argument. Matrices will be constructed in column major order. It is an error to construct matrices from other matrices. This is reserved for future use.

If the basic type (`bool`, `int`, or `float`) of a parameter to a constructor does not match the basic type of the object being constructed, the scalar construction rules (above) are used to convert the parameters.

Some useful vector constructors are as follows:

``` glsl
vec3(float)   // initializes each component of a vec3 with the float
vec4(ivec4)   // makes a vec4 from an ivec4, with component-wise conversion

vec2(float, float)              // initializes a vec2 with 2 floats
ivec3(int, int, int)            // initializes an ivec3 with 3 ints
bvec4(int, int, float, float)   // initializes with 4 Boolean conversions

vec2(vec3)    // drops the third component of a vec3
vec3(vec4)    // drops the fourth component of a vec4

vec3(vec2, float)   // vec3.x = vec2.x, vec3.y = vec2.y, vec3.z = float
vec3(float, vec2)   // vec3.x = float, vec3.y = vec2.x, vec3.z = vec2.y
vec4(vec3, float)
vec4(float, vec3)
vec4(vec2, vec2)
```

Some examples of these are:

``` glsl
vec4color = vec4(0.0, 1.0, 0.0, 1.0);
vec4 rgba = vec4(1.0);    // sets each component to 1.0
vec3 rgb = vec3(color);   // drop the 4th component
```

To initialize the diagonal of a matrix with all other elements set to zero:

``` glsl
mat2(float)
mat3(float)
mat4(float)
```

To initialize a matrix by specifying vectors, or by all 4, 9, or 16 floats for mat2, mat3 and mat4 respectively. The floats are assigned to elements in column major order.

``` glsl
mat2(vec2, vec2);
mat3(vec3, vec3, vec3);
mat4(vec4, vec4, vec4, vec4);

mat2(float, float,
     float, float);

mat3(float, float, float,
     float, float, float,
     float, float, float);

mat4(float, float, float, float,
     float, float, float, float,
     float, float, float, float,
     float, float, float, float);
```

A wide range of other possibilities exist, as long as enough components are present to initialize the matrix.

However, construction of a matrix from other matrices is currently reserved for future use.

### Structure Constructors

Once a structure is defined, and its type is given a name, a constructor is available with the same name to construct instances of that structure. For example:

``` glsl
struct light {
  float intensity;
  vec3 position;
};

light lightVar = light(3.0, vec3(1.0, 2.0, 3.0));
```

The arguments to the constructor must be in the same order and of the same type as they were declared in the structure.

Structure constructors can be used as initializers or in expressions.

## Vector Components

The names of the components of a vector are denoted by a single letter. As a notational convenience, several letters are associated with each component based on common usage of position, color or texture coordinate vectors. The individual components of a vector can be selected by following the variable name with period ( . ) and then the component name.

The component names supported are:

|              |                                                                  |
| ------------ | ---------------------------------------------------------------- |
| {x, y, z, w} | useful when accessing vectors that represent points or normals   |
| {r, g, b, a} | useful when accessing vectors that represent colors              |
| {s, t, p, q} | useful when accessing vectors that represent texture coordinates |

The component names x, r, and s are, for example, synonyms for the same (first) component in a vector.

Note that the third component of a texture, r in OpenGL, has been renamed p so as to avoid the confusion with r (for red) in a color.

Accessing components beyond those declared for the vector type is an error so, for example:

``` glsl
vec2 pos;
pos.x   // is legal
pos.z   // is illegal
```

The component selection syntax allows multiple components to be selected by appending their names (from the same name set) after the period (.).

``` glsl
vec4 v4;
v4.rgba;    // is a vec4 and the same as just using v4,
v4.rgb;     // is a vec3,
v4.b;       // is a float,
v4.xy;      // is a vec2,
v4.xgba;    // is illegal - the component names do not come from
            //the same set.
```

The order of the components can be different to swizzle them, or replicated:

``` glsl
vec4 pos = vec4(1.0, 2.0, 3.0, 4.0);
vec4 swiz = pos.wzyx; // swiz = (4.0, 3.0, 2.0, 1.0)
vec4 dup = pos.xxyy; // dup = (1.0, 1.0, 2.0, 2.0)
```

This notation is more concise than the constructor syntax. To form an r-value, it can be applied to any expression that results in a vector r-value.

The component group notation can occur on the left hand side of an expression.

``` glsl
vec4 pos = vec4(1.0, 2.0, 3.0, 4.0);
pos.xw = vec2(5.0, 6.0);        // pos = (5.0, 2.0, 3.0, 6.0)
pos.wx = vec2(7.0, 8.0);        // pos = (8.0, 2.0, 3.0, 7.0)
pos.xx = vec2(3.0, 4.0);        // illegal - 'x' used twice
pos.xy = vec3(1.0, 2.0, 3.0);   // illegal - mismatch between vec2 and vec3
```

To form an l-value, swizzling must be applied to an l-value of vector type, contain no duplicate components, and results in an l-value of scalar or vector type, depending on number of components specified.

Array subscripting syntax can also be applied to vectors to provide numeric indexing. So in

``` glsl
vec4 pos;
```

*pos[2]* refers to the third element of pos and is equivalent to *pos.z*. This allows variable indexing into a vector, as well as a generic way of accessing components. Any integer expression can be used as the subscript. The first component is at index zero. Behavior is undefined if the index is greater than or equal to the size of the vector.

## Matrix Components

The components of a matrix can be accessed using array subscripting syntax. Applying a single subscript to a matrix treats the matrix as an array of column vectors, and selects a single column, whose type is a vector of the same size as the matrix. The leftmost column is column 0. A second subscript would then operate on the column vector, as defined earlier for vectors. Hence, two subscripts select a column and then a row.

``` glsl
mat4 m;
m[1] = vec4(2.0);   // sets the second column to all 2.0
m[0][0] = 1.0;      // sets the upper left element to 1.0
m[2][3] = 2.0;      // sets the 4th element of the third column to 2.0
```

Behavior is undefined when accessing a component outside the bounds of a matrix (e.g., component \[3\]\[3\] of a mat3).

## Structures and Fields

As with vector components and swizzling, the fields of a structure are also selected using the period ( . ).

In total, the following operators are allowed to operate on a structure:

|                          |       |
| ------------------------ | ----- |
| structure field selector | .     |
| equality                 | == != |
| assignment               | =     |

The equality and assignment operators are only valid if the two operands’ types are of the same declared structure. When using the equality operators, two structures are equal if and only if all the fields are component-wise equal.

## Assignments

Assignments of values to variable names are done with the assignment operator (`=`), like

``` glsl
lvalue = expression
```

The assignment operator stores the value of expression into lvalue. It will compile only if expression and lvalue have the same type. All desired type-conversions must be specified explicitly via a constructor. L-values must be writable. Variables that are built-in types, entire structures, structure fields, l-values with the field selector (`.`) applied to select components or swizzles without repeated fields, and l-values dereferenced with the array subscript operator (`[]`) are all l-values. Other binary or unary expressions, non-dereferenced arrays, function names, swizzles with repeated fields, and constants cannot be l-values.

The ternary operator (`?:`) is also not allowed as an l-value.

Expressions on the left of an assignment are evaluated before expressions on the right of the assignment.

Other assignment operators are

- The arithmetic assignments add into (`+=`), subtract from (`-=`), multiply into (`*=`), and divide into (`/=`). The expression

  ``` glsl
  lvalue op= expression
  ```

  is equivalent to

  ``` glsl
  lvalue = lvalue op expression
  ```

  and the l-value and expression must satisfy the semantic requirements of both op and equals (`=`).

- The assignments modulus into (`%=`), left shift by (`<<=`), right shift by (`>>=`), inclusive or into (`|=`), and exclusive or into (`^=`). These operators are reserved for future use.

Reading a variable before writing (or initializing) it is legal, however the value is undefined.

## Expressions

Expressions in the shading language are built from the following:

- Constants of type `bool`, `int`, `float`, all vector types, and all matrix types.
- Constructors of all types.
- Variable names of all types, except array names not followed by a subscript.
- Subscripted array names.
- Function calls that return values.
- Component field selectors and array subscript results.
- Parenthesized expression. Parentheses can be used to group operations. Operations within parentheses are done before operations across parentheses.
- The arithmetic binary operators add (`+`), subtract (`-`), multiply (`_`), and divide (`/`), that operate on integer and floating-point typed expressions (including vectors and matrices). The two operands must be the same type, or one can be a scalar float and the other a float vector or matrix, or one can be a scalar integer and the other an integer vector. Additionally, for multiply (`_`), one can be a vector and the other a matrix with the same dimensional size of the vector. These result in the same fundamental type (integer or float) as the expressions they operate on. If one operand is scalar and the other is a vector or matrix, the scalar is applied component-wise to the vector or matrix, resulting in the same type as the vector or matrix. Dividing by zero does not cause an exception but does result in an unspecified value. Multiply (`_`) applied to two vectors yields a component-wise multiply. Multiply (`_`) applied to two matrices yields a linear algebraic matrix multiply, not a component-wise multiply. Use the built-in functions dot, cross, and matrixCompMult to get, respectively, vector dot product, vector cross product, and matrix component-wise multiplication.
- The operator modulus (`%`) is reserved for future use.
- The arithmetic unary operators negate (`-`), post- and pre-increment and decrement (`--` and `++`) that operate on integer or floating-point values (including vectors and matrices). These result with the same type they operated on. For post- and pre-increment and decrement, the expression must be one that could be assigned to (an `l-value`). Pre-increment and pre-decrement add or subtract 1 or 1.0 to the contents of the expression they operate on, and the value of the pre-increment or pre-decrement expression is the resulting value of that modification. Post-increment and post-decrement expressions add or subtract 1 or 1.0 to the contents of the expression they operate on, but the resulting expression has the expression’s value before the post-increment or post-decrement was executed.
- The relational operators greater than (`>`), less than (`<`), greater than or equal (`>=`), and less than or equal (`<=`) operate only on scalar integer and scalar floating-point expressions. The result is scalar Boolean. The operands’ types must match. To do component-wise comparisons on vectors, use the built-in functions lessThan, lessThanEqual, greaterThan, and greaterThanEqual.
- The equality operators equal (`==`), and not equal (`!=`) operate on all types except arrays. They result in a scalar Boolean. For vectors, matrices, and structures, all components of the operands must be equal for the operands to be considered equal. To get component-wise equality results for vectors, use the built-in functions equal and notEqual.
- The logical binary operators and (`&&`), or (`||`), and exclusive or (`^^`). They operate only on two Boolean expressions and result in a Boolean expression. And (`&&`) will only evaluate the right hand operand if the left hand operand evaluated to true. Or (`||`) will only evaluate the right hand operand if the left hand operand evaluated to false. Exclusive or (`^^`) will always evaluate both operands.
- The logical unary operator not (`!`). It operates only on a Boolean expression and results in a Boolean expression. To operate on a vector, use the built-in function not.
- The sequence (`,`) operator that operates on expressions by returning the type and value of the right-most expression in a comma separated list of expressions. All expressions are evaluated, in order, from left to right.
- The ternary selection operator (`?:`). It operates on three expressions (`exp1 ? exp2 : exp3`). This operator evaluates the first expression, which must result in a scalar Boolean. If the result is true, it selects to evaluate the second expression, otherwise it selects to evaluate the third expression. Only one of the second and third expressions is evaluated. The second and third expressions must be the same type, but can be of any type other than an array. The resulting type is the same as the type of the second and third expressions.
- Operators and (`&`), or (`|`), exclusive or (`^`), not (`~`), right-shift (`>>`), left-shift (`<<`). These operators are reserved for future use.

For a complete specification of the syntax of expressions, see Section 9 "Shading Language Grammar" .

When the operands are of a different type they must fit into one of the following rules:

- one of the arguments is a float (i.e. a scalar), in which case the result is as if the scalar value was replicated into a vector or matrix before being applied.
- the left argument is a floating-point vector and the right is a matrix with a compatible dimension in which case the `*` operator will do a row vector matrix multiplication.
- the left argument is a matrix and the right is a floating-point vector with a compatible dimension in which case the `*` operator will do a column vector matrix multiplication.

## Vectorand Matrix Operations

With a few exceptions, operations are component-wise. When an operator operates on a vector or matrix, it is operating independently on each component of the vector or matrix, in a component-wise fashion.

For example,

``` glsl
vec3 v, u;
float f;
v = u + f;
```

will be equivalent to

``` glsl
v.x = u.x + f;
v.y = u.y + f;
v.z = u.z + f;
```

And

``` glsl
vec3 v, u, w;
w = v + u;
```

will be equivalent to

``` glsl
w.x = v.x + u.x;
w.y = v.y + u.y;
w.z = v.z + u.z;
```

and likewise for most operators and all integer and floating point vector and matrix types. The exceptions are matrix multiplied by vector, vector multiplied by matrix, and matrix multiplied by matrix. These do not operate component-wise, but rather perform the correct linear algebraic multiply. They require the size of the operands match.

``` glsl
vec3 v, u;
mat3 m;

u = v * m;
```

is equivalent to

``` glsl
u.x = dot(v, m[0]); // m[0] is the left column of m
u.y = dot(v, m[1]); // dot(a,b) is the inner (dot) product of a and b
u.z = dot(v, m[2]);
```

And

``` glsl
u = m * v;
```

is equivalent to

``` glsl
u.x = m[0].x * v.x + m[1].x * v.y + m[2].x * v.z;
u.y = m[0].y * v.x + m[1].y * v.y + m[2].y * v.z;
u.z = m[0].z * v.x + m[1].z * v.y + m[2].z * v.z;
```

And

``` glsl
mat m, n, r;

r = m * n;
```

is equivalent to

``` glsl
r[0].x = m[0].x * n[0].x + m[1].x * n[0].y + m[2].x * n[0].z;
r[1].x = m[0].x * n[1].x + m[1].x * n[1].y + m[2].x * n[1].z;
r[2].x = m[0].x * n[2].x + m[1].x * n[2].y + m[2].x * n[2].z;


r[0].y = m[0].y * n[0].x + m[1].y * n[0].y + m[2].y * n[0].z;
r[1].y = m[0].y * n[1].x + m[1].y * n[1].y + m[2].y * n[1].z;
r[2].y = m[0].y * n[2].x + m[1].y * n[2].y + m[2].y * n[2].z;

r[0].z = m[0].z * n[0].x + m[1].z * n[0].y + m[2].z * n[0].z;
r[1].z = m[0].z * n[1].x + m[1].z * n[1].y + m[2].z * n[1].z;
r[2].z = m[0].z * n[2].x + m[1].z * n[2].y + m[2].z * n[2].z;
```

and similarly for vectors and matrices of size 2 and 4.

All unary operations work component-wise on their operands. For binary arithmetic operations, if the two operands are the same type, then the operation is done component-wise and produces a result that is the same type as the operands. If one operand is a scalar float and the other operand is a vector or matrix, then the operation proceeds as if the scalar value was replicated to form a matching vector or matrix operand.
