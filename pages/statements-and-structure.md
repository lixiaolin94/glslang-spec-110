# Statements and Structure

The fundamental building blocks of the OpenGL Shading Language are:

- statements and declarations
- function definitions
- selection (`if-else`)
- iteration (`for`, `while`, and `do-while`)
- jumps (`discard`, `return`, `break`, and `continue`)

The overall structure of a shader is as follows

``` glsl
translation-unit:
  global-declaration
  translation-unit global-declaration

global-declaration:
  function-definition
  declaration
```

That is, a shader is a sequence of declarations and function bodies. Function bodies are defined as

``` glsl
function-definition:
  function-prototype { statement-list }

statement-list:
  statement
  statement-list statement

statement:
  compound-statement
  simple-statement
```

Curly braces are used to group sequences of statements into compound statements.

``` glsl
compound-statement:
  { statement-list }

simple-statement:
  declaration-statement
  expression-statement
  selection-statement
  iteration-statement
  jump-statement
```

Simple declaration, expression, and jump statements end in a semi-colon.

This above is slightly simplified, and the complete grammar specified in Section 9 "Shading Language Grammar" should be used as the definitive specification.

Declarations and expressions have already been discussed.

## Function Definitions

As indicated by the grammar above, a valid shader is a sequence of global declarations and function definitions. A function is declared as the following example shows:

``` glsl
// prototype
returnType functionName (type0 arg0, type1 arg1, ..., typen argn);
```

and a function is defined like

``` glsl
// definition
returnType functionName (type0 arg0, type1 arg1, ..., typen argn)
{
  // do some computation
  return returnValue;
}
```

Where returnType must be present and include a type. Each of the typeN must include a type and can optionally include the qualifier `in`, `out`, `inout`, and/or `const`.

A function is called by using its name followed by a list of arguments in parentheses.

Arrays are allowed as arguments, but not as the return type. When arrays are declared as formal parameters, their size must be included. An array is passed to a function by using the array name without any subscripting or brackets, and the size of the array argument passed in must match the size specified in the formal parameter declaration.

Structures are also allowed as arguments. The return type can also be structure.

See Section 9 "Shading Language Grammar" for the definitive reference on the syntax to declare and define functions.

All functions must be either declared with a prototype or defined with a body before they are called. For example:

``` glsl
float myfunc (float f,        // f is an input parameter
              out float g);   // g is an output parameter
```

Functions that return no value must be declared as `void`. Functions that accept no input arguments need not use void in the argument list because prototypes are required and therefore there is no ambiguity when an empty argument list "( )" is declared. The idiom "(`void`)" as a parameter list is provided for convenience.

Function names can be overloaded. This allows the same function name to be used for multiple functions, as long as the argument list types differ. If functions’ names and argument types match, then their return type and parameter qualifiers must also match. Overloading is used heavily in the built-in functions.

When overloaded functions (or indeed any functions) are resolved, an exact match for the function's signature is sought. This includes exact match of array size as well. No promotion or demotion of the return type or input argument types is done. All expected combination of inputs and outputs must be defined as separate functions.

For example, the built-in dot product function has the following prototypes:

``` glsl
float dot (float x, float y);
float dot (vec2 x, vec2 y);
float dot (vec3 x, vec3 y);
float dot (vec4 x, vec4 y);
```

User-defined functions can have multiple declarations, but only one definition. A shader can redefine built-in functions. If a built-in function is redeclared in a shader (i.e. a prototype is visible) before a call to it, then the linker will only attempt to resolve that call within the set shaders that are linked with it.

The function main is used as the entry point to a shader. A shader need not contain a function named main, but one shader in a set of shaders linked together to form a single program must. This function takes no arguments, returns no value, and must be declared as type `void`:

``` glsl
void main()
{
  ...
}
```

The function main can contain uses of `return`. See Section 6.4 "Jumps" for more details.

### Function Calling Conventions

Functions are called by value-return. This means input arguments are copied into the function at call time, and output arguments are copied back to the caller before function exit. Because the function works with local copies of parameters, there are no issues regarding aliasing of variables within a function. At call time, input arguments are evaluated in order, from left to right. However, the order in which output parameters are copied back to the caller is undefined. To control what parameters are copied in and/or out through a function definition or declaration:

- The keyword `in` is used as a qualifier to denote a parameter is to be copied in, but not copied out.
- The keyword `out` is used as a qualifier to denote a parameter is to be copied out, but not copied in. This should be used whenever possible to avoid unnecessarily copying parameters in.
- The keyword `inout` is used as a qualifier to denote the parameter is to be both copied in and copied out.
- A function parameter declared with no such qualifier means the same thing as specifying in.

