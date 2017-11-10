<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Henrik Heine (hheine@mail.uni-paderborn.de)
 PADrend Version 1.0.0
------------------------------------------------------------------------------------------------->
<!---BEGINN_INDEXSECTION--->
<!---Automaticly generated section. Do not edit!!!--->
# Overview
* 3.1.1 EScript Basics
    * 3.1.1.1 [Basic Language Features](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/1_Basic_Language_Features/Basic_Language_Features.md)
    * 3.1.1.2 [OOP in EScript](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/2_OOP_in_EScript/OOP_in_EScript.md)
    * 3.1.1.3 [Modules in EScript](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/3_Modules_in_EScript/Modules_in_EScript.md)
    * 3.1.1.4 Operators
    * 3.1.1.5 Traits
<!---END_INDEXSECTION--->

# EScript
EScript is an object-oriented scripting language for controlling C++ applications. It is compiled and executed by a virtual machine.

## Syntax
The syntax is similar to C and JavaScript, therefore statements always ends with a semicolon and there are two kinds of comments. A single line comment starts with `//` and a multiline comment is enclosed with `/*` and `*/`.

## Data Types and Operators
### Primitive Types
EScript supports the following primitive `call-by-value` types:
* `Number` representing double precision numbers. Number literals must be either integrals (decimal, hexadecimal or binary) or floating point numbers. Example: `42 27.4 0x1a -2.7e+10 0b101010`
* `String` must be surrounded by either `"` or by `'`. Example `"test"` and `'test'`. Strings can contain the typical escape sequences and can contain newline characters.
* `Bool` Either `true` or `false`
* `Identifier` immutable name starting with a dollar sign, which is often used for constants: `$example`
* `Void` The `void` value is similar to the null value of many high level languages.

In an expression `false` and `void` are the only values that are evaluated as false. Every other value (also the numeric value `0`) is evaluated as true.
```javascript
outln(false || false); // Output: false
outln(false || void); // Output: false
outln(false || 0); // Output: true
outln(false || ""); // Output: true
```

### Some Operators
```javascript
outln( 2+40 ); // Output: 42
outln( 2*21 ); // Output: 42
outln( "4" + "2" ); // Output: 42
outln( "foo"+"bar" ); // Output: foobar
outln( "wup " * (6/2) ); // Output: wup wup wup
outln( 1>2 ); // Output: false
outln( !true ); // Output: false
outln( true & true ); // Output: true
outln( false || true ); // Output: true
outln( "foo" == "bar" ); // Output: false
outln( "foo" != "bar" ); // Output: true
```
Strings are automatically converted to numbers, if they are ussed in a binary expression and the left operand is a number:
```javascript
outln( 12 + "3" ); // Output: 15
outln( 10 * "10" ); // Output: 100
outln( 10 == "10" ); // Output: true
outln( 10 == "10.0" ); // Output: true
```
If the left operand is a string, the right operand is automatically converted to a string:
```javascript
outln( "12" + 3 ); // Output: 123
outln( "10" == 10 ); // Output: true
outln( "10.0" == 10 ); // Output: false
```
EScript supports many different operators. The precedence is given by the table in [OperatorPrecedence.md](OperatorPrecedence.md)
### Equality
EScript supports different equality operators. One with automatic conversion (`==`) and one without(`===`).
```javascript
outln( 10 == "10" ); // Output: true
outln( 10 === "10" ); // Output: false
outln( 10 === 10 ); // Output: true
outln( true == "foo" ); // Output: true
outln( true === "true" ); // Output: false
outln( "true" == true ); // Output: true
outln( "true" === true ); // Output: false
```

### Calling functions
Functions can be called just like in C or JavaScript:
```javascript
// call global function 'load':
load("someScript.escript");
// call function 'saveTextFile' in namespace 'IO':
IO.saveTextFile( "foo.txt" , "bar" );
// call global method 'outln'
outln(a, b, c, 42, "test");
```

