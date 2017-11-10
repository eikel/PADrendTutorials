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

# EScript modules
Modules are arbitrary objects identified by an unique id, which is typically a path (e.g. 'Std/MultiProcedure'). A single module is normally defined in a single file.
The module loader loads the file only if it is requested by some script. Furthermore the module loader will load each module only once and it will return always the same instance of that module.

The concept of modules is used to structure the code and to load a file only if it is really needed.
Furthermore it prevents namespace pollution.

## Usage
Modules are always loaded by the module loader, which is defined in the 'Std' namespace: `Std.module`
A module is loaded by simply using the module loader as a function with the ID of the target module:
```
var MultiProcedure = Std.module('Std/MultiProcedure');
```
By default the module loader will search in the `PADrend` root folder and all its subdirectories. It is advised to place your modules in a subdirectory of the `extPlugins` folder. For example in an extra directory: `PADrend/extPlugins/MyModules/`

If an EScript file is loaded by a module loader itself, the loader is injected as a static variable called `module`. Therefore you can use `module('...')` instead of `Std.module('...')`.
Furthermore the module loader supports relative module ids.

## Defining own modules
A already said, a module is an arbitrary object, which is defined and returned by a single file.
A very simple module could look like this:
```
// PADrend/extPlugins/MyModules/IntWrapper.escript
var IntWrapper = new Type;
IntWrapper.value @(private) ::= 0;
IntWrapper._constructor ::= fn(v = 0) {
  value = v;
}
IntWrapper.setValue ::= fn(Number v) {
  value = v;
};
IntWrapper.getValue ::= fn() {
  return value;
}
Mod.print ::= fn() {
  outln(value);
};
// a module file MUST return the module object
return IntWrapper;
```
Now you can load and use this module from anywhere via: `Std.module('MyModules/IntWrapper')`.
For example:
```
var IntWrapper = Std.module('MyModules/IntWrapper');
var int = new IntWrapper(10);
int.print(); // Output: 10
int.setValue(42);
int.print(); // Output: 42
```

## Callback
Loading a module can also trigger a callback function.
```
Std.module.on('MyModules/IntWrapper', fn(IntWrapper) {
  outln("IntWrapper was loaded!");
});
```
If the specified module is already loaded, the given function is executed immediately with the module as a parameter. Otherwise the function is added to a list for that module. If the module is later on loaded, all stored callbacks are called.
> ATTENTION: If the module is not loaded, the callback will never be called!

This feature is useful to add optional extensions. For example you could add extra functionality for some other module. If that other module is not loaded, there is no need to add your extra functions.
Example:
```
module.on('MyModules/SomeGUI', fn(gui) {
  gui.addMenu("Do Something");
});
```


