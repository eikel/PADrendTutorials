<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Henrik Heine (hheine@mail.uni-paderborn.de)
 PADrend Version 1.0.0
------------------------------------------------------------------------------------------------->
<!---BEGINN_INDEXSECTION--->
<!---Automaticly generated section. Do not edit!!!--->
# Overview
* 3.1.1.4 Operators
    * 3.1.1.4.1 [Operator Precedence](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/4_Operators/1_Operator_Precedence.md)
    * 3.1.1.4.2 [Operator Overloading](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/4_Operators/2_Operator_Overloading.md)
<!---END_INDEXSECTION--->

# Operator precendence

The following table summarizes the operator precendence from highest (0) to lowest (22).

| Precendence | Operator | Description | Associativity |
| ----- | ----- | ----- | ----- |
| 0 | `@` | | Right |
| 1 | `.`<br>`[ ]`<br>`( )` | Member access<br>Indexing<br>Function call | Left |
| 2 | `new` | Create new instance | Left |
| 3 | `fn` | Function definition | Right |
| 4 | `++`<br>`--`<br>(unary) `-`<br>`~`<br>`!` | Increment<br>Decrement<br>Unary minus<br>Bitwise not<br>Logical not | Right |
| 5 | `*` `/` | Multiplication and Division | Left |
| 6 | `%` | Modulo | Left |
| 7 | `+` `-` | Addition and Subtraction | Left |
| 8 | `<<` `>>` | Left and Right shift | Left |
| 9 | `<` `>` `<=` `>=` `~=` | Relational operators | Left |
| 10 | `==` `!=` `===` `!==` <br> <code>---&#124;></code> <br> `->` | Relational operators<br>Inheritance test<br>Object binder | Left |
| 11 | `=>` | Parameter binder | Left |
| 12 | `&` | Bitwise and | Left |
| 13 | `^` | Bitwise xor | Left |
| 14 | <code>&#124;</code> | Bitwise or | Left |
| 15 | `&&` | Logical and | Right |
| 16 | <code>&#124;&#124;</code> | Logical or | Right |
| 17 | `?:` | Ternary operator `a?b:c` | Right |
| 18 | `=` | Assignment | Right |
| 19 | `:=` | Attribute declaration | Right |
| 20 | `::=`<br><code>*= /= %= += -= &= ^= &#124;=</code> | Type attribute declaration<br>Compound assignments | Right |
| 21 | `...` | Varargs | Left |
| 22 | `,` | Comma | Left |


