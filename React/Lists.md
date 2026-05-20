Use .map() to create lists.

Things to note:
1. Each list must have a unique key.
2. Can use index for key if the list is not dynamic.

### Example:
```jsx
function MyCars() {
  const cars = ['Ford', 'BMW', 'Audi'];
  return (
    <>
      <h1>My Cars:</h1>
      <ul>
        {cars.map((car, index) => <li key={index}>I am a { car }</li>)}
      </ul>
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <MyCars />
);
```

### Next Chapter:
Up next: [[Forms]]