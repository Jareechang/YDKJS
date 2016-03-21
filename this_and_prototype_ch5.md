## Ch 5: Prototypes

Objects in javascript have a internal property `[[prototype]]`.
it is simply a reference to another object. 

Almost all Objects are given `non-null` value for this property upon
creation.

As we examined the `[[Get]]` operation earlier, with a slight
mention of **prototypes** (where if a property does not exist in an object,
it continues the search in its **prototype**).

``` javascript

var anotherObject = {
    a:2 
}

// Creating an object linked to `anotherObject`
var myObject = Object.create(anotherObject);

myObject.a; // 2
```

**This look up in the `[[prototype]]` behaviour is also seen in:**

`for..in` and `in`

```
var anotherObject = {
    a:2 
}

var myObject = Object.create(anotherObject);

for (var k in myObject) {
    console.log("found: " +k);
}
// found: a

("a" in myObject) // true
```
This look up behaviour continues until a property is found or the chain ends.

#### Object.prototype

Where does `[[prototype]]`chain end? 

On top of every normal `[[prototype]]` chain is the built-in `Object.prototype` (includes
all common utilities used all over JS).

Examples: 
1. `.toString()`
2. `.valueOf(..)`
3. `.hasOwnProperty(..)`
4. `.isPrototypeOf(..)`


#### Setting & Shadowing properties

Going back to example:

``` javascript
myObject.foo = "bar";
```

if `foo` is not in the myObject, the `[[prototype]]` is traversed, much like the `[[Get]]`
operation. If it is not found anywhere along the chain, then it is then added to `myObject`.
However, if `foo` exists higher in the chain then it could create some problems.

If the property name `foo` ends up both in `myObject` itself and at higher level of the `[[prototype]]`
this is referred to as *shadowing* because `myObject.foo` lookup will always find the `foo` property thats 
lowest of in the chain. `myObject.foo` **shadows** any `foo` property which appears higher in the chain.

Now, back to the examination of case when assignment `myObject.foo = "bar"` and `foo` is not already on *myObject* 
but at higher level of `myObject`'s `[[prototype]]` chain:

1. if normal data accessor property named `foo` is found anywhere higher 
**and it is not marked as read-only (writeable: false)** then new property is added directly to `myObject`,
resulting in **shadow property**.

2. if property `foo` is marked **ready-only(writeable: false)** then **no shadowing occurs**.

3. if property `foo` is found higher in the chain, and its a setter, then the setter will always be called.
    - No `foo` will be added (aka, shadowed on) `myObject` nor will `foo` setter be redefined.

**NOTE:** shadowing should be avoided, as it leads to problems with delegation between them...

Shadowing can also occur implicitly:

``` javascript
var anotherObject = {
    a:2
}

var myObject = Object.create(anotherObject);

anotherObject.a; // 2
myObject.a // 2;

anotherObject.hasOwnProperty("a"); // true 
myObject.hasOwnProperty("a"); // false 

myObject.a++; // implicit shadowing

anotherObject.a; // 2
myObject.a; // 3

myObject.hasOwnProperty("a"); // true
```

behind the scenes, the `++` corresponds to `myObject.a = myObject + 1;`. The result is
`[[Get]]` look up for the values then `[[Put]]` assignment on the myObject.

#### "Class" 

in Javascript, classess can't describe what an objects do. The object defines its own 
behaviours directly. **there is just objects**.

#### "Class" functions

All functions by default by default get a public, non-enumerable property on them
called `prototype`, which points at an otherwise arbitrary object.

``` javascript
function Foo() {
    //...
}

Foo.prototype // {  }
```
This object called "Foo's prototype" because we access it via `Foo.prototype` property
reference.

Each object created from calling `new Foo()` will end up `[[prototype]]`-linked 
to this "Foo dot prototype" object.

**illustration:** 

``` javascript
function Foo() {
    //...
}

var a = new Foo();

Object.getPrototypeOf(a) === Foo.prototype // true
```

when `a` is created via `new Foo()`, one of things happening is that a gets a link to 
an internal `[[prototype]]` linked to an object that `Foo.prototype` is pointing at.

This is much different than classical object oriented languages where mutiple copies
("instances") of classes can be made. 
*Class - copy the behaviour plan from the class into physical object*.

In javascript, there are no such copy-action occuring. You don't create multiple instances of a class,
rather you create mutiple objects that `[[prototype]]` link to a common object. 
So, these objects don't end up quited **linked** than separated and disconnected.

**Conclusion**: `new Foo()` indirectly creates an new object that links to another object


## Review 

##### new vs Object.create(..)

**Note:** using the following as example in explanation

``` javascript
function Foo() {
    // ...
}

var a = new Foo();
```

**New** 

functions themselves are not constructors, when `new Foo()` is used, the **constructor** 
property is invoked. The `new Foo()` basically "hijacks" any normal function, calls it in a 
way that constructs an object.

Additionally, the new object created from the `new Foo()` keyword will be internally `[[prototype]]`-linked to 
the "Foo dot prototype" of the original function.

**Note:** this is more an accidental side effect of the `new` feature (where **a new object links to another object**).
        a more direct way is to use `Object.create(...)`

**Object.create(..)**

Creating proper links between object with no side-effects, see example :

``` javascript
function Foo(name) {
    this.name = name;
}

Foo.prototype.myName = function() {
    return this.name;
}

function Bar(name) {
    this.name = name;
}

// Create new Bar.prototype linked to `Foo.prototype````
Bar.prototype = Object.create(Foo.prototype);

```
Essentially, the example above, `Object.create(..)` creates a "new" object, and links
the new object's internal [[prototype]] to the object you specify (in our example, Foo.prototype).

##### linking Misconceptions

```
Bar.prototype = Foo.prototype;
```

Rather than linking to a new object, it directly links to the `Foo.prototype` object. 
While modifying `Bar.prototype`, you are also modifying `Foo.prototype` (the same object). 

```
Bar.prototype = new Foo();
```

This does create a new object, however, it uses `Foo(..)` "constructor call" (`.constructor`)
it comes with the side effect (state change, logging .. etc) of the function.


