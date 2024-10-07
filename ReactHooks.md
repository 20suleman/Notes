# Categories of React Hooks

The video divides React hooks into 8 major categories:

1. **State Management Hooks** (e.g., `useState`, `useReducer`)
2. **Effect Hooks** (e.g., `useEffect`, `useLayoutEffect`, `useInsertionEffect`)
3. **Ref Hooks** (e.g., `useRef`, `useImperativeHandle`)
4. **Performance Hooks** (e.g., `useMemo`, `useCallback`)
5. **Context Hooks** (e.g., `useContext`)
6. **Transition Hooks** (e.g., `useTransition`, `useDeferredValue`)
7. **Random Hooks** (e.g., `useDebugValue`, `useId`)

## 1. State Management Hooks

### 1.1. `useState`

Manages local state in functional components.

#### Example 1: Capturing User Input

```jsx
import { useState } from "react";

function UserForm() {
  const [username, setUsername] = useState("");

  return (
    <div>
      <input
        type="text"
        value={username}
        onChange={(e) => setUsername(e.target.value)}
        placeholder="Enter your name"
      />
      <p>Username: {username}</p>
    </div>
  );
}
```

**Explanation:** The useState hook stores the value of the input field. As the user types, the onChange handler updates the state, which updates the paragraph below.

#### Example 2: Toggle Visibility (Boolean State)

```jsx
import { useState } from "react";

function ToggleComponent() {
  const [isVisible, setIsVisible] = useState(false);

  return (
    <div>
      <button onClick={() => setIsVisible(!isVisible)}>
        {isVisible ? "Hide" : "Show"} Component
      </button>
      {isVisible && <p>This is a toggleable component!</p>}
    </div>
  );
}
```

**Explanation:** The button toggles a boolean state, showing or hiding the paragraph. Commonly used for toggling UI elements.

### 1.2 `useReducer`

Manages more complex state logic or multiple related state variables.

#### Example: Managing Multiple Input Fields

```jsx
import { useReducer } from "react";

function formReducer(state, action) {
  switch (action.type) {
    case "SET_NAME":
      return { ...state, name: action.payload };
    case "SET_EMAIL":
      return { ...state, email: action.payload };
    default:
      return state;
  }
}

function UserForm() {
  const [state, dispatch] = useReducer(formReducer, { name: "", email: "" });

  return (
    <form>
      <input
        type="text"
        value={state.name}
        onChange={(e) =>
          dispatch({ type: "SET_NAME", payload: e.target.value })
        }
        placeholder="Name"
      />
      <input
        type="email"
        value={state.email}
        onChange={(e) =>
          dispatch({ type: "SET_EMAIL", payload: e.target.value })
        }
        placeholder="Email"
      />
      <p>Name: {state.name}</p>
      <p>Email: {state.email}</p>
    </form>
  );
}
```

**Explanation:** useReducer manages form state by handling multiple related state variables (name and email) with actions dispatched to update each field.

## 2. Side Effect Hooks

### 2.1. `useEffect`

Performs side effects like API calls, timers, and DOM mutations in function components.

#### Example 1: Updating Document Title

```jsx
import { useState, useEffect } from "react";

function TitleUpdater() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]); // runs the effect only when `count` changes

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

**_Explanation:_** The document title is updated every time the count changes.

#### Example 2: Fetching Data on Component Mount

```jsx
import { useState, useEffect } from "react";

function DataFetcher() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchData() {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/posts/1"
      );
      const result = await response.json();
      setData(result);
      setLoading(false);
    }
    fetchData();
  }, []); // runs the effect only once on mount

  if (loading) {
    return <p>Loading...</p>;
  }

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.body}</p>
    </div>
  );
}
```

**Explanation:** useEffect is used to fetch data when the component mounts (runs only once). It’s a common pattern for data fetching in functional components.

## 3. Reference Hooks

### 3.1. `useRef`

Stores a mutable value that does not trigger re-renders.

#### Example: Managing a Timer

```jsx
import { useState, useRef } from "react";

function Timer() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef();

  const startTimer = () => {
    intervalRef.current = setInterval(() => {
      setSeconds((prevSeconds) => prevSeconds + 1);
    }, 1000);
  };

  const stopTimer = () => {
    clearInterval(intervalRef.current);
  };

  return (
    <div>
      <p>{seconds} seconds passed</p>
      <button onClick={startTimer}>Start Timer</button>
      <button onClick={stopTimer}>Stop Timer</button>
    </div>
  );
}
```

**Explanation:** useRef is used to store the interval ID without causing re-renders, allowing the timer to start and stop.

## 4. Performance Hooks

### 4.1. `useMemo`

Memoizes the result of an expensive computation, recomputing only when its dependencies change.

#### Example: Expensive Computation

```jsx
import { useMemo, useState } from "react";

function ExpensiveCalculation({ numbers }) {
  const [value, setValue] = useState(0);

  const total = useMemo(() => {
    return numbers.reduce((acc, num) => acc + num, 0);
  }, [numbers]); // Recalculates only when `numbers` array changes

  return (
    <div>
      <p>Total: {total}</p>
      <input
        type="number"
        value={value}
        onChange={(e) => setValue(parseInt(e.target.value, 10))}
      />
    </div>
  );
}
```

**Explanation:** useMemo optimizes performance by preventing unnecessary recalculations of expensive operations unless the dependencies (in this case, numbers) change.

### 4.2. `useCallback`

Memoizes a function, preventing it from being recreated on each render.

#### Example: Prevent Unnecessary Re-renders

```jsx
import { useCallback, useState } from "react";

