Portals are used to render HTML outside the parent component's DOM hierachy.

Use when:
Any UI element that needs to "break out" of its container's layout, especially when the parent component has:

- `overflow: hidden`
- `z-index` conflicts
- Complex positioning requirements

To use:
`createPortal(<div> Hi </div>, document.body)`
### Example:
```jsx
import { createRoot } from 'react-dom/client';
import { useState } from 'react';
import { createPortal } from 'react-dom';

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;

  return createPortal(
    <div style={{
      position: 'fixed',
      top: 0,
      left: 0,
      right: 0,
      bottom: 0,
      backgroundColor: 'rgba(0, 0, 0, 0.5)',
      display: 'flex',
      alignItems: 'center',
      justifyContent: 'center'
    }}>
      <div style={{
        background: 'white',
        padding: '20px',
        borderRadius: '8px'
      }}>
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>,
    document.body
  );
}

function MyApp() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <h1>My App</h1>
      <button onClick={() => setIsOpen(true)}>
        Open Modal
      </button>

      <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
        <h2>Modal Content</h2>
        <p>This content is rendered outside the App component!</p>
      </Modal>
    </div>
  );
}

createRoot(document.getElementById('root')).render(
  <MyApp />
)
```

Even though a portal renders content in a different part of the DOM tree, events from the portal content still bubble up through the React component tree as if the portal wasn't there.

### Next Chapter:
Up next: [[Suspense]]