### Math functions
Many functions that are typically present in a math namespace are directly defined for all numbers. For example instead of calling `sin` or `sqrt` with a number as a parameter, you call the function on the number object itself:
```javascript
outln(9.sqrt()); // Output: 3
outln((3.141).sin()); // Output: 0.000592654
```

### Local variables
Local variables are declared with the `var` keyword followed by the name of the variable:
```javascript
var foo; // empty variable named foo (containing void)
var x = 500 - 80 / 2; // variable x containing a number
// The variable "message" will be of type String
var message = "Please click the button";
// Dynamically change the type to Number
message = 5;
```
One important difference to C is, that local variables are valid in the tighest enclosing block, but *excluding* functions defined inside the block. So they are also valid *before* they are declared. Local variables and parameters are allocated on the stack for every call of the surrounding function.
```javascript
{
	outln(x); // Output: void
	var x = 5;
	outln(x); // Output: 5
  var fun = fn() {
    outln(x); // warning: Variable not found 'x'
  };
}
```
The following script will throw a warning: `Variable not found: 'x'`
```javascript
{
	outln(x);
}
```


### Static variables
The scope of static variables is the tighest enclosing block, *including* functions defined inside the block. Static variables are allocated *once* for all calls of the enclosing function.
```
static factorial = fn( Number n ) {
  return (n == 0) ? 1 : factorial (n - 1) * n; // factorial is visible, because it is static
};
out( factorial( 5 ) ); // Output: 120
```

## Built-in collection types
EScript already contains two different collection types (Array and Map), both of them are able to change their size dynamically.

Arrays are collections with numbers as keys and arbitrary objects as values. They can be created by using the array syntax or by instantiating a new array object:
```javascript
var numbers = [3, 23, 7, 3, 100, 1, 35];
var colors = ["red", "green", "blue"];
var array = new Array();
array[10] = 42;
outln(numbers[4]); // Output: 100
outln(numbers[10]); // Output: void
outln(numbers.count()); // Output: 7
outln(number.empty()); // Output: false
```

Maps are basically associative arrays with arbitrary keys (except `void`) and arbitrary values. EScript also supports a map literal, using curly braces. If you want to create an empty map, you must instantiate a new map, because the literal won't work. This is due to the fact that empty curly braces are recognized as empty blocks and not as empty maps.
```javascript
var fruits = {
  "lemon" : "yellow",
  "cherry" : "red"
};
fruits["apple"] = "green";
var map = new Map();
map[42] = "some Value";
```

## Control structures
EScript supports the typical set of control structures.
### if/else
```
var result = someFunction ();
if(result) {
  out("Success");
} else {
  out("Failure");
}
var num = calculateSomething ();
if(num < 0)
  out("Too small");
else if(num >= 0 && num <= 100)
  out("Range okay");
else
  out("Too large");
```
### ternary operator ? :
```
var abs = value >=0 ? value : -value;
```
### while and do-while
```
var i = 0;
while(i<10) {
  out(i++, " ");
} // Output: 0 1 2 3 4 5 6 7 8 9
var j = 0;
do {
	out(j++, " ");
} while(j < 10); // Output: 0 1 2 3 4 5 6 7 8 9
```
### Looping with for
```
var sum = 0;
for(var i = 0; i < 100; ++i) {
  sum += i;
}
outln("Sum of numbers: ", sum);
```
### Collection iteration with foreach
The general syntax of the foreach loop is: `foreach(<container> as <key>, <value>) <statement>` or if you're only interested in the values: `foreach(<container> as <value>) <statement>`
```
var chars = [’a’, ’c’, ’k’, ’b’, ’d’, ’x’, ’j’];
foreach(chars as var index, var currentChar) {
  if(currentChar === ’x’) {
    outln("Character ’x’ found at index ", index);
    break;
  }
}
foreach(chars as var currentChar) {
  if(currentChar === ’x’) {
    outln("Character ’x’ was found");
    break;
  }
}
```
### Exception handling
Exceptions can be caught by using a try-catch statement:
```
try {
  outln(42/0); // will throw a division by zero exception
} catch(e) {
  outln(e);
}
```
You can throw any object as an exception like this: `throw 42;` or `throw new Exception();`

