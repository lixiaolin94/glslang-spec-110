# Shading Language Grammar

The grammar is fed from the output of lexical analysis. The tokens returned from lexical analysis are

``` glsl
ATTRIBUTE CONST BOOL FLOAT INT
BREAK CONTINUE DO ELSE FOR IF DISCARD RETURN
BVEC2 BVEC3 BVEC4 IVEC2 IVEC3 IVEC4 VEC2 VEC3 VEC4
MAT2 MAT3 MAT4 IN OUT INOUT UNIFORM VARYING
SAMPLER1D SAMPLER2D SAMPLER3D SAMPLERCUBE SAMPLER1DSHADOW SAMPLER2DSHADOW
STRUCT VOID WHILE

IDENTIFIER TYPE_NAME FLOATCONSTANT INTCONSTANT BOOLCONSTANT
FIELD_SELECTION
LEFT_OP RIGHT_OP
INC_OP DEC_OP LE_OP GE_OP EQ_OP NE_OP
AND_OP OR_OP XOR_OP MUL_ASSIGN DIV_ASSIGN ADD_ASSIGN
MOD_ASSIGN LEFT_ASSIGN RIGHT_ASSIGN AND_ASSIGN XOR_ASSIGN OR_ASSIGN
SUB_ASSIGN

LEFT_PAREN RIGHT_PAREN LEFT_BRACKET RIGHT_BRACKET LEFT_BRACE RIGHT_BRACE DOT
COMMA COLON EQUAL SEMICOLON BANG DASH TILDE PLUS STAR SLASH PERCENT
LEFT_ANGLE RIGHT_ANGLE VERTICAL_BAR CARET AMPERSAND QUESTION
```

The following describes the grammar for the OpenGL Shading Language in terms of the above tokens.

