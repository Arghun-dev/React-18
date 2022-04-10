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


## Suspense

**Streaming HTML and Selective Hydration**

There are 2 major SSR features in React 18 unlocked by Suspense:

Streaming HTML basically means that user can make the page interactable beforea all of the data is loaded in, so for example with server side rendering nowadays, the client has to fetch all of the HTML, after fetching the HTML, the client then loads all of the JavaScript code from the server, and then hydrates the entire app, what `hydration` basically does is it maps all of the virtual dom elements with all of the real dom elements, which are present in the html, however react makes it faster now via `suspense`. 

Now the API for suspense works like this:

```js
<Layout>
  <Navbar />
  <Sidebar />
  <RightPane>
    <Post />
    <Suspense fallback={<Spinner />}>
      <Comments />
    </Suspense>
  </RightPane>
</Layout>
```
  
You define a part of your components as children for suspense component and you define a fallback.

Now, for Comments, when the page is initially loaded in, the Suspense component will display the `Spinner`, since the data has yet to be loaded in, now after the HTML is loaded in, react will stream the new html and replace the spinner with the comments component, so this solves part of the problem, where you don't have to fetch all of the data before you fetch anything, now for the second part where all of the page has to be hydrated at the same time, Suspense now allows selective hydration, and it can remove certain parts of the page from the hydration and allow them hydrate separately, so in the case of this comments component, since this is inside of Suspense, all of the rest of the page can be hydrated by react, and when the new component gets loaded in, react will hydrate it separately, this is known as selective hydration, react also handles the order of hydrating the components in the page, so the user does not have to worry about things hydrating out of order and destroying the user experience. so this gives a benefit of having a more interactable page and having it load faster rather than having to wait for everything to load in.
