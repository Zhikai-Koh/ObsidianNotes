It is used to share state between deeply nested components more easily than with `useState` alone.
### Example
```jsx
import { useState, createContext, useContext } from 'react';
import { createRoot } from 'react-dom/client';

const UserContext = createContext();

function Component1() {
  const [user, setUser] = useState("Linus");

  return (
    <UserContext.Provider value={user}>
      <h1>{`Hello ${user}!`}</h1>
      <Component2 />
    </UserContext.Provider>
  );
}

function Component2() {
  return (
    <>
      <h1>Component 2</h1>
      <Component3 />
    </>
  );
}

function Component3() {
  const user = useContext(UserContext);

  return (
    <>
      <h1>Component 3</h1>
      <h2>{`Hello ${user} again!`}</h2>
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <Component1 />
);
```

1. Use `createContext()` to create a context.
2. Wrap the part of the tree where you want to use the context on with <{variableName}.Provider value = {state_you_want_to_pass_down}>
3. Use ```useContext(UserContext);``` to access the context in a child component.
### Next Chapter:
Next up: [[useRef]]
