# Pass - by reference or by value?

---

Arguments in Python are passed by assignment:

* If you pass a mutable object into a function, the function gets a reference to that same object and you can mutate it, but if you rebind the reference in the function, the outer scope will know nothing about it, and after you're done, the outer reference will still point at the original object


* If you pass an immutable object to a function, you can't rebind the outer reference, and you can't even mutate the object.

Look at the examples below


---

### Mutable types params

Let's try to modify a list that was passed to a function:
```python
def change_list(inner_list):
    print('got', inner_list)
    inner_list.append(4)
    print('changed to', inner_list)

outer_list = [1, 2, 3]

print('before, outer_list =', outer_list)
change_list(outer_list)
print('after, outer_list =', outer_list)
```

Output you will:
```
before, outer_list = [1, 2, 3]
got [1, 2, 3]
changed to [1, 2, 3, 4]
after, outer_list = [1, 2, 3, 4]
```

Since the parameter passed in is a reference to outer_list, not a copy of it, we can use the mutating list methods to change it and have the changes reflected in the outer scope.

Now let's see what happens when we try to change the reference that was passed in as a parameter:
```python
def try_to_change_list_reference(the_list):
    print('got', the_list)
    the_list = [5, 6, 7]
    print('set to', the_list)

outer_list = [1, 2, 3]

print('before, outer_list =', outer_list)
try_to_change_list_reference(outer_list)
print('after, outer_list =', outer_list)
```
Output:
```
before, outer_list = [1, 2, 3]
got [1, 2, 3]
set to [5, 6, 7]
after, outer_list = [1, 2, 3]
```
Since the `the_list` parameter was passed by value, assigning a new list to it had no effect that the code outside the function could see. The `the_list` was a copy of the `outer_list` reference, and we had `the_list` point to a new list, but there was no way to change where `outer_list` pointed.

---

### Immutable types params

Let's try to do the same with for example strings. First let's try to change the reference:

```python
def try_to_change_string_reference(the_string):
    print('got', the_string)
    the_string = 'fake''
    print('set to', the_string)

outer_string = 'original'

print('before, outer_string =', outer_string)
try_to_change_string_reference(outer_string)
print('after, outer_string =', outer_string)
```
Output:

```
before, outer_string = original
got original
set to fake
after, outer_string = original
```
Again, since the `the_string` parameter was passed by value, assigning a new string to it had no effect that the code outside the function could see. The `the_string` was a copy of the `outer_string` reference, and we had `the_string` point to a new string, but there was no way to change where `outer_string` pointed.
