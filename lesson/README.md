# `useEffect`

Hooks are functions that let you "hook into" React state and lifecycle features from functional components. Hooks don't work inside classes - they're a way to use React features without classes.

We've learned about `useState`, which is a hook for adding state to functional components. There are a few more built-in hooks for accessing React's internals, and you can also create your own hooks. Today we'll be learning about `useEffect`.

`useEffect` is a hook that lets you perform side effects in function components. "Side effects" are things that affect the outside world or come from the outside world, like changing the DOM or fetching data from an API or setting timers.

### Set Up And Run A New React App

1. Open the terminal to the `lesson` directory--the simplest way to do so is to right-click on the `lesson` folder in VS Code and select "Open in Integrated Terminal". If you have only the folder open in VS Code, you can also use the keyboard shortcut Ctrl+` (backtick) to open the terminal.

2. In the terminal, type `npm create vite .` and hit enter/return. The `.` is important--this will create a new Vite project in the current directory.

3. It will warn you that there are files here currently. Use the arrow keys and Enter/Return to select "Ignore files and continue". This allows us to keep our readme and any data/assets files we have in our new project folder.

4. Choose React and then JavaScript from the following menus, using arrow keys and Enter/Return.

5. Install dependencies by entering `npm install` in the terminal.

6. Run the app by typing `npm run dev` in the terminal. This will provide a clickable link to open the app in your default browser, or you can navigate to the localhost URL in your browser.

### A Basic Side Effect Use Case: Timers

Let's look at an example of a side effect: setting a timer, using JavaScript's built-in `setInterval` function.

```jsx
import { useState, useEffect } from "react";

function Timer() {
  const [numSeconds, setNumSeconds] = useState(0);

  useEffect(() => {
    setInterval(() => {
      setNumSeconds((numSeconds) => numSeconds + 1);
    }, 1000);
  }, []);

  return <div>{numSeconds} seconds have passed</div>;
}

export default Timer;
```

Import this component into your `App.jsx` file and render it. You should see a timer that increments every second.

`useEffect` takes two arguments: a callback function, and, optionally but usually used, an array of dependencies. The callback function (in this case a function that begins a timer to go off every second) is the function that `useEffect` calls, and the dependencies array tells React how often to call our callback function. With an empty array, our callback is called when the function first runs, but _not_ on re-render. This is truly necessary in this case-- since our call to `setNumSeconds` changes state, and our component relies on that state, we re-render every second. If we didn't include the empty dependencies array, then React would call our callback function again,creating _another_ timer. And we'd get a new timer every single second!

### Cleaning Up

Since side effects are outside of React's system, it doesn't have a built-in way to handle them. Which makes sense--you don't want to build code into React for managing timers, cancelling network or database calls, working with DOM elements _without_ the Virtual DOM, and so on. React gives us powerful tools to weave our side effects into our interface, but we have to take responsibility for them if we want an effective app.

One of the major aspects we often have to deal with when performing a side effect is cleaning up after an ongoing effect. The callback in `useEffect` can also, optionally, return a function to be called when it's time to take the component off the DOM. React calls our returned function whenever the component is taken off the DOM, or "unmounted", which usually happens when the user navigates to a new part of our app or takes an action that removes a component from a list of components (think a todo list deletion).

In the case of our timer, if the user navigates to another part of our app, we want to stop the timer from firing off every second. JavaScript's `setInterval` function returns an ID we can pass to the built-in `clearInterval` function to cancel the timer, and we can return a function from `useEffect` to utilize `clearInterval` to do so.

```jsx
import { useState, useEffect } from "react";

function Timer() {
  const [numSeconds, setNumSeconds] = useState(0);
  useEffect(() => {
    const intervalId = setInterval(() => {
      setNumSeconds((numSeconds) => numSeconds + 1);
    }, 1000);

    return () => clearInterval(intervalId);
  }, []);

  return <div>{numSeconds} seconds have passed</div>;
}
```

### Dependencies

The second argument to `useEffect` is an array of dependencies. This array tells React when to call the callback function. If the array is empty, the callback is called once when the component mounts, and then never again. If the array contains values, the callback is called when any of those values change.

Here is an example of a useEffect Hook that is dependent on a variable. If the count variable updates, the effect will run again:

```jsx
import { useState, useEffect } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  const [calculation, setCalculation] = useState(0);

  useEffect(() => {
    setCalculation(() => count * 2);
  }, [count]); // <- add the count variable here

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount((c) => c + 1)}>+</button>
      <p>Calculation: {calculation}</p>
    </div>
  );
}

export default Counter;
```

Import this component into your `App.jsx` file and render it in place of the Timer component. You should see a counter that doubles the count every time you click the button.

Looking back at our Timer example, we could expand it, giving the user the ability to start and stop the timer. To do this, we will use the fact that when an effect is called, React runs the returned cleanup function from the previous callback.

Switch `App.jsx` back to the Timer component and add a button to start and stop the timer:

```jsx
import { useState, useEffect } from "react";

