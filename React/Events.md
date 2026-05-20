React events are written in camelCase syntax:
`onClick` instead of `onclick`.

React event handlers are written inside curly braces:
`onClick={shoot}`  instead of `onclick="shoot()"`

### Event Example:
```jsx
function Football() {
//Can also make this a nullary function
  const shoot = (a) => {
    alert(a);
  }

  return (
    <button onClick={() => shoot("Goal!")}>Take the shot!</button>
  );
}

createRoot(document.getElementById('root')).render(
  <Football />
);
```
More examples in [[Forms]]

### Next Chapter:
Next up: [[Conditionals]]