In a function, writing to an input-only parameter is allowed. Only the function’s copy is modified. This can be prevented by declaring a parameter with the `const` qualifier.

When calling a function, expressions that do not evaluate to l-values cannot be passed to parameters declared as `out` or `inout`.

No qualifier is allowed on the return type of a function.

``` glsl
function-prototype:
  type function-name(const-qualifier parameter-qualifier type name array-specifier, ... )

type:
  any basic type, structure name, or structure definition

const-qualifier:
  empty
  const

parameter-qualifier:
  empty
  in
  out
  inout

name:
  empty
  identifier

array-specifier:
  empty
  [ integral-constant-expression ]
```

However, the `const` qualifier cannot be used with `out` or `inout`. The above is used for function declarations (i.e. prototypes) and for function definitions. Hence, function definitions can have unnamed arguments.

Behavior is undefined if recursion is used. Recursion means having any function appearing more than once at any one time in the run-time stack of function calls. That is, a function may not call itself either directly or indirectly. Compilers may give diagnostic messages when this is detectable at compile time, but not all such cases can be detected at compile time.

## Selection

Conditional control flow in the shading language is done by either if, or if-else:

``` glsl
if (bool-expression)
  true-statement
```

or

``` glsl
if (bool-expression)
  true-statement
else
  false-statement
```

If the expression evaluates to `true`, then true-statement is executed. If it evaluates to `false` and there is an `else` part then false-statement is executed.

Any expression whose type evaluates to a Boolean can be used as the conditional expression bool-expression. Vector types are not accepted as the expression to `if`.

Conditionals can be nested.

## Iteration

For, while, and do loops are allowed as follows:

``` glsl
for (init-expression; condition-expression; loop-expression)
  sub-statement

while (condition-expression)
  sub-statement

do
  statement
while (condition-expression)
```

See Section 9 "Shading Language Grammar" for the definitive specification of loops.

The `for` loop first evaluates the *init-expression*, then the *condition-expression*. If the *condition-expression* evaluates to true, then the body of the loop is executed. After the body is executed, a `for` loop will then evaluate the *loop-expression*, and then loop back to evaluate the *condition-expression*, repeating until the *condition-expression* evaluates to false. The loop is then exited, skipping its body and skipping its *loop-expression*. Variables modified by the *loop-expression* maintain their value after the loop is exited, provided they are still in scope. Variables declared in *init-expression* or *condition-expression* are only in scope until the end of the sub-statement of the `for` loop.

The `while` loop first evaluates the *condition-expression*. If true, then the body is executed. This is then repeated, until the *condition-expression* evaluates to false, exiting the loop and skipping its body. Variables declared in the *condition-expression* are only in scope until the end of the sub-statement of the while loop.

The `do-while` loop first executes the body, then executes the *condition-expression*. This is repeated until *condition-expression* evaluates to false, and then the loop is exited.

Expressions for *condition-expression* must evaluate to a Boolean.

Both the *condition-expression* and the *init-expression* can declare and initialize a variable, except in the `do-while` loop, which cannot declare a variable in its *condition-expression*. The variable’s scope lasts only until the end of the sub-statement that forms the body of the loop.

Loops can be nested.

Non-terminating loops are allowed. The consequences of very long or non-terminating loops are platform dependent.

## Jumps

These are the jumps:

``` glsl
jump_statement:
  continue;
  break;
  return;
  return expression;
  discard;  // in the fragment shader language only
```

There is no "goto" nor other non-structured flow of control.

The `continue` jump is used only in loops. It skips the remainder of the body of the inner most loop of which it is inside. For `while` and `do-while` loops, this jump is to the next evaluation of the loop *condition-expression* from which the loop continues as previously defined. For `for` loops, the jump is to the *loop-expression*, followed by the *condition-expression*.

The `break` jump can also be used only in loops. It is simply an immediate exit of the inner-most loop containing the `break`. No further execution of *condition-expression* or *loop-expression* is done.

The `discard` keyword is only allowed within fragment shaders. It can be used within a fragment shader to abandon the operation on the current fragment. This keyword causes the fragment to be discarded and no updates to any buffers will occur. It would typically be used within a conditional statement, for example:

``` glsl
if (intensity < 0.0)
  discard;
```

A fragment shader may test a fragment’s alpha value and discard the fragment based on that test.

However, it should be noted that coverage testing occurs after the fragment shader runs, and the coverage test can change the alpha value.

The `return` jump causes immediate exit of the current function. If it has expression then that is the return value for the function.

The function main can use `return`. This simply causes main to exit in the same way as when the end of the function had been reached. It does not imply a use of `discard` in a fragment shader. Using `return` in main before defining outputs will have the same behavior as reaching the end of main before defining outputs.
