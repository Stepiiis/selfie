Copyright (c) the Selfie Project authors. All rights reserved. Please see the AUTHORS file for details. Use of this source code is governed by a BSD license that can be found in the LICENSE file.

Selfie is a project of the Computational Systems Group at the Department of Computer Sciences of the University of Salzburg in Austria. For further information and code please refer to:

selfie.cs.uni-salzburg.at

This is the grammar of the C Star (C\*) programming language.

C\* is a tiny subset of the programming language C. C\* features global variable declarations with optional initialization as well as procedures with parameters and local variables. C\* has five statements (assignment, while loop, if-then-else, procedure call, and return) and standard arithmetic (`+`, `-`, `*`, `/`, `%`) and comparison (`==`, `!=`, `<`, `>`, `<=`, `>=`) operators over variables and procedure calls as well as integer, character, and string literals. C\* includes the unary `*` operator for dereferencing pointers hence the name but excludes data types other than `uint64_t` and `uint64_t*`, bitwise and Boolean operators, and many other features. The C\* grammar is LL(1) with 7 keywords and 22 symbols. Whitespace as well as single-line (`//`) and multi-line (`/*` to `*/`) comments are ignored.

C\* Keywords: `uint64_t`, `void`, `sizeof`, `if`, `else`, `while`, `for`, `return`

C\* Symbols: `integer`, `character`, `string`, `identifier`, `,`, `;`, `(`, `)`, `{`, `}`, `+`, `-`, `*`, `/`, `%`, `=`, `==`, `!=`, `<`, `>`, `<=`, `>=`, `...`

with:

```
integer    = "0" | non_zero_digit { digit } .

character  = "'" printable_character "'" .

string     = """ { printable_character } """ .

identifier = letter { letter | digit | "_" } .
```

and:

```
non_zero_digit = "1" | ... | "9" .

digit  = "0" | non_zero_digit .

letter = "a" | ... | "z" | "A" | ... | "Z" .
```

C\* Grammar:

```
cstar      = { variable_init |  procedure } .

variable_init = variable ( [ initialize ] | "[" int_literal "]" ) ";" .

variable   = type identifier .

type       = "uint64_t" [ "*" ] .

initialize = "=" [ cast ] [ "-" ] value .

cast       = "(" type ")" .

value      = integer | character .

statement  = assignment | assignment ";" | if | while | for | call ";" | return ";" .

assignment = ( [ "*" ] identifier | "*" "(" expression ")" | identifier "[" expression "]" ) "=" expression .

expression = logical_or_expression .

logical_or_expression = logical_and_expression [ ( "||" ) logical_and_expression ]

logical_and_expression = bitwise_or_expression [ ( "&&" ) bitwise_or_expression ]

bitwise_or_expression = bitwise_and_expression [ ( "|" ) bitwise_and_expression ] .

bitwise_and_expression = equality_expression [ ( "&" ) equality_expression ] .

equality_expression = relation_expression [ ( "==" | "!=" )  relation_expression ] .

relation_expression =  shift_arithmetic [ ( "<" | ">" | "<=" | ">=" ) shift_arithmetic ] .

shift_arithmetic = arithmetic { ( "<<" | ">>" ) arithmetic } .

arithmetic = term { ( "+" | "-" ) term } .

term       = factor { ( "*" | "/" | "%" ) factor } .

factor     = [ cast ] [ "-" ] [ "*" ] [ "~" ] [ "!" ]
             ( "sizeof" "(" type ")" | literal | identifier [ "[" expression "]" ] | call | "(" expression ")" ) .

int_literal = ["0x"] integer .

literal    = value | string | int_literal .

if         = "if" "(" expression ")"
               ( statement | "{" { statement } "}" )
             [ "else"
               ( statement | "{" { statement } "}" ) ] .

while      = "while" "(" expression ")"
               ( statement | "{" { statement } "}" ) .

for        = "for" "(" statement ";" expression ";" statement ")" 
               ( statement | "{" { statement } "}" ) .

procedure  = ( type | "void" ) identifier "(" [ variable { "," variable } [ "," "..." ] ] ")"
             ( ";" | "{" { variable ";" } { statement } "}" ) .

call       = identifier "(" [ expression { "," expression } ] ")" .

return     = "return" [ expression ] .
```