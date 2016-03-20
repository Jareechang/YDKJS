# This & Prototype Ch.3 notes

#### Syntax 

There are two forms of *Objects* in Javascript. 

1. Declarative (*literal*) 
``` javascript
var myObj = {
    key: value,
    //..
};
```
2. Constructed
``` javascript
var myObj = new Object();
myObj.key = value;
```
**NOTE:** The only difference really is that you can add one or more key/value
pairs to the key/value pairs to the literal delcaration.


#### Type

**Javascript has 6 primary types:**

1. String
2. Number
3. Boolean
4. Null
5. Undefined 
6. Object

**NOTE:** the `typeof null` which returns a string of "object" is a bug in javascript.
**null** itself is a primitive type.


**functions** - a sub-type of object ("callable" object). They are considered "first class" 
as they can be handled the same way as objects (with callable behaviour) 

**Arrays** - a form of object, with extra behaviours. Organization of data is more strucutured than 
regular objects.

#### Properties vs Methods

Technically, functions never belong to objects.

Everytime a property is accessed on an object, this is a **property accesss** (regardless of the types you get back,
including functions).

**Consider this example** 

``` javascript
function foo() {
    console.log("foo");
}

var someFoo = foo;

var myObject = {
    someFoo : foo
};

foo;        // function foo() {..}

someFoo;        // function foo() {..}

myObject.someFoo;        // function foo() {..}
```

All of the variable are indentical in that they hold the same reference to the `foo function`.

#### Duplicating Objects

One subset solution of the duplication, is that objects that are JSON-safe,
can be easily duplicated with: 

``` javascript
var newObj = JSON.parse( JSON.stringify( someObj ) )
```
**ES6 syntax** 

ES6 now has `Object.assign(...)` that takes an *target object* as first parameter, and 
one or more object as its source object. 
Then, it iterate over all the **enumerables**, owned keys (**immediately present**) 
on the source object(s) and copies them (via `=` assignment)

**NOTE:** `object.assign(..)` is purely `=` style assignment so any special charateristics
(like writable) on a source object is not **preserved** on the target object.

#### Property Descriptors

in ES5, the JS language gives distinction between the characteristics of properties,
such as whether the property was **read-only** or not. This was not the case in prior versions of 
javascript.

**consider this example**:
``` javascript
var myObject = {
    a:2
}

Object.getOwnPropertyDescriptor(myObject, "a");
// {
//      value: 2,
//      writable: true,
//      enumerable: true, 
//      configurable: true
// }
```
The above example shows the default values for the descriptor characteristics are when we 
create a normal property.

we can use `Object.defineProperty(..)` add a new property, or modify existing one (if its *configurable*)
with desired characteristics.

**example:**

``` javascript
var myObject = {};

Object.defineProperty(myObject, "a", {
    value: 2,
    writeable: true,
    configurable: true,
    enumerable: true
})

myObject.a; // 2
```

#### Writable 

The ability to change the value is controlled by the **writabl** property.

#### Configurable

If an Object is configurable, it allows object to be "configured" via 
*defineProperty()* 

**Note:** if property is already `configurable: false`, writable can always be changed from 
`true` to `false` without error but not `true` if already `false`.

Additionally, `configurable:false` also prevents `delete` operators on object properties.

#### Enumerable
This property controls whether or not a property will show up in a `for..in` loop

#### Immutability

1. Object Constants 
by combing `writable: false` and `configurable:false`, you can create a *constant* (that cannot be changed, or redefined or deleted)

2. Prevent Extensions
preventing new properties by being added to objects, we can use the built-in `Object.preventExtensions(..)`.

3. Seal 
`Object.seal(..)` creates a "sealed" object, in that it takes an object, call `Object.preventExtensions(..)` and 
marks `configurable:false`.
You can't add new values, but also cannot configure or delete existing properties (however, you may edit them).

4. Freeze
`Object.freeze(..)` creates frozen object, where it takes an existing object, calls `Object.seal(..)` and marks `writable: false`
so, its values cannot change.

This is the highest level of immutability that you can attain for an object itself.

#### [[Get]]

**Consider:**
``` javascript
var myObject = {
    a:2
}

myObject.a; // 2
```
When properties are accessed, the object performs a `[[Get]]` operation (much like a [[Get]]() ). 
The default built-in `[[Get]]` operation for an object, it first inspects the object for a property 
of the requested name, and it finds it, it will return it.

`[[Get]]` algorithm also defines another important behaviour when it does not find a property in the object.
Then it leads to traversal of the `[[prototype]]`.

#### [[Put]]

This is the setter for object (allows us to set or create property on the object).

**Overview of the [[Put]] algorithm:** 
1. Is there an accessor descriptor - **If so, call the setter**
2. Is the property a data descriptor with `writable:false` ? 
- non-strict mode: silently fail
- strict mode: throw `TypeError` 
3. Otherwise, set the value to existing property as normal

## Getters & Setters

When a property is defined to have either a getter or setter, its definition becomes an "accessor descriptor"
(as opposed to "data descriptor"). the `value` and `writable` characteristics of the descriptor are moot and ignored.
and instead it considers the `get` and `set`.

**For example** 

``` javascript

var myObject = {
    get a() {
        return 2;
    }
}

myObject.a = 3;

myObject.a; // 3
```

Normally, we would also want to have a `setter` in this case.

``` javascript
var myObject = {
    get a() {
        return this._a_;
    }
    set a(val) {
        this._a_ = val * 2;
    }
}

myObject.a = 2;

myObject.a; // 4
```

**NOTE:** In this case, we actually stored the **2** in another variable,
the `_a_` is a naming convention.

#### Existence

Javascript contain built-in functions to check whether a property in an object exists or not.


**consider the example:**
``` javascript

var myObject = {
    a: 2
}

("a" in myObject); // true
("b" in myObject); // false

myObject.hasOwnProperty("a") // true
myObject.hasOwnProperty("b") // false
```

**subtle difference between `in` and `Object.hasOwnProperty(..)`:**

1. in 
    - Check if property is in the object, but it also checks its prototype for the property.
    - Check is only existence for property name ( example: `4 in [2,4,6]; // false` )
2. Object.hasOwnProperty 
    - Checks to see if property exists in the **object in question**
    - Object.hasOwnProperty is accessible via *deletgation* through Object.prototype
    with `Object.create(null)`, it will fail. A way around it is to use `Object.prototype.hasOwnProperty(myObject,"a")`






