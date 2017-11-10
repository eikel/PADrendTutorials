<!------------------------------------------------------------------------------------------------
This work is licensed under the Creative Commons Attribution-ShareAlike 4.0 International License.
 To view a copy of this license, visit http://creativecommons.org/licenses/by-sa/4.0/.
 Author: Henrik Heine (hheine@mail.uni-paderborn.de)
 PADrend Version 1.0.0
------------------------------------------------------------------------------------------------->
<!---BEGINN_INDEXSECTION--->
<!---Automaticly generated section. Do not edit!!!--->
# Overview
* 3.2 C++
    * 3.2.1 [Creating a C++ Plugin](../../3_Development_Guide/2_C++/1_Creating_a_C++_Plugin/Creating_a_C++_Plugin.md)
    * 3.2.2 [Macros for EScript Bindings](../../3_Development_Guide/2_C++/2_Macros_for_EScript_Bindings.md)
    * 3.2.3 [Creating Object Bindings](../../3_Development_Guide/2_C++/3_Creating_Object_Bindings/Creating_Object_Bindings.md)
    * 3.2.4 [Extending MinSG States](../../3_Development_Guide/2_C++/4_Extending_MinSG_States/Extending_MinSG_States.md)
<!---END_INDEXSECTION--->

# Macros

## Function definition macros
In order to define a function, you have to use these macros. Each of them comes in two flavors: long and short

The short version just uses the long version, but it modifies the last argument:
`LastArg` is transformed to `{ return EScript::value(LastArg); }`

Therefore you use the short version if you just want to do a single statement and return that result, like: `new E_Obj()`. If you want to call a void function or if you want to return something else, you can use the comma operator: `(someFunction(), nullptr)` This will result in calling `someFunction`, but discarding any return value and return the second argument instead. Therefore this is basically a short version for this: `{ someFunction(); return EScript::value(nullptr); }`

The first argument of every function definition macro is the `typeObject`. This is an instance of `EScript::Type` describing your wrapper type. Therefore this objet is typically aquired through a call to `E_MyType::getTypeObject()`. The function will be added to this typeObject.


### Parameters
Each definition macro has two parameters `min` and `max`, describing the minimal and maximal parameter count allowed for this function. If you want to allow an arbitrary number of arguments, just pass `-1` as the maximal value. EScript will throw an exception if someone tries to call your function with an invalid amount of arguments.
You can access this arguments via the `parameter` variable. This is a variable of the type `ParameterValues` and it is basically a list or array to `ObjRef` instances.
The amount of given arguments can be accessed by `parameter.count()` and each argument by the index operator: `parameter[0]` ... `parameter[n-1]`

It is important to note, that the parameter array contains only `ObjRef` objects. Therefore you have to cast them to a concrete type in order to use them.

Convert an parameter to:
* a *basic* type (like `int`, `float`, `string`, etc.):
 * `int value = parameter[i].toInt()`
 * `float value = parameter[i].toFloat()`
 * etc.
* a pointer to an EScript type (or a wrapper type):
 * `E_TargetType* value = parameter[i].toType<E_TargetType>()`
 * e.g.: `E_Vec3* value = parameter[i].toType<E_Vec3>()`.
* any other type:
 * `TargetType value = parameter[i].to<TargetType>(rt)` (`rt` refers to the runtime)
 * e.g. `Vec3* value = parameter[i].to<Vec3*>(rt)`
 * or `Vec3 value = parameter[i].to<Vec3>(rt)`

> `parameter[i].to<TargetType>(rt)` will try to convert the wrapper objetc to the real object. Make sure to use the correct types, not every wrapper can be converted to a pointer, reference, etc.

If you want to test if a parameter is of a specific type, you have to convert it to a pointer type and check if it is not a `nullptr`. This can be done by using `toType<E_Type>()` or by using `to<Type*>(rt)`. Example:
```
Vec2* vec = parameter[0].to<Vec2*>(rt);
if(vec != nullptr) {
  // do something with this Vec2*
} else {
  rt.warn("Parameter must be a Vec2");
}
```


