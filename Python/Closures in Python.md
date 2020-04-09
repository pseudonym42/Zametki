# Closures in Python

---

Closure is a function. A function that has a nested function which gets returned.

That nested function has access (obviously) to the outer function's scope.

Closures are predominantly used in functional languages, In Python they are normally used
to create decorators.

Even though closures supposed to "hide" some data from user, so a user could not amend the data
but could only read it, in Python it is possible to amend it. See the example below:

All function objects (if it is a closure function object) have a `__closure__` attribute, which returns a tuple of cell objects:

```Python
def outer():
    data = ['x', 'y']

    def inner():
        print(data)

    return inner

outer()() # this prints ['x', 'y']

# you can access data list as follows:
closure = outer()

print(outer.__closure__) # this prints None
print(closure.__closure__) # this prints (<cell at 0x...: list object at 0x...>,)
print(closure.__closure__[0].cell_contents) # this prints ['x', 'y']

# now you can amend it like:
closure.__closure__[0].cell_contents.append('z')
closure() # this prints ['x', 'y', 'z']


```