function Timer() {
  const [numSeconds, setNumSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(true);

  useEffect(() => {
    if (isRunning) {
      const intervalId = setInterval(() => {
        setNumSeconds((numSeconds) => numSeconds + 1);
      }, 1000);

      return () => clearInterval(intervalId);
    }
  }, [isRunning]);

  return (
    <div>
      {numSeconds} seconds have passed
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? "Stop" : "Start"}
      </button>
    </div>
  );
}
```

Whenever `isRunning` changes, the `useEffect` callback will run again. If `isRunning` is `true`, the timer will start. If `isRunning` is `false`, no new timer is started. Either way, the cleanup function from the previous callback will run, clearing the old timer.

Let's look at a more complex example. We'll use `useEffect` to fetch data from an API. We'll use the `jsonplaceholder` API, which is a fake online REST API for testing and prototyping. We'll fetch a list of posts and display them in our app.

### Fetching Data with `useEffect`

##### Create Posts

Create a new component in a file called `Posts.jsx` in the `src` folder.

```jsx
function Posts() {
  return (
    <div className="App">
      <h1>Posts</h1>
    </div>
  );
}

export default Posts;
```

Don't forget to export the function, and then import this component into `App.jsx` and render it in place of the Timer component.

**You should at this point have the word "Posts" rendered in your browser.**

##### Create Post

- In the **/src/** folder, create a new file named `Post.jsx`.
- Create a function called `Post` that returns a `<div>` inside of parentheses. Don’t forget to export the function at the bottom of the file.

```jsx
function Post(props) {
  return <div className="post">A post.</div>;
}

export default Post;
```

##### Render Props Into Post

Delete the dummy text and prepare to render the props passed into the `Post` component.

```jsx
function Post(props) {
  return (
    <div className="post">
      <h2>{props.title}</h2>
      <p>{props.body}</p>
    </div>
  );
}

export default Post;
```

##### Set Up State for Posts

```jsx
import { useState } from "react";

function Posts() {
  const [posts, setPosts] = useState([]);

  return (
    <div className="App">
      <h1>Posts</h1>
    </div>
  );
}

export default Posts;
```

##### Fetch Data with `useEffect` and `async/await`:

- Import `useEffect` from React.
- Use `useEffect` to fetch data from the JSON Placeholder API when the component mounts and update the `posts` state.

**Note:** The callback function passed to `useEffect` cannot be async, so we'll define an async function inside the effect and call it.

```jsx
import { useState, useEffect } from "react";
import Post from "./Post";

function App() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    async function fetchPosts () {
      const response = await fetch("https://jsonplaceholder.typicode.com/posts");
      const data = await response.json();
      setPosts(data);
    };

    fetchPosts();
  }, []);

  return (
    <div className="App container-fluid">
      <h1>Posts</h1>
      <div className="posts"></div>
    </div>
  );
}

export default App;
```

##### Render Posts

Map over the `posts` state and render a `Post` component for each post.

```jsx
import { useState, useEffect } from "react";
import Post from "./Post";

function App() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    async function fetchPosts () {
      const response = await fetch("https://jsonplaceholder.typicode.com/posts");
      const data = await response.json();
      setPosts(data);
    };

    fetchPosts();
  }, []);

  return (
    <div className="App container-fluid">
      <h1>Posts</h1>
      <div className="posts"></div>
    </div>
  );
}

export default App;
```

##### Render Posts

Map over the `posts` state and render a `Post` component for each post.

```jsx
import { useState, useEffect } from "react";
import Post from "./Post";

function App() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    async function fetchPosts () {
      const response = await fetch("https://jsonplaceholder.typicode.com/posts");
      const data = await response.json();
      setPosts(data);
    };

    fetchPosts();
  }, []);

  return (
    <div className="App container-fluid">
      <h1>Posts</h1>
      <div className="posts">
        {posts.map((post) => (
          <Post key={post.id} title={post.title} body={post.body} />
        ))}
      </div>
    </div>
  );
}

export default App;
```

##### Style Your Components:

Add some basic CSS to **/src/App.css** to style your components.

```css
.App {
  text-align: center;
}

.posts {
  display: flex;
  flex-wrap: wrap;
}

.post {
  border: 1px solid #ccc;
  padding: 10px;
  margin: 10px;
  width: 30%;
  box-shadow: 2px 2px 8px #aaa;
}
```

##### Check Your Browser

Save all your files and check your browser. You should see a list of posts fetched from the JSON Placeholder API.

##### Congrats! You're done.

You've successfully used `useEffect` with `async/await` to fetch data from an API and display it in your React app.
