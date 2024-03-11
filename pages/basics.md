# Basics

## Character Set

The source character set used for the OpenGL shading languages is a subset of ASCII. It includes the following characters:

- The letters **a-z**, **A-Z**, and the underscore (`_`).
- The numbers **0-9**.
- The symbols period (`.`), plus (`+`), dash (`-`), slash (`/`), asterisk (`*`), percent (`%`).
- The symbols angled brackets (`<` and `>`), square brackets (`[` and `]`), parentheses (`(` and `)`), braces (`{` and `}`).
- The symbols caret (`^`), vertical bar (`|`), ampersand (`&`), tilde (`~`), equals (`=`), exclamation point (`!`).
- The symbols colon (`:`), semicolon (`;`), comma (`,`), and question mark (`?`).
- The number sign (`#`) for preprocessor use.
- White space: the space character, horizontal tab, vertical tab, form feed, carriage-return, and line-feed.

Lines are relevant for compiler diagnostic messages and the preprocessor. They are terminated by carriage-return or line-feed. If both are used together, it will count as only a single line termination. For the remainder of this document, any these combinations is simply referred to as a new-line.

In general, the language’s use of this character set is case sensitive.

There are no character or string data types, so no quoting characters are included.

There is no end-of-file character. The end of a source string is indicated by a length, not a character.

## Source Strings

The source for a single shader is an array of strings of characters from the character set. A single shader is made from the concatenation of these strings. Each string can contain multiple lines, separated by new-lines. No new-lines need be present in a string; a single line can be formed from multiple strings. No new-lines or other characters are inserted by the implementation when it concatenates the strings to form a single shader. Multiple shaders of the same language (vertex or fragment) can be linked together to form a single program.

Diagnostic messages returned from compiling a shader must identify both the line number within a string and which source string the message applies to. Source strings are counted sequentially with the first string being string 0. Line numbers are one more than the number of new-lines that have been processed.

## Preprocessor

There is a preprocessor that processes the source strings before they are compiled.

The complete list of preprocessor directives is as follows.

``` glsl
#
#define
#undef

#if
#ifdef
#ifndef
#else
#elif
#endif

#error
#pragma

#extension
#version

#line
``` glsl

The following operators are also available

``` glsl
defined
```

Each number sign (`#`) can be preceded in its line only by spaces or horizontal tabs. It may also be followed by spaces and horizontal tabs, preceding the directive. Each directive is terminated by a new-line. Preprocessing does not change the number or relative location of new-lines in a source string.

The number sign (`#`) on a line by itself is ignored. Any directive not listed above will cause a diagnostic message and make the implementation treat the shader as ill-formed.

`#define` and `#undef` functionality are defined as is standard for C++ preprocessors for macro definitions both with and without macro parameters.

The following predefined macros are available

``` glsl
__LINE__
__FILE__
__VERSION__
```

*\_\_LINE\_\_* will substitute a decimal integer constant that is one more than the number of preceding new-lines in the current source string.

*\_\_FILE\_\_* will substitute a decimal integer constant that says which source string number is currently being processed.

*\_\_VERSION\_\_* will substitute a decimal integer reflecting the version number of the OpenGL shading language. The version of the shading language described in this document will have *\_\_VERSION\_\_* substitute the decimal integer 110.

All macro names containing two consecutive underscores (`__`) are reserved for future use as predefined macro names. All macro names prefixed with "GL_" ("GL" followed by a single underscore) are also reserved.

`#if`, `#ifdef`, `#ifndef`, `#else`, `#elif`, and `#endif` are defined to operate as is standard for C++ preprocessors.

Expressions following `#if` and `#elif` are restricted to expressions operating on literal integer constants, plus identifiers consumed by the `defined` operator. Character constants are not supported. The operators available are

