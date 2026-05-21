How to use:
```jsx
useReducer(reducer, initialState, init)
```

The `reducer` function contains your custom state logic and the `initialState`can be a simple value, but generally will contain an object. The `init` argument is optional and is used to initialize the state.

The `useReducer` Hook returns the current `state`and a `dispatch`method.

### Example:
```jsx
import { useReducer } from 'react';
import { createRoot } from 'react-dom/client';

const initialScore = [
  {
    id: 1,
    score: 0,
    name: "John",
  },
  {
    id: 2,
    score: 0,
    name: "Sally",
  },
];

const reducer = (state, action) => {
  switch (action.type) {
    case "INCREASE":
      return state.map((player) => {
        if (player.id === action.id) {
          return { ...player, score: player.score + 1 };
        } else {
          return player;
        }
      });
    default:
      return state;
  }
};

function Score() {
  const [score, dispatch] = useReducer(reducer, initialScore);

  const handleIncrease = (player) => {
    dispatch({ type: "INCREASE", id: player.id });
  };

  return (
    <>
      {score.map((player) => (
        <div key={player.id}>
          <label>
            <input
              type="button"
              onClick={() => handleIncrease(player)}
              value={player.name}
            />
            {player.score}
          </label>
        </div>
      ))}
    </>
  );
}

createRoot(document.getElementById('root')).render(
  <Score />
);
```
### Example of using init:

```jsx
// ❌ BAD: localStorage.getItem runs on EVERY single render
const [state, dispatch] = useReducer(reducer, {
  tokens: localStorage.getItem('auth_tokens') 
});

//   GOOD: localStorage.getItem runs EXACTLY ONCE on mount
function initializeState(key) {
  return { tokens: localStorage.getItem(key) };
}
// ... inside your component:
const [state, dispatch] = useReducer(reducer, 'auth_tokens', initializeState);
```

### Next chapter:
Next up: [[useCallback]]