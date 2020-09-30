# Bind, call and apply

---

All of these methods pass object/function which to be used instead of `this` object i.e. these functions allow attach specific value to `this`, the only difference between them is when a corresponding function which is given the value of `this` is invoked


## bind()
Allows to attach specific object/function to `this`, like below we attach `{name: "Dave"}` to `this`, so when `helloFunc` is called it will use `{name: "Dave"}` and not the default `name: "James"`:

```javascript
var person1 = {  
    name: "James",
    hello: function(thing) {
        console.log(this.name + " says hello " + thing);
    }
}

person1.hello("world");  // output: "James says hello world"
var helloFunc = person1.hello.bind({name: "Dave"});
helloFunc("Baku");  // output: "Dave says hello Baku"
```


## call()
Attaches object/function to `this` and executes the function immediately:

```javascript
var person2 = {  
    name: "Mike",
    hello: function(thing) {
        console.log(this.name + " says hello " + thing);
    }
}

person2.hello("world");  // output: "Mike says hello world"
person2.hello.call({ name: "Rob" }, "London"); // output: "Rob says hello London"
```

## apply()

Similar to `call()` except that it takes an array-like object

Also note how below we use reserved keyword inside function, it is available in each function and used to access undeclared parameters which get passed to the function, e.g. below `hello()` function accepts no params but they are passed so they could be accessed via `arguments` keyword:

```javascript
var person3 = {  
    name: "Colin",
    hello: function() {
        console.log(this.name + " says hello " + arguments[1]);
    }
}
var myList = ["sun", "mars", "jupiter"]
person3.hello.apply({name: "Imran"}, myList); // output: "Imran says hello mars"
```


You can also find that often `apply()` used with built in methods e.g. `Math.max()` which do not accept arrays as an argument:

```javascript
let maxValue1 = Math.max(1, 2, 3); // <-- does not accept an array
console.log(maxValue1); // output: "3"

// but if you need to use an array you can use it via apply()
let numbers = [5, 6, 2, 3, 7];
let maxValue2 = Math.max.apply(null, numbers);
console.log(maxValue2); // output: "7"
```

Note how we passed `null` instead of `this` here
