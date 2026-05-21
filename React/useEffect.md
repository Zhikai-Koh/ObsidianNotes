The `useEffect` Hook allows you to perform side effects in your components.

Some examples of side effects are: fetching data, directly updating the DOM, and timers.

`useEffect` accepts two arguments. The second argument is optional.

`useEffect(<function>, <dependency>)`

### Example:
```jsx
import { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

function Counter() {
  const [count, setCount] = useState(0);
  const [calculation, setCalculation] = useState(0);

  useEffect(() => {
    setCalculation(() => count * 2);
  }, [count]); // <- This means that useEffect will run everytime count changes.

  return (
    <>
      <p>Count: {count}</p>
      <button onClick={() => setCount((c) => c + 1)}>+</button>
      <p>Calculation: {calculation}</p>
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <Counter />
);
```
To make useEffect run on multiple dependencies, just add more stuff to the [count] array.
To make useEffect only run once, use [] as its dependency

### Effect Cleanup:
```jsx
function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    let timer = setTimeout(() => {
      setCount((count) => count + 1);
    }, 1000);

    return () => clearTimeout(timer)
  }, []);

  return <h1>I've rendered {count} times!</h1>;
}
```
clearTimeout(timer) prevents memory leak and errors by removing the timer after the effect runs.

Reasons:
1. If component disappears (like when user leave page), the timer should disappear too.
2. Without clearing, even if people leave page, timer still continue firing even though component is not there.

### Next Chapter:
Next up: [[useContext]]
