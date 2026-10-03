---
layout: default
title: A Small-C language definition for teaching compiler design
description: A subset of K&R C designed for an undergraduate compiler design course.
---

# A Small-C language definition for teaching compiler design

*By Egdares Futch H. · Originally published June 8, 2017 on [Medium](https://medium.com/@efutch/a-small-c-language-definition-for-teaching-compiler-design-b70198531a2f)*

The Small-C language definition presented here is a subset of the K&R second edition version of the C language, specifically designed for use in an undergraduate compiler design course.

Since this language definition is to be used as a teaching tool, it contains omissions, bugs and errors that are to be discovered by the student, in order to develop the skills to implement a high-level language compiler.

As an example, some of the changes that could (or should) be made to the grammar are:

1. Allow the declaration of more than one function in a source file (!).
2. Include the void type for procedure declarations.
3. Have one main function, that should be invoked when the program starts. This function should have void type.
4. Support recursion.
5. Add char and array types.

## Terminal symbols (tokens)

### Integer

A sequence of digits denoting an integer number in the range -32768..32767. This should be stored in a twos-complement representation. Could be expanded to support 32- or 64-bit integers.

### Identifier

A sequence of letters, digits and underscores that may only be initiated with underscores or letters, no longer than 16 characters.

### Reserved words

```
break continue else if int return while readint writeint
```

### Special characters

```
+ - * / % ! ? : = , < > ( ) { } || && ==
```

Comments should start with `/*` and end with `*/`, and can contain any legal character including newline. Comments should not be nested, nor go past the end of a source file.

Two adjacent terminal symbols should be separated by one or more comments, spaces or newlines, unless one of the other symbols is a special character. Comments, spaces and newlines have no syntactic value.

### Embedded I/O library

To avoid the need to implement a runtime library for I/O, I have included two reserved keywords for creating integer read and write functions, that can be generated directly from the object code module.

## Small-C syntax

This is the Small-C syntax, described in [EBNF](http://www.garshol.priv.no/download/text/bnf.html):

```
smallc_program  ::= type_specifier id '(' param_decl_list ')' compound_stmt
type_specifier  ::= int | char
param_decl_list ::= param_decl ( ',' param_decl )*
param_decl      ::= type_specifier id
compound_stmt   ::= '{' ( var_decl* stmt* )? '}'
var_decl        ::= type_specifier var_decl_list ';'
var_decl_list   ::= variable_id ( ',' variable_id )*
variable_id     ::= id ( '=' expr )?
stmt            ::= compound_stmt | cond_stmt | while_stmt
                  | break ';' | continue ';' | return expr ';'
                  | readint '(' id ')' ';'
                  | writeint '(' expr ')' ';'
cond_stmt       ::= if '(' expr ')' stmt ( else stmt )?
while_stmt      ::= while '(' expr ')' stmt
expr            ::= id '=' expr | condition
condition       ::= disjunction | disjunction '?' expr ':' condition
disjunction     ::= conjunction | disjunction '||' conjunction
conjunction     ::= comparison | conjunction '&&' comparison
comparison      ::= relation | relation '==' relation
relation        ::= sum | sum ( '<' | '>' ) sum
sum             ::= sum '+' term | sum '-' term | term
term            ::= term '*' factor | term '/' factor | term '%' factor | factor
factor          ::= '!' factor | '-' factor | primary
primary         ::= num | charconst | id | '(' expr ')'
```

## Additional rules

1. No identifier can be declared more than once in the same scope.
2. No identifier can be used without being declared first.
3. The source program should have a main procedure, without parameters.
4. The number and type of arguments in function calls should be the same as the declaration of the function.
5. A return statement should not return a value unless it is contained inside a function that was declared to return a value.
6. The expression in a return statement should be the same type as the return type of the function in which it is contained.
7. Reporting of lexical, syntactic and semantic errors.

## Phases to deliver

1. Lexical analyzer, built with a tool such as Flex, JLex, ANTLR.
2. Parser, built with a tool such as Bison, CUP, ANTLR.
3. Intermediate code generation (triples or quads, such as those suggested by the Dragon book).
4. Object code, usual target could be the [MIPS](http://www.cs.wisc.edu/~larus/SPIM/cod-appa.pdf) architecture, but x86 could also be used, for more adventurous spirits.

*Note: A previous version of this work was published in <http://maestros.unitec.edu/~efutch/small-c__english_version_.html> but updated here for better editing and publishing capabilities.*
