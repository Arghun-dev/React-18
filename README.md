# React-18

## startTransition

```js
// Update input value
setInputValue(input);

// Update the searched value and search results
setSearchQuery(input);
```

this feature, allows to developer to indicate to React, which actions may cause `lag` on the screen, you might have been using `debounce`.

To understand this, let's consider a component with a simple search field, it might have two functions, one will be to update the actual value of the input variable, so that the user sees what he is typing in real time, the second function would be to control what results are being returned from the search field, so in this case when the user is typing he would expect to see the results of his search query at a relatively good performance level, however in this case it is clear that function1 has higher priority than function2, because user expects what he's typing to be there before the search results to be there. it us okay if the search results are delayed for a few seconds. but