| Precedence  | Operator class         | Operators    | Associativity |
| ----------- | ---------------------- | ------------ | ------------- |
| 1 (highest) | parenthetical grouping | ()           | NA            |
| 2           | unary                  | defined +-~! | Right to Left |
| 3           | multiplicative         | \*/%         | Left to Right |
| 4           | additive               | +-           | Left to Right |
| 5           | bit-wise shift         | << >>        | Left to Right |
| 6           | relational             | < > <= >=    | Left to Right |
| 7           | equality               | == !=        | Left to Right |
| 8           | bit-wise and           | &            | Left to Right |
| 9           | bit-wise exclusive or  | ^            | Left to Right |
| 10          | bit-wise inclusive or  | \|           | Left to Right |
| 11          | logical and            | &&           | Left to Right |
| 12          | logical inclusive or   | \|           | Left to Right |

The `defined` operator can be used in either of the following ways:

``` glsl
defined identifier
defined ( identifier )
```

There are no number sign based operators (no `#`, `#@`, `##`, etc.), nor is there a `sizeof` operator.

The semantics of applying operators to integer literals in the preprocessor match those standard in the C++ preprocessor, not those in the OpenGL Shading Language.

Preprocessor expressions will be evaluated according to the behavior of the host processor, not the processor targeted by the shader.

`#error` will cause the implementation to put a diagnostic message into the shader’s information log (see the API in external documentation for how to access a shader’s information log). The message will be the tokens following the `#error` directive, up to the first new-line. The implementation must then consider the shader to be ill-formed.

`#pragma` allows implementation dependent compiler control. Tokens following `#pragma` are not subject to preprocessor macro expansion. If an implementation does not recognize the tokens following

`#pragma`, then it will ignore that pragma. The following pragmas are defined as part of the language.

``` glsl
#pragma STDGL
```

The **STDGL** pragma is used to reserve pragmas for use by future revisions of this language. No implemention may use a pragma whose first token is **STDGL**.

``` glsl
#pragma optimize(on)
#pragma optimize(off)
```

can be used to turn off optimizations as an aid in developing and debugging shaders.

It can only be used

outside function definitions. By default, optimization is turned on for all shaders. The debug pragma

``` glsl
#pragma debug(on)
#pragma debug(off)
```

can be used to enable compiling and annotating a shader with debug information, so that it can be used with a debugger. It can only be used outside function definitions. By default, debug is turned off.

Shaders should declare the version of the language they are written to. The language version a shader is written to is specified by

``` glsl
#version number
```

where number must be 110 for this specification’s version of the language (following the same convention as *\_\_VERSION\_\_* above), in which case the directive will be accepted with no errors or warnings. Any number less than 110 will cause an error to be generated. Any number greater than the latest version of the language a compiler supports will also cause an error to be generated. Version 110 of the language does not require shaders to include this directive, and shaders that do not include a `#version` directive will be treated as targeting version 110. Compilers for subsequent versions of this language are guaranteed, on seeing the `#version 110` directive in a shader, to either support version 110, or to issue an error that they do not support it.

The `#version` directive must occur in a shader before anything else, except for comments and white space.

By default, compilers of this language must issue compile time syntactic, grammatical, and semantic errors for shaders that do not conform to this specification. Any extended behavior must first be enabled.

Directives to control the behavior of the compiler with to respect to extensions are declared with the `#extension` directive

``` glsl
#extension extension_name : behavior
#extension all : behavior
```

where extension_name is the name of an extension. Extension names are not documented in this specification. The token **all** means the behavior applies to all extensions supported by the compiler. The behavior can be one of the following