function Button({ onClick }) {
  console.log("Button rendered");
  return <button onClick={onClick}>Click me</button>;
}

function ParentComponent() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount((prevCount) => prevCount + 1);
  }, []); // Function is only created once

  return (
    <div>
      <p>Count: {count}</p>
      <Button onClick={increment} />
    </div>
  );
}
```

**Explanation:** useCallback prevents the creation of a new function on each render, which can improve performance byreducing unnecessary re-renders.
`or`
_useCallback prevents the increment function from being recreated on every render, which reduces unnecessary re-renders when passing callbacks as props._

## 5. Context Hook

### 5.1. `useContext`

Consumes a context value within a component.

#### Example: Accessing Theme Context

```jsx
import { useContext, createContext } from "react";

const ThemeContext = createContext("light");

function ThemedComponent() {
  const theme = useContext(ThemeContext);

  return <p>The current theme is {theme}</p>;
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedComponent />
    </ThemeContext.Provider>
  );
}
```

**Explanation:** useContext accesses the current value of ThemeContext without manually passing props. Useful for global data such as themes or authentication.

## 6. Transition Hooks

### 6.1. `useTransition`

Marks state updates as non-urgent, allowing smoother user experiences by deferring updates.

**Example:** Non-urgent State Update

```jsx
import { useState, useTransition } from "react";

function SearchComponent({ data }) {
  const [searchTerm, setSearchTerm] = useState("");
  const [isPending, startTransition] = useTransition();
  const filteredData = data.filter((item) => item.includes(searchTerm));

  const handleChange = (e) => {
    startTransition(() => {
      setSearchTerm(e.target.value);
    });
  };

  return (
    <div>
      <input type="text" onChange={handleChange} placeholder="Search..." />
      {isPending ? (
        <p>Loading...</p>
      ) : (
        <ul>
          {filteredData.map((item) => (
            <li key={item}>{item}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

**Explanation:** useTransition allows state updates to be deferred, preventing the UI from blocking while performing an expensive operation like filtering large data sets.

### 6.2. `useDeferredValue`

Defers a value update to allow more urgent updates to proceed first.

**Example:** Deferring Value Update

```jsx
import { useState, useDeferredValue } from "react";

function DeferredList({ items }) {
  const [filter, setFilter] = useState("");
  const deferredFilter = useDeferredValue(filter);

  const filteredItems = items.filter((item) => item.includes(deferredFilter));

  return (
    <div>
      <input
        type="text"
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Filter..."
      />
      <ul>
        {filteredItems.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

**Explanation:** useDeferredValue defers updating the filter value, improving performance by allowing the UI to remain responsive while the filtering operation is delayed.

## 7. Debugging Hooks

### 7.1. `useDebugValue`

Provides a label for custom hooks to assist with debugging in React DevTools.

**Example:** Custom Hook Debugging

```jsx
import { useState, useDebugValue } from "react";

function useCustomHook(value) {
  const [state, setState] = useState(value);
  useDebugValue(state ? "Active" : "Inactive");
  return [state, setState];
}

function DebugComponent() {
  const [isActive, setIsActive] = useCustomHook(true);

  return <div>{isActive ? "Active" : "Inactive"}</div>;
}
```

**Explanation:** useDebugValue is used in custom hooks to provide additional debugging information in React DevTools, useful for identifying the state of the hook.

### 7.2. `useId`

Generates a unique ID for form elements or other use cases where a unique identifier is needed.

**Example:** Generating Dynamic IDs for Forms

```jsx
import { useId } from "react";

function FormComponent() {
  const id = useId();

  return (
    <div>
      <label htmlFor={id}>Email</label>
      <input id={id} type="email" placeholder="Enter your email" />
    </div>
  );
}
```

**Explanation:** useId generates unique IDs, useful for ensuring form fields or components have unique identifiers, especially when the component is reused multiple times.

### 3.2. useImperativeHandle

Customizes the instance value that is exposed to parent components when using ref.

**Example**: Exposing Child Methods to Parent

```jsx
import { useImperativeHandle, forwardRef, useRef } from "react";

function ChildComponent(props, ref) {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focusInput: () => {
      inputRef.current.focus();
    },
  }));

  return <input ref={inputRef} placeholder="Focus me" />;
}

const ForwardedChild = forwardRef(ChildComponent);

function ParentComponent() {
  const ref = useRef();

  return (
    <div>
      <ForwardedChild ref={ref} />
      <button onClick={() => ref.current.focusInput()}>Focus Input</button>
    </div>
  );
}
```

**Explanation**: useImperativeHandle allows the child component to expose a method (focusInput) that the parent component can call, without exposing the entire DOM element.

### 2.2. useLayoutEffect

Runs synchronously after all DOM mutations. Used when you need to measure or modify the DOM immediately after render.

**Example**: Measuring DOM Size

```jsx
import { useLayoutEffect, useRef, useState } from "react";

function LayoutComponent() {
  const [height, setHeight] = useState(0);
  const divRef = useRef();

  useLayoutEffect(() => {
    setHeight(divRef.current.offsetHeight);
  }, []);

  return (
    <div>
      <div
        ref={divRef}
        style={{ height: "100px", backgroundColor: "lightblue" }}
      >
        Resize me
      </div>
      <p>Height: {height}px</p>
    </div>
  );
}
```

**Explanation**: useLayoutEffect is used to measure the height of the div immediately after it renders. This hook is useful when you need to interact with the DOM before the browser paints the screen.