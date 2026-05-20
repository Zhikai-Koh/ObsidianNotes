Props are arguments passed into React components.
Props are passed to components via HTML attributes.
Props are **read-only**!

Props acts as Javascript arguments, and HTML attributes:

```jsx
//Javascript similarity:
function Car(myProperty) {
  return (
    <h2>I am a {myProperty.brand}!</h2>
  );
}

var x = True;

createRoot(document.getElementById('root')).render(
//Similarity to HTML
  <Car brand="Ford" year={1969} available={x}/>
);
```
As seen in `<Car brand="Ford" year={1969} available={x}/>`, props can be of any data type.

However, other than Strings which can be sent in quotes, every other data type must be sent in curly brackets.

### Object Props:
```jsx
function Car(props) {
  return (
    <>
      <h2>My {props.carinfo.name} {props.carinfo.model}!</h2>
      <p>It is {props.carinfo.color} and it is from {props.carinfo.year}!</p>
    </>
  );
}

const carInfo = {
  name: "Ford",
  model: "Mustang",
  color: "red",
  year: 1969
};

createRoot(document.getElementById('root')).render(
  <Car carinfo={carInfo} />
);
```

### Array Props:
```jsx
function Car(props) {
  return (
    <h2>My car is a {props.carinfo[0]} {props.carinfo[1]}!</h2>
  );
}

const carInfo = ["Ford", "Mustang"];

createRoot(document.getElementById('root')).render(
  <Car carinfo={carInfo} />
);
```

### Destructuring Props:
#### 1. Use {} to destructure prop:
```jsx
function Car({color}) {
  return (
    <h2>My car is {color}!</h2>
  );
}

createRoot(document.getElementById('root')).render(
  <Car brand="Ford" model="Mustang" color="red" year={1969} />
);
```

#### 2. Destructure props inside components:
```jsx
function Car(props) {
  const {brand, model} = props;
  return (
    <h2>I love my {brand} {model}!</h2>
  );
}

createRoot(document.getElementById('root')).render(
  <Car brand="Ford" model="Mustang" color="red" year={1969} />
);
```

#### 3. ...Rest:
Unspecified arguments will be stored inside rest as {model: "Mustang", year: 1969}:
```jsx
function Car({color, brand, ...rest}) {
  return (
    <h2>My {brand} {rest.model} is {color}!</h2>
  );
}

createRoot(document.getElementById('root')).render(
  <Car brand="Ford" model="Mustang" color="red" year={1969} />
);
```

#### 4. Props.children:
Use .children on the prop to get the content from the parent.
```jsx
function Son(props) {
  return (
    <div style={{background: 'lightgreen'}}>
      <h2>Son</h2>
      <div>{props.children}</div>
    </div>
  );
}

function Parent() {
  return (
    <div>
      <h1>My Children</h1>
      <Son>
        <p>
          This was written in the Parent component,
          but displayed as a part of the Son component
        </p>
      </Son>
    </div>
  );
}

createRoot(document.getElementById('root')).render(
  <Parent />
);
```
### Default values:
```jsx
function Car({color = "blue", brand}) {
  return (
    <h2>My {color} {brand}!</h2>
  );
}

createRoot(document.getElementById('root')).render(
  <Car brand="Ford" />
);
```

### Next Chapter:
Next up: [[Events]]