# Direct vs. Delegated event handlers in jQuery

---

> NOTE: the below suggestions should work on jQuery version 1.9.1 and above


There are two main types of event handlers in jQuery (at least I am aware of only these two types) – delegated and direct. Whenever I need to create an event handler in jQuery I always try to use a delegated one, and here’s why. Imagine you have the following html:

```html
<button id='add_icecream'>
    Add IceCream
</button>
<div id='box'>
    <p>IceCream!</p>
</div>
```
And you have a handler as shown below which creates new paragraph with “More IceCream” every time the button is clicked:

```javascript
$("#add_icecream").click(function() {
  let newElement = $('p').text('More IceCream!');
  $("#box").append(newElement);
});
```

Now, you have been given a task to make the dynamically generated paragraphs to turn red (change the text colour) when they clicked.

Note that to implement this you cannot just use `.click()` similar to the above code as `.click()` type of pattern does not cover dynamically generated elements. So if you just do this:
```javascript
$("#box > p").click(function() {
  $(this).css({"color": "red"});
});
```
Then the newly generated elements would require rebinding the handler i.e. the above handler simply will not work for them until you “re-start” it again.
> Note the usage of child selector (>) which selects only direct child elements.

To address the dynamically added elements we have to use `.on()` handler assignment pattern, which also has another advantage – it can be removed via `.off()`

Now there are delegated and direct event handlers. The direct event handler could be created like this:
```javascript
$("#box > p").on('click', function() {
  $(this).css({"color": "red"});
});
```
But it has two major disadvantages: similar to `.click()` it does not handle dynamically created elements and also it creates a handler per each paragraph element.

So the right approach is going to be the delegated handler:
```javascript
$("#box").on('click', 'p', function() {
  $(this).css({"color": "red"});
});
```
Here we delegate the event handling to the parent div element with “box” id. So whenever there’s a click on any child paragraph the event is going to be handled by the div element and so it will also handle all the dynamically added elements. And you if you’d like to handle clicks only from the direct children then you still can use the arrow selector like:
```javascript
$("#box").on('click', '> p', function() {
  $(this).css({"color": "red"});
});
```
