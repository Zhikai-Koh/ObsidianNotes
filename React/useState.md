Note that props are read only, can only set state using the given function.
```jsx
import { useState } from "react";

function FavoriteColor() {
{
  const [color, setColor] = useState("red");
}
```
1. Import useState
2. Set two variables, first variable holds the current state, second variable holds the function to change the state.
3. Argument placed inside useState() determines the initial value of the first variable.

### Example:
```jsx
import { useState } from 'react';
import { createRoot } from 'react-dom/client';

function FavoriteColor() {
  const [color, setColor] = useState("red");

  return (
    <>
      <h1>My favorite color is {color}!</h1>
      <button
        type="button"
        onClick={() => setColor("blue")}
      >Blue</button>
    </>
  )
}

createRoot(document.getElementById('root')).render(
  <FavoriteColor />
);
```

#### Another way to update the state:

If the state is an object like:
```jsx
  const [car, setCar] = useState({
    brand: "Ford",
    model: "Mustang",
    year: "1964",
    color: "red"
  });
```
Using `setCar({color: "blue"})` will result in the whole object being overridden.

Instead use this:
```jsx
const updateColor = () => {
  setCar(previousState => {
    return { ...previousState, color: "blue" }
  });
}
```
This ensures that only the property of color is updated, and all other states are preserved.

### Next Chapter:
Up next: [[useEffect]]