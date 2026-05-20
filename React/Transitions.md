While similar to [[Suspense]], transition controls update priority. It tells react which update is urgent and which is not.

Use transitions when you have:
- A slow operation that might freeze the UI
- Updates that aren't immediately critical
- Search results that take time to display

Transition does NOT delay setState itself.  
  
It lowers the priority of the render work  
caused by that state update.
### Example:
```jsx
import { useState, useTransition } from 'react';

function SearchResults({ query }) {
  // Simulate slow search results
  const items = [];
  if (query) {
    for (let i = 0; i < 1000; i++) {
      items.push(<li key={i}>Result for {query} - {i}</li>);
    }
  }
  return <ul>{items}</ul>;
}

function App() {
  const [input, setInput] = useState('');
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    // Urgent: Update input field
    setInput(e.target.value);
    
    // Non-urgent: Update search results
    //The transition applies to the ENTIRE rendering caused by that state update.
    startTransition(() => {
      setQuery(e.target.value);
    });
  };

  return (
    <div>
      <input 
        type="text" 
        value={input} 
        onChange={handleChange} 
        placeholder="Type to search..."
      />
      {isPending && <p>Loading results...</p>}
      <SearchResults query={query} />
    </div>
  );
}
```

### Next Chapter:
Up next: [[ForwardRef]]