### Constructor
```
// short version
ES_CTOR(typeObject, min, max, returnExpr)
// long version
ES_CONSTRUCTOR(typeObject, min, max, {
  // ...
  return ...;
})
```
The constructor is a special function. It always must return an instance of the wrapper class. So typically you would just return something like:
* `EScript::create(new MyType());`
* or `new E_MyType()`


### Static class functions
```
// short version
ES_FUN(typeObject, functionName, min, max, returnExpr)
// long version
ES_FUNCTION(typeObject, functionName, min, max, {
  // ...
  return ...;
})
```
With this macro you can add functions directly to the type object. The parameters are similar to the constructor, but this time, you must also define the name of the function as a string. Example:
```
EScript::Type* typeObject = E_MyType::getTypeObject();
//! Vec2 getXZ(Vec3) this function will extract the x and z value of the given Vec3
ES_FUNCTION(typeObject, "getXZ", 1, 1, {
  Vec3 vec = parameter[0].to<Vec3>(rt);
  return EScript::create( new Vec2(vec.x(), vec.z()) );
})
```
You could use this function from EScript like this:
```
var value = MinSG.MyType.getXZ(new Geometry.Vec3(1,2,3));
outln(value); // Output: Vec2(1 3)
```

### Member functions
```
// short version
ES_MFUN(typeObject, targetType, functionName, min, max, returnExpr)
ES_MFUNCTION(typeObject, targetType, functionName, min, max, {
  // ...
  return ...;
})
```
A member function will have access to the underlying object. Therefore you have to specify the actual type of the *real* object. For the wrapper class `E_MyType` this will typically be just `MyType`.
In order to access the concrete object, you have to use the `thisObj` pointer. You can also use the wrapper instance with the `thisEObj` variable, which is an `EScript::ObjPtr`.
Example from the `E_Vec3` wrapper class:
```
//! Number|thisObj Vec3.x([value])
ES_MFUNCTION(typeObject, Vec3, "x", 0, 1, {
  if(parameter.count() == 1){
    thisObj->setX(parameter[0].to<float>(rt));
    return thisEObj;
  } else {
    return thisObj->getX();
  }
})
```

## Conversion macros
The conversion macros are used to convert between the wrapper class and your actual class back and forth.
### From a *wrapper* object to a *real* object
```
ES_CONV_EOBJ_TO_OBJ(eSourceType, targetType, expr)
```
This will convert a `eSourceType*` to the type specified by `targetType` using the expression given by `expr`. The `eSourceType` will always be a wrapper class, without any modifiers, like `E_Geometry::E_Vec3`. And the `targetType` will often be a pointer or reference to your actual type, like `Geometry::Vec3*`. For small classes like `Vec2` and `Vec3` a conversion to the type without modifiers is also useful, but it should be avoided for larger classes. A variable called `eObj` is a pointer to your `eSourceType` and can be used for your conversion.

Example:
```
// eObj is a pointer to an E_Geometry object, therefore we first dereference it:
// *eObj is now an E_Geometry object, which is a subclass of EScript::ReferenceObject.
// This class overrides the dereference operator, therefore **eObj will return the wrapped object.
ES_CONV_EOBJ_TO_OBJ(E_Geometry::E_Vec3, Geometry::Vec3&, **eObj)
```

### From a *real* object to a *wrapper* object
```
ES_CONV_OBJ_TO_EOBJ(sourceType, eTargetType, expr)
```
This will convert a `sourceType` to an `eTargetType*` by using the expression `expr`. The `sourceType` is your *real* object class with some optional modifiers, typically `*` or `&`. `eTargetType` is the name of your wrapper class without any modifiers. A variable called `obj` is available inside of your expression and it holds an object of the type given by `sourceType`.

Example:
```
ES_CONV_OBJ_TO_EOBJ(const Geometry::Vec3&, E_Geometry::E_Vec3, new E_Geometry::E_Vec3(obj))
```


