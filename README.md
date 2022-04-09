# React-18

## startTransition

```js
// Update input value
setInputValue(input);

// Update the searched value and search results
setSearchQuery(input);
```

this feature, allows to developer to indicate to React, which actions may cause `lag` on the screen, you might have been using `debounce`.

To understand this, let's consider a component with a simple search field, it might have two functions, one will be to update the actual value of the input variable, so that the user sees what he is typing in real time, the second function would be to control what results are being returned from the search field, so in this case when the user is typing he would expect to see the results of his search query at a relatively good performance level, however in this case it is clear that function1 has higher priority than function2, because user expects what he's typing to be there before the search results to be there. it us okay if the search results are delayed for a few seconds. but the input has to be typed in live.

So in this case we can wrap `setSearchQuery` in `startTransition` allowing it to handle it as non-urgent and to be interrupted if more urgent updates like clicks or key presses come in. 

If a transition gets updated by the user for example by typing multiple characters in a single row, react will throw out the previous state which wasn't finished and render only the latest update => so, what that means in this case is if that the user starts typing and if the user now is some several steps ahead, but `startTransition` might have an older value, then it will throw out that older value, and then work from the latest value which it gets, `transitions` also let you keep most of the interactions snappy, even if they lead to significant UI changes, they also let you avoid wasting time rendering content that's no longer relevant by throwing out the old steel content and only working with the new one.

react also gives us a new `useTranstion` api that we can use to show a loader while the transition is pending, so in this case while the `setSearchQuery` function is running we can show a spinner, because that transition is still pending, and after this `setSearchQuery` function runs completely that spinner would be replaced with actual data.



## batching

batching is something that the developer generally doesn't have to worry about, but it's good to know what's happening inside of react behind the scenes, so to understand batching let's have a look at this code below: 

```js
import { useState } from 'react';

export default funciton App() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);
  
  function fetchSomething = async () => {
     return await new Promise((resolve) => setTimeout(resolve));
  }
  
  function handleClick() {
    // React 17 and earlier does not batch these because
  
    setCount(c => c + 1); // causes re-render
    setFlag(!flag); // causes re-render
  }
  
  function renderClick() {
    fetchSomething().then(() => {
      setCount(c => c + 1);
      setFlag(!flag);
    })
  }
}
```

what react does, it will call `setState` inside of any other function, it will actually batch those `setState` calls those together, and the call them at once, so it doesn't cause multiple renders, and all of your data is applied in one render, this is good for performance.

And if you don't want to batch, you can use `flushSync` api.
