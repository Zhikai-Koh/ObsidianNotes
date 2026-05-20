`forwardRef` lets your component pass a reference to one of its children. It's like giving a direct reference to a ==DOM element== inside your component.

### Example:
```jsx
import { forwardRef, useRef } from 'react';

const MyInput = forwardRef((props, ref) => (
  <input ref={ref} {...props} />
));

function App() {
  const inputRef = useRef();

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
	    {/* ref = {inputRef} stores a reference of MyInput to inputRef */}
	    {/* Allows App()(parent) access to input(child) */}
      <MyInput ref={inputRef} placeholder="Type here..." />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}
```

### Next Chapter:
Up next: [[Router]]