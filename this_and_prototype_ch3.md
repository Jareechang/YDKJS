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
