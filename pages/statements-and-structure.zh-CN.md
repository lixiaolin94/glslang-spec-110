# 语句和结构

OpenGL 着色语言的基本构建块是：

- 语句和声明
- 函数定义
- 选择（`if-else`）
- 迭代（`for`、`while` 和 `do-while`）
- 跳转（`discard`、`return`、`break` 和 `continue`）

着色器的整体结构如下：

``` glsl
translation-unit:
  global-declaration
  translation-unit global-declaration

global-declaration:
  function-definition
  declaration
```

也就是说，着色器是声明和函数体的序列。函数体定义为：

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

花括号用于将语句序列分组为复合语句。

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

简单声明、表达式和跳转语句以分号结尾。

上面的内容略有简化，应将第 9 节“着色语言语法”中指定的完整语法用作权威规范。

声明和表达式已经讨论过了。

## 函数定义

如上面的语法所示，有效的着色器是全局声明和函数定义的序列。函数声明如下例所示：

``` glsl
// 原型
returnType functionName (type0 arg0, type1 arg1, ..., typen argn);
```

函数定义如下：

``` glsl
// 定义
returnType functionName (type0 arg0, type1 arg1, ..., typen argn)
{
  // 执行一些计算
  return returnValue;
}
```

其中 returnType 必须存在并包括类型。每个 typeN 必须包括类型，并且可以选择包括限定符 `in`、`out`、`inout` 和/或 `const`。

通过使用函数名后跟括号中的参数列表来调用函数。

允许将数组作为参数，但不能作为返回类型。当数组声明为形式参数时，必须包括它们的大小。通过使用没有任何下标或括号的数组名将数组传递给函数，并且传入的数组参数的大小必须与形式参数声明中指定的大小匹配。

也允许将结构体作为参数。返回类型也可以是结构体。

有关声明和定义函数的语法的权威参考，请参见第 9 节“着色语言语法”。

所有函数在调用之前必须使用原型声明或使用函数体定义。例如：

``` glsl
float myfunc (float f,        // f 是输入参数
              out float g);   // g 是输出参数
```

不返回值的函数必须声明为 `void`。不接受输入参数的函数不需要在参数列表中使用 void，因为需要原型，因此在声明空参数列表"()"时没有歧义。为方便起见，提供了"(`void`)"作为参数列表的惯用法。

函数名可以重载。这允许对多个函数使用相同的函数名，只要参数列表类型不同即可。如果函数名和参数类型匹配，则它们的返回类型和参数限定符也必须匹配。重载在内置函数中被大量使用。

在解析重载函数（或实际上任何函数）时，会寻找函数签名的精确匹配。这包括数组大小的精确匹配。不会对返回类型或输入参数类型进行提升或降级。所有预期的输入和输出组合都必须定义为单独的函数。

例如，内置点积函数具有以下原型：

``` glsl
float dot (float x, float y);
float dot (vec2 x, vec2 y);
float dot (vec3 x, vec3 y);
float dot (vec4 x, vec4 y);
```

用户定义的函数可以有多个声明，但只能有一个定义。着色器可以重定义内置函数。如果在调用内置函数之前在着色器中重新声明它（即原型可见），则链接器将仅尝试在与其链接的着色器集中解析该调用。

函数 main 用作着色器的入口点。着色器不需要包含名为 main 的函数，但链接在一起形成单个程序的一组着色器中必须有一个。该函数不接受任何参数，不返回值，并且必须声明为 `void` 类型：

``` glsl
void main()
{
  ...
}
```

函数 main 可以包含 `return` 的使用。有关更多详细信息，请参阅第 6.4 节“跳转”。

### 函数调用约定

函数通过值返回调用。这意味着输入参数在调用时复制到函数中，输出参数在函数退出之前复制回调用者。因为函数使用参数的局部副本，所以在函数内部不存在变量别名的问题。在调用时，输入参数按从左到右的顺序求值。但是，将输出参数复制回调用者的顺序是未定义的。要控制通过函数定义或声明复制哪些参数：

- 关键字 `in` 用作限定符，表示要复制进参数，但不复制出。
- 关键字 `out` 用作限定符，表示要复制出参数，但不复制进。只要有可能，应使用此关键字以避免不必要地复制参数。
- 关键字 `inout` 用作限定符，表示参数既要复制进又要复制出。
- 没有这样的限定符声明的函数参数意味着与指定 in 相同的含义。

在函数中，允许写入只读参数。只修改函数的副本。可以通过使用 `const` 限定符声明参数来防止这种情况。

