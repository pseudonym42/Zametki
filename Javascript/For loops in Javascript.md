# For loops in Javascript

---

There three main types of `for` loops in Javascript:
* classic (1):
```javascript
    for (let i; i < n; i++) {
        ...
    }
```
* for ... **in** (2):
```javascript
    for (let i in obj) {
        ...
    }
```
* for ... **of** (3):
```javascript
    for (let i of obj) {
        ...
    }
```
Let's discuss (2) and (3).

Interesting gotcha of (2) is that it iterates in arbitrary order. Also it only iterates over object properties that are enumerable i.e. when particular property enumerable option is set to true. For example:
```javascript
let zzz = { myKey: 'val' }
```
Now to check if enumerable property is true you can try this:
```javascript
Object.getOwnPropertyDescriptor(zzz, 'myKey').enumerable
```
The (3) iterates over values not properties like loop (2). Also unlike (2) it requires the iterable object ti implement iterator interface which is implemented by property `[Symbol.iterator]` (similar to Python). This property returns an iterator so (3) can iterate over it via `.next()`<sup>1</sup>. Any object that contains a `[Symbol.iterator]` method is an iterable object for e.g. Array, Set, String etc. Note that `Object` objects do not have default iteration behavior as they do not have property `[Symbol.iterator]` so cannot be iterated over with (3) unless corresponding property `[Symbol.iterator]` added manually.

**So in short**: use (2) to iterate over `Object` objects. Do **NOT** use (2) to iterate over arrays as it is doing that in random order as noted above. Use (3) to iterate over arrays.

---

1. Also note that unlike in Python in Javascript `.next()` returns not a value but the following:
```javascript
    {
        value: some_value,
        done: true|false
    }
``````

&NewLine;
&NewLine;
