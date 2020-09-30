# What is the difference between JS primitive and object types?

---

Objects are "collections" of properties. A property can reference an object or a primitive.

Primitives are values, they have no properties.

In JS there are 5 primitive types:
* undefined
* null
* boolean
* string
* number

Everything else is an object. The primitive types `boolean`, `string` and `number` can be wrapped by their object counterparts. These objects are instances of the `Boolean`, `String` and `Number`
constructors respectively

### `boolean` vs `Boolean`

you can create a primitive using either of the ways below:

```javascript
let boolPrim1 = true;
let boolPrim2 = Boolean(true);
console.log(typeof boolPrim1); // "boolean"
console.log(typeof boolPrim2); // "boolean"
```

but if you want to create a `Boolean` object you need to use keyword `new`

```javascript
let boolObj1 = new Boolean(true);
console.log(typeof boolObj1); // "object"
```

Now `boolObj1` is actually an object so you can assign an arbitrary value to some arbitrary property:
```javascript
boolObj1.arbitraryProperty1 = "randomData";
```

the above is obviously not possible with primitive values as primitives can't hold properties,

However, assigning a property to a primitive doesn't produce an error because of auto-boxing:
```javascript
boolPrim2.arbitraryProperty1 = "randomData";
console.log(boolPrim2.arbitraryProperty1); // "undefined"
```

Auto-boxing (i.e. automatic type casting) happens when assigning a property, e.g. this:
```javascript
boolPrim2.arbitraryProperty1 = "randomData";
```

is actually interpreted as this:
```javascript
(new Boolean(boolPrim2)).arbitraryProperty1 = 'randomData';
```

Thus JS engine creates a wrapper object and then assigns a property to it and then immediately discards the object, so noting actually happens to our `boolPrim2` primitive


There's one gotcha though in creating objects via `new Boolean()` - an object will always evaluate to `true` as in JS objects always evaluate to `true` in __boolean contexts__ - even if the wrapped value is `false`. See the below example.

```javascript
// note that boolean value assigned false if you do not provide true explicitly in brackets
let boolObj2 = new Boolean();
console.log(boolObj2); // "{Boolean: false}"
```

And here's  __boolean contexts__, if you try to evaluate the above object you will get `true`:

```javascript
if (boolObj2) {
    // we should not get here, but above evaluated to true
    console.log("Evaluated to true");
    // if you console.log the object though - then console is grabbing the real value:
    console.log(boolObj2); // false
}
```

So when handling booleans it is recommended to either use primitives or when `new Boolean()` used then use built-in method `valueOf`, which will grab the real value, this is what console.log was doing in the above example automatically under the hood, e.g.:

```javascript
if (!boolObj2.valueOf()) {
    console.log("boolObj2 is false"); // This is now correct!
}
```

Some more info about boolean's behaviour in JS.

1. These 2 are equivalent:
```javascript
let boolObj3 = Object(Boolean(true));
let boolObj3 = new Boolean(true);
```

2. Using `Boolean` without keyword `new` often used for casting any primitive or object to a primitive `boolean`, note that the same can be done using `!!`:
```javascript
console.log(Boolean(undefined)); // same as !!undefined -- returns false
console.log(Boolean(""));        // same as !!"" -- returns false
console.log(Boolean(null));      // same as !!null -- returns false
console.log(Boolean(123));       // same as !!123 -- returns true
console.log(Boolean("123"));     // same as !!"123" -- returns true
console.log(Boolean(NaN));       // same as !!NaN -- returns false
console.log(Boolean(new String('hh'))); // same as !!new String('hh') -- returns true
```

3. Even though `new Boolean(x)` always evaluates to `true` independent of `x` value, it DOES evaluate correctly i.e. to the value `x` if used in comparison, because in comparisons JS uses `.valueOf()` which always returns the real value, e.g.:
```javascript
let boolObj4 = new Boolean();
if (boolObj4) console.log("Should not but does evaluates to true");
// below evaluates correctly to false as JS engine uses .valueOf()
// during runtime, note that the below log message should not appear
if (boolObj4 == true) console.log("I should not be logged");
```






### `String()` and `Number()` objects

Now if we consider `String()` and `Number()` objects they behave in the similar way to what we described above,

```javascript
console.log(typeof "abc");                          //"string"
console.log(typeof String("abc"));                  //"string"
console.log(typeof new String("abc"));              //"object"
console.log(typeof new String("abc").valueOf());    //"string"
```

Earlier we mentioned auto-boxing and how JS is casting a primitive to an object under the hood to assign a property. Similar auto-boxing happens when you try to access some methods defined in objects but via a primitive rather than an object, e.g. let's add some arbitrary method to `Number` prototype.

Note that it is considered a bad practice and not recommended, it is done here for demo:
```javascript
Number.prototype.myArbitraryMethod = function() {
    return typeof this;
}
let numPrim1 = 123;
```
If you try to use that method with primitive then JS will __not__ throw an error, rather it will cast the `numPrim1` on the fly to a `Number` object, here `myArbitraryMethod` returns type of caller, which is already an object as JS casted primitive `numPrim1` to object:

```javascript
console.log((numPrim1).myArbitraryMethod()); // "object"
```

Thus primitives have access to all the properties (including methods) defined by their respective object constructors
