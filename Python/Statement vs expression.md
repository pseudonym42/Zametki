# Statement vs expression

---


The difference in Python is that expression evaluates to a value like:

```python
a = print(1)
```

Statement though does not return a value so you cannot write smth like this in Python 2 (as print is not a function in python but a statement), so below will raise SyntaxError in Python 2:

```python
a = print 1
```