But in order to get a full stacktrace you should throw an exception by calling the `Runtime.exception(msg)` function:
```
Runtime.exception("some message");
```

## Functions
One important difference to C is, that in EScript functions are first class objects and that they don't have a name. Functions are defined with the `fn` keyword and because a function is just another object, the definition is a statement and therefore it must end with a semicolon.
```
var square = fn(num) {
  return num * num;
}; // don't forget this semicolon!
var a = square(5);
outln( a ); // Outputs: 25
```

### Recursion
Remember local variables are only visible inside the surrounding scope, but *excluding* nested functions. Therefore the following code wouldn't work, because the `factorial` variable is not visible inside the function.
```
var factorial = fn(Number x){
	if(x>1)
		return x*factorial(x-1); // Warning: Variable 'factorial' not found
	return x;
};
out( factorial(4) );
```
To overcome this issue you must define the factorial variable static or you can use the special `thisFn` keyword, which is a reference to the current function:
```
var factorial = fn(Number x){
	if(x>1)
		return x*thisFn(x-1);
	return x;
};
out( factorial(4) ); // Output: 24
```

### Type restrictions
The parameters of a function can be restricted to specific types. An exception is thrown if the type does not match.
```
var square = fn(Number num) {
  return num * num;
};
square(5); // ok
square("foo"); // runtime error
```
It is also possible to allow multiple types for a single parameter. Furthermore it is important to know, that void is an independent type!
```
var print = fn([Number, String] value) {
  outln(value);
};
print(42); // ok
print("foo"); // ok
print(void); // runtime error
var print2 = fn([Number, String, void] value) {
  outln(value);
};
print(void); // ok
```
### Default values
Parameters can have default values. If a type restriction is specified, the default value must also be of that type!
```
var print = fn(value = "no value") {
  outln(value);
};
print("hello"); // Output: hello
print(); // Output: no value
var print2 = fn(String value = void) {
  outln(value);
};
print2(); // runtime error
```
### Varargs
The last parameter of a function definition can be used for a variable argument list. The function can then accept arbitrary many values that are stored as an array in the last parameter.
```
var sum = fn( numbers... ) { // all arguments are automatically passed as an array
  var sum = 0;
  foreach( numbers as var n)
    sum += n;
  return sum;
};
outln( sum( 10,100,1000,4 ) ); // Output: 1114
```
### Multiple return values
EScript does not support multiple return values directly, but it has some syntactic sugar to automatically assign multiple values to the content of an array: `[ lValue* ] = Array`
```
var calc = fn() {
  return [17, 42]; // returns an array
};
[var a, var b] = calc();
outln("a:", a, " b:", b); // Output: a:17 b:42
```
### Parameter binding
You can set the first parameters of a function to fixed values by using a so called function binder (*FnBinder*): `Array => Function`
The result of this expression is a FnBinder object that is used as a wrapper around the function. You can use this object like a normal function, but it will always call the underlying function with the first parameters fixed:
```
var myFun = fn(a, b, c) {
  out("a:", a, "b:", b, " c:", c );
};
myFun(1, 2, 3); // Output: a:1 b:2 c:3
// create a wrapper that always passes 100 and 200 as the first two parameters
var myBoundFun = [ 100, 200 ] => myFun;
myBoundFun( 300 ); // Output: a:100 b:200 c:300
```

Furthermore the *FnBinder* is used to combine an object with a function: `Object -> Function`
The result of this expression is again a FnBinder object. The bound object will be the `this` object of the function.
```
var print = fn() {
	outln(this);
};
print(); // Output: void
var boundedPrint = 42 -> print;
boundedPrint(); // Output: 42
```