| behavior | Effect                                                                                                                                                                                                                                                                                                                                                |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| require  | Behave as specified by the extension extension_name. <br /><br />Give an error on the `#extension` if the extension extension_name is not supported, or if **all** is specified.                                                                                                                                                                                |
| enable   | Behave as specified by the extension extension_name.<br /><br />Warn on the `#extension` if the extension extension_name is not supported.<br /><br />Give an error on the `#extension` if **all** is specified.                                                                                                                                                      |
| warn     | Behave as specified by the extension extension_name, except issue warnings on any detectable use of that extension that is not supported by other enabled or required extensions.<br /><br />If **all** is specified, then warn on all detectable uses of any extension used.<br /><br />Warn on the `#extension` if the extension extension_name is not supported. |
| disable  | Behave (including issuing errors and warnings) as if the extension extension_name is not part of the language definition.<br /><br />If **all** is specified, then behavior must revert back to that of the non-extended core version of the language being compiled to.<br /><br />Warn on the `#extension` if the extension extension_name is not supported.      |

The **extension** directive is a simple, low-level mechanism to set the behavior for each extension. It does not define policies such as which combinations are appropriate, those must be defined elsewhere. Order of directives matters in setting the behavior for each extension: Directives that occur later override those seen earlier. The **all** variant sets the behavior for all extensions, overriding all previously issued **extension** directives, but only for the *behaviors* **warn** and **disable**.

The initial state of the compiler is as if the directive

``` glsl
#extension all : disable
```

was issued, telling the compiler that all error and warning reporting must be done according to this specification, ignoring any extensions.

Each extension can define its allowed granularity of scope. If nothing is said, the granularity is a shader (that is, a single compilation unit), and the extension directives must occur before any non-preprocessor tokens. If necessary, the linker can enforce granularities larger than a single compilation unit, in which case each involved shader will have to contain the necessary extension directive.

Macro expansion is not done on lines containing `#extension` and `#version` directives.

`#line` must have, after macro substitution, one of the following two forms:

``` glsl
#line line
#line line source-string-number
```

where *line* and *source-string-number* are constant integer expressions. After processing this directive (including its new-line), the implementation will behave as if it is compiling at line number *line+1* and source string number *source-string-number*. Subsequent source strings will be numbered sequentially, until another `#line` directive overrides that numbering.

## Comments

Comments are delimited by \/\* and \*\/, or by \/\/ and a new-line. The begin comment delimiters (\/\* or \/\/) are not recognized as comment delimiters inside of a comment, hence comments cannot be nested. If a comment resides entirely within a single line, it is treated syntactically as a single space.

## Tokens

The language is a sequence of tokens. A token can be

``` glsl
token:
  keyword
  identifier
  integer-constant
  floating-constant
  operator
```

## Keywords

The following are the keywords in the language, and cannot be used for any other purpose than that defined by this document:

``` glsl
attribute const uniform varying

break continue do for while

if

else

in out inout

float int void bool true false

discard return

mat2 mat3 mat4

vec2 vec3 vec4

ivec2 ivec3 ivec4

bvec2 bvec3 bvec4

sampler1D sampler2D sampler3D samplerCube sampler1DShadow sampler2DShadow

struct
```

The following are the keywords reserved for future use. Using them will result in an error:

``` glsl
asm

class union enum typedef template this packed

goto switch default

inline noinline volatile public static extern external interface

long short double half fixed unsigned

input output

hvec2 hvec3 hvec4 dvec2 dvec3 dvec4 fvec2 fvec3 fvec4

sampler2DRect sampler3DRect sampler2DRectShadow

sizeof cast

namespace using
```

In addition, all identifiers containing two consecutive underscores (`__`) are reserved as possible future keywords.

## Identifiers

Identifiers are used for variable names, function names, struct names, and field selectors (field selectors select components of vectors and matrices similar to structure fields, as discussed in Section 5.5 "Vector Components" and Section 5.6 "Matrix Components"). Identifiers have the form

``` glsl
identifier
  nondigit
  identifier nondigit
  identifier digit

nondigit: one of
  _ a b c d e f g h i j k l m n o p q r s t u v w x y z
  A B C D E F G H I J K L M N O P Q R S T U V W X Y Z

digit: one of
  0 1 2 3 4 5 6 7 8 9
```

Identifiers starting with "gl\_" are reserved for use by OpenGL, and may not be declared in a shader as either a variable or a function.
