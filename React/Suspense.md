Suspense is what to show users while components are loading.

### Example:
```jsx
import { createRoot } from 'react-dom/client';
import { Suspense, lazy } from 'react';

const Header = lazy(() => import('./Header'));
const Content = lazy(() => import('./Content'));
const Sidebar = lazy(() => import('./Sidebar'));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <Header />
        <div style={{ display: 'flex' }}>
          <Sidebar />
          <Content />
        </div>
      </Suspense>
    </div>
  );
}

createRoot(document.getElementById('root')).render(
  <App />
);
```
This example shows that suspense can wrap many components at once until all is loaded up.

### Next Chapter:
Up next: [[Transitions]]