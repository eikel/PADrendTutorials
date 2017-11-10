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

# Traits
Traits are a concept of object oriented programming. A trait represents a set of methods and/or attributes, that can be used to extend a class. Traits are one possible solution for the problem that arises if an object should provide a mix of different properties. For example if a class should inherit methods from two different classes, but the underlying language doesn't support multiple inheritance.
A trait usually adds methods to an already existing object.
In EScript it is possible to add properties afterwards, via the `:=` and `::=` operators. For example one could simple add additional functions to already existing types:
```
Geometry.Vec3.xz ::= fn() { return new Geometry.Vec2(this.x(), this.z()); };
// and now one could do the following:
var vec3d = new Geometry.Vec3(10, 20, 30);
var vec2d = vec3d.xz();
outln(vec2d); // Output: Vec2(10 30)
```
Adding such members manually could easily end up in a mess of spaghetti code. Therefore EScript provides a much more comfortable way of handling such things: `Traits`

## Trait example
Imagine we already have a trait called `TestTrait` with a method called `print`. Then we could add this trait to any EScript object by simply doing this:
```
Traits.addTrait(someObject, TestTrait);
someObject.print(); // call to the print function of the TestTrait
```
Furthermore it is possible to check if a given object has a specific trait: `Traits.queryTrait(someObject, TestTrait)`

## Trait definition
Each trait is an instance of the `Std.Trait` class. In most cases it is desirable to use the `Traits.GenericTrait` class instead of its base class, because the `GenericTrait` allows you to actually add new attributes, including properties like `@(private)`, to an object.

First of all you have to create a new instance:
```
var TestTrait = new Traits.GenericTrait;
```
then you can add new attributes:
```
// you must add your attributes to the attributes member variable of your trait
TestTrait.attributes.print ::= fn() {
  outln("print was called on ", this); // this will refer to the actual object
};
```
The `attributes` member is an actual `Type` object, therefore you can use the `:=` operator as well as the `::=` operator. If you want to execute some code when the trait is added to an object, you can add a function to the `onInit` function:
```
// onInit is a MultiProcedure, therefore you add another function to it, don't override it!
TestTrait.onInit += fn(obj) {
  outln("Trait is added to ", obj);
  // Here you can modify the object as you like:
  obj._secret @(private) := 42;
};
```
You could also restrict the type this trait can be added to, by simply adding a type restriction to the first parameter ob the `onInit` function. Furthermore you could also add additional arguments to this function, but make sure that you call the `addTrait` with those additional parameters:
```
// Now with an additional parameter and type restriction
TestTrait.onInit += fn(MyType obj, secret) {
  // An exception is thrown if someone adds this trait to a wrong type
  outln("Trait is added to ", obj);
  obj._secret @(private) := secret;
};
```

### Multiple Uses
By default a trait can only be added once to an object. If one tries to add a trait twice, an exception is thrown. In most cases this behavior is desired, but sometimes it is useful to add a trait multiple times to the same object. In order to do so, one must explicitly allow this behavior:
```
var MyTrait = new Traits.GenericTrait;
MyTrait.allowMultipleUses();
```

### Removal
By default it is not possible to remove a trait and an exception is thrown if one tries to remove it. In order to allow removal it is necessary to explicitly allow this behavior:
```
var MyTrait = new Traits.GenericTrait;
MyTrait.onRemove := fn() {
  // implement some logic here
};
MyTrait.allowRemoval();
```
It is necessary to implement an `onRemove` function. If this function is missing, an exception is thrown on removal.
Now it is possible to remove this trait with: `Traits.removeTrait(obj, MyTrait)`
This will call the `onRemove` function and it will afterwards remove the trait from the internal list of traits for the given object. It is also possible to add additional arguments to the `removeTrait` function. These arguments will be forwarded to the `onRemove` function:
```
var MyTrait = new Traits.GenericTrait;
MyTrait.onRemove := fn(a, b, c) {
  // implement some logic here
};
MyTrait.allowRemoval();
// ...
Traits.removeTrait(obj, MyTrait, 1, 2, 3);
```
Unfortunately it is not possible to completely remove an attribute from an object. Therefore it is a good idea to set those attributes to `void`.


## Traits API
Short overview of the full Traits API

* `Traits.addTrait(obj, Trait trait, params...)`
 * adds the given `trait` to `obj` and passes all extra parameters to the `onInit` function
* `Traits.addTraitByName(obj, String traitName, params...)`
 * Add a trait to the given object. The trait is identified by its name. The trait's name must correspond to the EScript attribute structure beginning with GLOBALS. (e.g. `"Std.Traits.SingletonTrait"`)
* `Traits.assureTrait(obj, trait)`
 * Adds the given trait if it has not already been added.
* `Traits.getTraitByName(String name)`
 * Finds the trait identified by its name. The traits name must correspond to the EScript attribute structure beginning with GLOBALS. (e.g. `"Std.Traits.SingletonTrait"`)
* `Traits.queryLocalTrait(obj, traitOrTraitName)`
 * Checks if the given object has a trait stored locally (and not by inheritance).
* `Traits.queryTrait(obj, traitOrTraitName)`
 * Checks if the given object has a trait (the trait may be inherited).
* `Traits.queryTraits(obj)`
 * Collects all traits of an object (including inherited traits).
* `Traits.removeTrait(obj, Trait trait, params...)`
 * Remove a trait from the given object. If the trait is not designed for removal, an exception is thrown.
* `Traits.requireTrait(obj, traitOrTraitName)`
 * Throws an exception if the given object does not have the given trait.


