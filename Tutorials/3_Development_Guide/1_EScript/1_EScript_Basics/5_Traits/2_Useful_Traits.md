<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Henrik Heine (hheine@mail.uni-paderborn.de)
 PADrend Version 1.0.0
------------------------------------------------------------------------------------------------->
<!---BEGINN_INDEXSECTION--->
<!---Automaticly generated section. Do not edit!!!--->
# Overview
* 3.1.1.5 Traits
    * 3.1.1.5.1 [Traits](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/5_Traits/1_Traits.md)
    * 3.1.1.5.2 [Useful Traits](../../../../3_Development_Guide/1_EScript/1_EScript_Basics/5_Traits/2_Useful_Traits.md)
<!---END_INDEXSECTION--->

# Useful Traits
EScript already offers some useful traits that can be used in order to simplify your code.

## `Traits.DefaultComparisonOperatorsTrait`
This trait with its super long name will automatically implement all comparison operators (`<`, `>`, `<=`, `>=`, `==`, `!=`). You only have to provide the `<` function and all other operators are redirected to it. This function will be added as a private attribute of the type and it has therefore access to the `this` object.
This trait can be only added to `Type` objects.

> Attention: This trait will change the behavior of == and !=

```
var MyType = new Type;
MyType.value := void;
MyType._constructor ::= fn(Number v) {
  this.value = v;
};
Traits.addTrait(MyType, DefaultComparisonOperatorsTrait), fn(other) {
  return this.value < other.value;
});

var a = new MyType(0);
var b = new MyType(42);
var c = new MyType(42);
outln(a < b); // true
outln(a > b); // false
outln(a == b); // false
outln(b == c); // true
```

## `Traits.CallableTrait`
This trait marks a type or object as a callable object. Whenever you create a type that overrides the call operator, you should add this trait to it. For example functions and `FnBinder` objects have this trait. With this trait it is possible to easily test if an object can be called:
```
var fun = fn(callable) {
  // This will throw an exception if the trait is missing
  Traits.requireTrait(callable, Traits.CallableTrait);
  // do something with it
  return callable(42);
};
```

You can optionally add a function as an additional parameter to the `addTrait` call. This function will be set as the `_call` function of the given object. If the parameter is omitted, `_call` is set to `void`. Therefore you should either add a function as a parameter or you should first call `addTrait` and define the `_call` function afterwards:
```
var MyType = new Type;
Traits.addTrait(MyType, Traits.CallableTrait, fn() {
  // do something here
});
```
or:
```
var MyType = new Type;
Traits.addTrait(MyType, Traits.CallableTrait);
MyType._call = fn() {
  // do something here
};
```


