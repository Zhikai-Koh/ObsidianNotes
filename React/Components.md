### What are components:
Components are independent and reusable bits of code. They serve the same purpose as JavaScript functions, but work in isolation and return HTML.
#### Component example:
```jsx
//Component names MUST start with capital
function Car() {
  return (
    <h2>Hi, I am a Car!</h2>
  );
}
```

### How to use components:
```jsx
const root = createRoot(document.getElementById('root'))

root.render(
  <Car />
)
```
#### Reusing components:
Go to [[Props]] to learn more about how props work.
```jsx
function Car(props) {
  return (
    <h2>I am a {props.brand}!</h2> 
  );
}

function Garage() {
  return (
    <>
      <h1>Who lives in my Garage?</h1>
      <Car brand="Ford" />
      <Car brand="BMW" />
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <Garage />
);
```
### Exporting  and importing components:
#### Exporting:
```jsx
function Car() {
  return (
    <h2>Hi, I am a Car!</h2>
  );
}

export default Car;
```
#### Importing:
```jsx
import { createRoot } from 'react-dom/client'
import Car from './Vehicle.jsx';

createRoot(document.getElementById('root')).render(
  <Car />
);
```

### Next Chapter:
Next up: [[Props]]
