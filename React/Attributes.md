### Example of attributes:
```jsx
function Car() {
  return (
	//Note that instead of class, JSX uses className
    <h1 className="myclass">Hello World</h1>
  );
}
```
### Attribute values are wrapped in {}:
```jsx
function Car() {
  const x = "myclass";
  return (
	//Note that the attribute value is note wrapped in "" but is wrapped in {}
    <h1 className={x}>Hello World</h1>
  );
}
```
### Attributes follow camelCase:
```jsx
function Car() {
  const myfunc = () => {
    alert('Hello World');
  };
  return (
	//Note that attributes follow camelCase
    <button onClick={myfunc}>Click me</button>
  );
}
```

### Boolean attributes:
```jsx
//By default, all attributes in JSX is set to true
//I.e. the attribute disabled will be set to true
<button onClick={myfunc} disabled>Click me</button>
//Alternatively:
<button onClick={myfunc} disabled={true}>Click me</button>
```

### Style attribute:
```jsx
function Car() {
  const mystyles = {
    color: "red",
    fontSize: "20px",
    backgroundColor: "lightyellow",
    
  };

  return (
	//Style attribute only accept Javascript Objects with camelCased CSS properties, and the 
    <>
      <h1 style={mystyles}>My car</h1>
    </>
  );
}
```

### Next Chapter:
Next up: [[Components]]



