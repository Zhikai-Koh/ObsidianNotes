### Example of form:
```jsx
import { useState } from 'react';
import { createRoot } from 'react-dom/client';

function MyForm() {
  const [name, setName] = useState("");

  function handleChange(e) {
    setName(e.target.value);
  }

  return (
    <form>
      <label>Enter your name:
        <input
          type="text" 
          value={name}
          onChange={handleChange}
        />
      </label>
      <p>Current value: {name}</p>
    </form>
  )
}

createRoot(document.getElementById('root')).render(
  <MyForm />
);
```

Things to note:
1. useState("") --> a function that is destructured into two variables, first variable will hold the value, second variable is a setter function that allows you to change the value. 
2. The argument passed into useState determines the starting value of the variable.

### Submit form button
```jsx
import { useState } from 'react';
import { createRoot } from 'react-dom/client';

function MyForm() {
  const [name, setName] = useState("");

  function handleChange(e) {
    setName(e.target.value);
  }

  function handleSubmit(e) {
    e.preventDefault();
    alert(name);
  }

  return (
	  //Add a onSubmit
    <form onSubmit={handleSubmit}>
      <label>Enter your name:
        <input
          type="text" 
          value={name}
          onChange={handleChange}
        />
      </label>
      {/* This line creates the submit button */}
      <input type="submit" />
    </form>
  )
}

createRoot(document.getElementById('root')).render(
  <MyForm />
);
```

### Next Chapter:
Up next: [[Portals]]