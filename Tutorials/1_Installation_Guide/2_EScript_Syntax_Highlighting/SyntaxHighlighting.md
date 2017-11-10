<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Henrik Heine (hheine@mail.uni-paderborn.de)
 PADrend Version 1.0.0
------------------------------------------------------------------------------------------------->
<!---BEGINN_INDEXSECTION--->
<!---Automaticly generated section. Do not edit!!!--->
# Overview
* 1 Installation Guide
    * 1.1 [Installation Guide](../../1_Installation_Guide/1_Installation_Guide.md)
    * 1.2 [SyntaxHighlighting](../../1_Installation_Guide/2_EScript_Syntax_Highlighting/SyntaxHighlighting.md)
<!---END_INDEXSECTION--->

# Syntax Highlighting for EScript
EScripts syntax is very similar to JavaScript, therefore you could just set the language of your favorite editor to JavaScript, but in order to highlight all keywords, it might be better to use a special EScript highlighting.

For some editors we already have a corresponding highlighting file.
## Notepad++
The syntax is defined in [EScriptNPP.xml](EScriptNPP.xml).
In order to install it you have to click on `Language` -> `Define your language`. A new window will pop up and there you have to click on `import`. This will open an open file dialog and here you have to select the `EScriptNPP.xml` file.

## Atom
Simply install the `language-escript` package under `Packages` -> `Settings View` -> `Install Packages/Themes`.

## Code Blocks
The syntax is defined in [lexer_escript.xml](lexer_escript.xml). To enable it, copy the file to `???\CodeBlocks\share\CodeBlocks\lexers`, where `???` is your Code Blocks installation folder.

## Other Editors
Many editors allow to define an own language. Therefore you can use the following keywords and other settings. After each block the color and font used in the Notepad++ highlighting is shown.

### Keywords
Actual keywords of the language, that cannot be used as variable names.
```
for foreach do while continue break as exit
try catch throw
if else
fn function return lambda yield
var static global new this thisFn
namespace getIterator _constructor _get _set super
true false void null
```
* Color: #950055
* Style: Bold

### Predefined types/variables
```
GLOBALS Array ArrayIterator
Collection Delegate Exception ExtObject Function Iterator Map MapIterator NumberRef Parser Pointer ReferenceObject Runtime Script IO Math Rand
Bool Number Object String Type Void Identifier
```
* Color: #0080C0
* Style: Bold

### Predefined functions
```
assert chr clock getDate getOS getRuntime load loadOnce out outln parse parseJSON print_r system time toJSON _callFunction _parseJSON _toJSON toBool toNumber toString
```
* Color: #0000FF
* Style: Bold

### Attribute modifier
Modifiers that are used inside of an attribute description, like `T.x @(private) := 42;`
```
private init const override type
```

### Macros
```
__FILE__ __DIR__ __LINE__
```
* Color: #808080
* Style: Bold

### Operators
```
- ! % & ( ) * , . : ; ? [ ] ^ | ~ + < = > @
```
* Color: #0000FF
* Style: Normal

### Strings
Strings are surrounded by either `"` or by `'` and can be escaped by `\` like: `"stringA\n"` or `'stringB\n'`
* Color: #FF0000
* Style: Normal

### Comments
Single line comments start with `//`. Multiline comments start with `/*` and end with `*/`.
* Color: #008000
* Style: Normal

### Identifiers
Names that start with an `$` sign are special `Identifier` literals.
* Color: #800080
* Style: Bold


