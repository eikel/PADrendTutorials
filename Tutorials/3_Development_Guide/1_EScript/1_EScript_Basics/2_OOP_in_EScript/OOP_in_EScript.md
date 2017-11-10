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

# EScript OOP
One of EScript's many similarities to JavaScript is the handling of types. EScript is not a class based language, but instead a prototype based language. The easiest way to get started with user defined objects is by using the `ExtObject`. You can create a new instance of an `ExtObject` and define new attributes for it. You can afterwards access and modify the attributes as you like.
```
var car = new ExtObject;
car.color := "red"; // := creates a new member
car.speed := 190;
car.outputDesc := fn() {
  out("This is a ", this.color , " car ");
  out("with top speed ", this.speed , ".\n");
};
car.speed = 185;
car.outputDesc();
```
As you can see object attributes are defined with this syntax: `object.attribute := value;`. You can change the value of an attribute afterwards by using the normal assignment syntax: `object.attribute = value;`.
If there is already an attribute with this name defined, EScript won't give a warning. If on the other hand an attribute is accessed that was not defined earlier, EScript will give warning:
```
car.acceleration = 0; // Warning: Attribute not found 'acceleration'
```

## Types and inheritance
As already mentioned, EScript is not a class based language, but a prototype based one. But it is possible to achieve something similar to a class, by instantiating a new `Type`:
```
var Shape = new Type;
Shape.color := "white"; // Define object attribute with value "white"

// New type that is derived from Shape
var Polygon = new Type(Shape);
Polygon.numVertices := 3;

// New type that is derived from Shape
var Circle = new Type(Shape);
Circle.radius := 0;

// instantiate a new Circle
var circle = new Circle;
circle.color = "red";
circle.radius = 5;
```
Here one must be careful, because every attribute of a new instance will be set to the specified value. If the value is a value type, like a number or a string, the value is copied. But if it is a reference type, every instance will point to the same object!
```
Polygon.vertices := [];
var a = new Polygon;
a.vertices[0] = 42;
var b = new Polygon;
outln(b.vertices[0]); // Output: 42
```
This code will output the value `42`, because both instances are refering to the same array!
To overcome this issue you can provide attributes with annotations: `object.attribute @(<list of annotations) := value;`. In this case we look at the `init` annotation:
```
Polygon.vertices @(init) := Array;
var a = new Polygon;
a.vertices[0] = 42;
var b = new Polygon;
outln(b.vertices[0]); // Output: void
```
If you instanciate a new Polygon, the `vertices` attribute will be now set to a new array.

Other useful annotations are the `private` and `const` annotations. A *private* attribute can be only accessed by the `this` object. The value of a *const* attribute cannot be changed afterwards.

Another annotation is used to denote that a function is overiding the parent function:
```
var Parent = new Type;
Parent.print ::= fn() {
  outln("I'm the parent!");
};
var Child = new Type(Parent);
Child.print @(override) ::= fn() {
  outln("I'm the child!");
};
```
You can overide a function without using this annotation, but it is recommended to use this annotation, because EScript will give a warning if there is no function to override:
```
// Warning: Attribute marked with @(override) does not override.
Child.println @(override) ::= fn() {
  outln("I'm the child!");  
};
```
As you can see, this annotation will give you a hint if you accidentally try to override a function that does not exist in the parent.


### Static attributes
You can define static attributes by using `::=` instead of `:=`. In many cases you can define your object functions statically, because they are the same for all instances.
```
var T = new Type;
T.value := 42;
T.print ::= fn() {
  outln("value=" + this.value);
};
var a = new T;
a.value = 10;
a.print(); // Output: value=10
T.print(); // Output: value=42
```
Because EScript is prototype based, the `this` object of the *Type* object will point to the type itself and therefore `T.print()` will print out the value of the prototype.

### Constructor
It is also possible to define an own constructor. This is done by implementing an attribute with the special name `_constructor`. If a new instance of the type is instanciated with the `new` keyword, this attribute is called:
```
var Animal = new Type;
Animal.name := void;
Animal._constructor ::= fn(n){
	name = n;
};
Animal.print ::= fn(){
	out("My name is ", name, "\n");
};
var a = new Animal("Pubert");
a.print();
```
Before the body of a constructor is executed, the constructor of the super type is executed. If you want to give some parameters to the super constructor, you have to use the following syntax:
`Type._constructor ::= fn(constructorParams) @(super(constructorParams)) { ... }`
```
var Animal = new Type;
Animal.name := void;
Animal.canFly := void;
Animal._constructor ::= fn(n, f){
	name = n;
	canFly = f;
	out("Animal created\n");
};
Animal.print ::= fn(){
	out("My name is ", name, "and I can ", (canFly ? "fly" : "not fly"), ".\n");
};

var Dragon = new Type(Animal);
Dragon._constructor ::= fn(n) @(super(n + " the Great", true)){ // explicit call to super constructor
	out("Dragon created\n");
};

var a = new Dragon("Pubert");
a.print();
```