在调用函数时，不能将不计算为左值的表达式传递给声明为 `out` 或 `inout` 的参数。

不允许对函数的返回类型使用限定符。

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

但是，`const` 限定符不能与 `out` 或 `inout` 一起使用。上述内容用于函数声明（即原型）和函数定义。因此，函数定义可以有未命名的参数。

如果使用递归，行为是未定义的。递归意味着在函数调用的运行时堆栈中的任何一个时间点上出现多次任何函数。也就是说，函数不能直接或间接地调用自身。当在编译时可以检测到这种情况时，编译器可能会给出诊断消息，但并非所有这样的情况都可以在编译时检测到。

## 选择

着色语言中的条件控制流是通过 if 或 if-else 完成的：

``` glsl
if (bool-expression)
  true-statement
```

或

``` glsl
if (bool-expression)
  true-statement
else
  false-statement
```

如果表达式的计算结果为 `true`，则执行 true-statement。如果它的计算结果为 `false` 并且有 `else` 部分，则执行 false-statement。

任何类型计算结果为布尔值的表达式都可以用作条件表达式 bool-expression。向量类型不被接受为 `if` 的表达式。

条件语句可以嵌套。

## 迭代

允许使用以下 for、while 和 do 循环：

``` glsl
for (init-expression; condition-expression; loop-expression)
  sub-statement

while (condition-expression)
  sub-statement

do
  statement
while (condition-expression)
```

有关循环的权威规范，请参见第 9 节“着色语言语法”。

`for` 循环首先计算 *init-expression*，然后计算 *condition-expression*。如果 *condition-expression* 的计算结果为真，则执行循环体。执行完循环体后，`for` 循环将计算 *loop-expression*，然后循环回去计算 *condition-expression*，重复执行直到 *condition-expression* 的计算结果为假。然后退出循环，跳过其循环体和 *loop-expression*。如果仍在作用域内，则由 *loop-expression* 修改的变量在循环退出后保持其值。在 *init-expression* 或 *condition-expression* 中声明的变量仅在 `for` 循环的子语句结束之前有作用域。

`while` 循环首先计算 *condition-expression*。如果为真，则执行循环体。然后重复此操作，直到 *condition-expression* 的计算结果为假，退出循环并跳过其循环体。在 *condition-expression* 中声明的变量仅在 while 循环的子语句结束之前有作用域。

`do-while` 循环首先执行循环体，然后执行 *condition-expression*。重复此操作直到 *condition-expression* 的计算结果为假，然后退出循环。

*condition-expression* 的表达式必须计算为布尔值。

*condition-expression* 和 *init-expression* 都可以声明和初始化变量，但在 `do-while` 循环中不能在其 *condition-expression* 中声明变量。变量的作用域仅持续到构成循环体的子语句的末尾。

循环可以嵌套。

允许不终止的循环。非常长或不终止的循环的后果取决于平台。

## 跳转

跳转语句包括：

``` glsl
jump_statement:
  continue;
  break;
  return;
  return expression;
  discard;  // 只在片段着色器语言中
```

没有 "goto" 或其他非结构化的控制流。

`continue` 跳转仅在循环中使用。它跳过其所在的最内层循环的剩余部分。对于 `while` 和 `do-while` 循环，此跳转到循环 *condition-expression* 的下一次评估，从该评估循环继续之前定义的操作。对于 `for` 循环，跳转到 *loop-expression*，然后是 *condition-expression*。

`break` 跳转也只能在循环中使用。它只是立即退出包含 `break` 的最内层循环。不再执行 *condition-expression* 或 *loop-expression*。

`discard` 关键字只允许在片段着色器中使用。它可以在片段着色器中用于放弃当前片段的操作。此关键字会导致片段被丢弃，并且不会更新任何缓冲区。它通常在条件语句中使用，例如：

``` glsl
if (intensity < 0.0)
  discard;
```

片段着色器可以测试片段的 alpha 值，并根据该测试丢弃片段。

但是，应该注意的是，覆盖测试发生在片段着色器运行之后，并且覆盖测试可以更改 alpha 值。

`return` 跳转会立即退出当前函数。如果它有表达式，则该表达式是函数的返回值。

函数 main 可以使用 `return`。这只是导致 main 以与到达函数末尾时相同的方式退出。它并不意味着在片段着色器中使用 `discard`。在定义输出之前在 main 中使用 `return` 的行为与在定义输出之前到达 main 的末尾相同。
