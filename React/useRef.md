The `useRef` Hook allows you to persist values between renders.
It can be used to store a mutable value that does not cause a re-render when updated.
It can be used to access a DOM element directly.
useRef returns an object called current that can be accessed using:
`variable_name.current`

Similarly, check out [[ForwardRef]]

### Examples:

#### Using useRef to track previous state:
```jsx
import { useRef, useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  const [inputValue, setInputValue] = useState("");
  const previousInputValue = useRef("");

  useEffect(() => {
    previousInputValue.current = inputValue;
  }, [inputValue]);

  return (
    <>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
      <h2>Current Value: {inputValue}</h2>
      <h2>Previous Value: {previousInputValue.current}</h2>
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <App />
);
```

#### Using useRef to focus input
This example is similar to the one shown in [[ForwardRef]]

```jsx
import { useRef, useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

function App() {
  const [inputValue, setInputValue] = useState("");
  const previousInputValue = useRef("");

  useEffect(() => {
    previousInputValue.current = inputValue;
  }, [inputValue]);

  return (
    <>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
      <h2>Current Value: {inputValue}</h2>
      <h2>Previous Value: {previousInputValue.current}</h2>
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <App />
);
```

### Next Chapter:
Up next: [[useReducer]]