``` glsl
variable_identifier:
  IDENTIFIER

primary_expression:
variable_identifier
  INTCONSTANT
  FLOATCONSTANT
  BOOLCONSTANT
  LEFT_PAREN expression RIGHT_PAREN

postfix_expression:
  primary_expression
  postfix_expression LEFT_BRACKET integer_expression RIGHT_BRACKET
  function_call
  postfix_expression DOT FIELD_SELECTION
  postfix_expression INC_OP
  postfix_expression DEC_OP

integer_expression:
  expression

function_call:
  function_call_generic

function_call_generic:
  function_call_header_with_parameters RIGHT_PAREN
  function_call_header_no_parameters RIGHT_PAREN

function_call_header_no_parameters:
  function_call_header VOID
  function_call_header

function_call_header_with_parameters:
  function_call_header assignment_expression
  function_call_header_with_parameters COMMA assignment_expression

function_call_header:
  function_identifier LEFT_PAREN

function_identifier:
  constructor_identifier
  IDENTIFIER

// Grammar Note: Constructors look like functions, but lexical anaylsis recognized most of them as keywords.

constructor_identifier:
  FLOAT
  INT
  BOOL
  VEC2
  VEC3
  VEC4
  BVEC2
  BVEC3
  BVEC4
  IVEC2
  IVEC3
  IVEC4
  MAT2
  MAT3
  MAT4
  TYPE_NAME

unary_expression:
  postfix_expression
  INC_OP unary_expression
  DEC_OP unary_expression
  unary_operator unary_expression

// Grammar Note: No traditional style type casts.

unary_operator:
  PLUS
  DASH
  BANG
  TILDE // reserved

// Grammar Note: No '*' or '&' unary ops. Pointers are not supported.

multiplicative_expression:
  unary_expression
  multiplicative_expression STAR unary_expression
  multiplicative_expression SLASH unary_expression
  multiplicative_expression PERCENT unary_expression // reserved

additive_expression:
  multiplicative_expression
  additive_expression PLUS multiplicative_expression
  additive_expression DASH multiplicative_expression

shift_expression:
  additive_expression
  shift_expression LEFT_OP additive_expression // reserved
  shift_expression RIGHT_OP additive_expression // reserved

relational_expression:
  shift_expression
  relational_expression LEFT_ANGLE shift_expression
  relational_expression RIGHT_ANGLE shift_expression
  relational_expression LE_OP shift_expression
  relational_expression GE_OP shift_expression

equality_expression:
  relational_expression
  equality_expression EQ_OP relational_expression
  equality_expression NE_OP relational_expression

and_expression:
  equality_expression
  and_expression AMPERSAND equality_expression // reserved

exclusive_or_expression:
  and_expression
  exclusive_or_expression CARET and_expression // reserved

inclusive_or_expression:
  exclusive_or_expression
  inclusive_or_expression VERTICAL_BAR exclusive_or_expression // reserved

logical_and_expression:
  inclusive_or_expression
  logical_and_expression AND_OP inclusive_or_expression

logical_xor_expression:
  logical_and_expression
  logical_xor_expression XOR_OP logical_and_expression

logical_or_expression:
  logical_xor_expression
  logical_or_expression OR_OP logical_xor_expression

conditional_expression:
  logical_or_expression
  logical_or_expression QUESTION expression COLON conditional_expression

assignment_expression:
  conditional_expression
  unary_expression assignment_operator assignment_expression

assignment_operator:
  EQUAL
  MUL_ASSIGN
  DIV_ASSIGN
  MOD_ASSIGN // reserved
  ADD_ASSIGN
  SUB_ASSIGN
  LEFT_ASSIGN // reserved
  RIGHT_ASSIGN // reserved
  AND_ASSIGN // reserved
  XOR_ASSIGN // reserved
  OR_ASSIGN // reserved

expression:
  assignment_expression
  expression COMMA assignment_expression

constant_expression:
  conditional_expression

declaration:
  function_prototype SEMICOLON
  init_declarator_list SEMICOLON

function_prototype:
  function_declarator RIGHT_PAREN

function_declarator:
  function_header
  function_header_with_parameters

function_header_with_parameters:
  function_header parameter_declaration
  function_header_with_parameters COMMA parameter_declaration

function_header:
  fully_specified_type IDENTIFIER LEFT_PAREN

parameter_declarator:
  type_specifier IDENTIFIER
  type_specifier IDENTIFIER LEFT_BRACKET constant_expression RIGHT_BRACKET

parameter_declaration:
  type_qualifier parameter_qualifier parameter_declarator
  parameter_qualifier parameter_declarator
  type_qualifier parameter_qualifier parameter_type_specifier
  parameter_qualifier parameter_type_specifier

parameter_qualifier:
  /* empty */
  IN
  OUT
  INOUT

parameter_type_specifier:
  type_specifier
  type_specifier LEFT_BRACKET constant_expression RIGHT_BRACKET

init_declarator_list:
  single_declaration
  init_declarator_list COMMA IDENTIFIER
  init_declarator_list COMMA IDENTIFIER LEFT_BRACKET RIGHT_BRACKET
  init_declarator_list COMMA IDENTIFIER LEFT_BRACKET constant_expression RIGHT_BRACKET
  init_declarator_list COMMA IDENTIFIER EQUAL initializer

single_declaration:
  fully_specified_type
  fully_specified_type IDENTIFIER
  fully_specified_type IDENTIFIER LEFT_BRACKET RIGHT_BRACKET
  fully_specified_type IDENTIFIER LEFT_BRACKET constant_expression RIGHT_BRACKET
  fully_specified_type IDENTIFIER EQUAL initializer

// Grammar Note: No 'enum', or 'typedef'.

fully_specified_type:
  type_specifier
  type_qualifier type_specifier

type_qualifier:
  CONST
  ATTRIBUTE // Vertex only.
  VARYING
  UNIFORM

type_specifier:
  VOID
  FLOAT
  INT
  BOOL
  VEC2
  VEC3
  VEC4
  BVEC2
  BVEC3
  BVEC4
  IVEC2
  IVEC3
  IVEC4
  MAT2
  MAT3
  MAT4
  SAMPLER1D
  SAMPLER2D
  SAMPLER3D
  SAMPLERCUBE
  SAMPLER1DSHADOW
  SAMPLER2DSHADOW
  struct_specifier
  TYPE_NAME

struct_specifier:
  STRUCT IDENTIFIER LEFT_BRACE struct_declaration_list RIGHT_BRACE
  STRUCT LEFT_BRACE struct_declaration_list RIGHT_BRACE

struct_declaration_list:
  struct_declaration
  struct_declaration_list struct_declaration

struct_declaration:
  type_specifier struct_declarator_list SEMICOLON

struct_declarator_list:
  struct_declarator
  struct_declarator_list COMMA struct_declarator

struct_declarator:
  IDENTIFIER
  IDENTIFIER LEFT_BRACKET constant_expression RIGHT_BRACKET

initializer:
  assignment_expression

declaration_statement:
  declaration

statement:
  compound_statement
  simple_statement

// Grammar Note: No labeled statements; 'goto' is not supported.

simple_statement:
  declaration_statement
  expression_statement
  selection_statement
  iteration_statement
  jump_statement

compound_statement:
  LEFT_BRACE RIGHT_BRACE
  LEFT_BRACE statement_list RIGHT_BRACE

statement_no_new_scope:
  compound_statement_no_new_scope
  simple_statement

compound_statement_no_new_scope:
  LEFT_BRACE RIGHT_BRACE
  LEFT_BRACE statement_list RIGHT_BRACE

statement_list:
  statement
  statement_list statement

expression_statement:
  SEMICOLON
  expression SEMICOLON

selection_statement:
  IF LEFT_PAREN expression RIGHT_PAREN selection_rest_statement

selection_rest_statement:
  statement ELSE statement
  statement

// Grammar Note: No 'switch'. Switch statements not supported.

condition:
  expression
  fully_specified_type IDENTIFIER EQUAL initializer

iteration_statement:
  WHILE LEFT_PAREN condition RIGHT_PAREN statement_no_new_scope
  DO statement WHILE LEFT_PAREN expression RIGHT_PAREN SEMICOLON
  FOR LEFT_PAREN for_init_statement for_rest_statement RIGHT_PAREN statement_no_new_scope

for_init_statement:
  expression_statement
  declaration_statement

conditionopt:
  condition
  /* empty */

for_rest_statement:
  conditionopt SEMICOLON
  conditionopt SEMICOLON expression

jump_statement:
  CONTINUE SEMICOLON
  BREAK SEMICOLON
  RETURN SEMICOLON
  RETURN expression SEMICOLON
  DISCARD SEMICOLON // Fragment shader only.

// Grammar Note: No 'goto'. Gotos are not supported.

translation_unit:
  external_declaration
  translation_unit external_declaration

external_declaration:
  function_definition
  declaration

function_definition:
  function_prototype compound_statement_no_new_scope
```
