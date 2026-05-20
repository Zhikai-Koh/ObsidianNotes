### Using conditional to determine what to be rendered:

#### 1. Using &&:
Right side of && will only be evaluated of left side is true.
```jsx
function Car(props) {
  return (
    <>
      {props.brand && <h1>My car is a {props.brand}!</h1>}
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <Car brand="Ford" />
);
```

#### 2. Using If-Else and "? :" :
```jsx
function Goal(props) {
  const isGoal = props.isGoal;
  return (
    <>
      { isGoal ? <MadeGoal/> : <MissedGoal/> }
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <Goal isGoal={false} />
);
```

### Next Chapter:
Next up: [[Lists]]