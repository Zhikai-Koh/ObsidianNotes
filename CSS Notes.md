
```jsx
                style={{
                  display: 'grid',
                  //auto-fill each item in the row, each item must be 250px wide, 1fr is fractional units, where minmax make it so that all item in that row will swallow the remaining empy space evenly
                  gridTemplateColumns: 'repeat(auto-fill, minmax(250px, 1fr))',
                  gap: '20px',
                  width: '100%',
                  boxSizing: 'border-box',
                  backgroundColor: '#f9f9f9'
```

## More on gridTemplateColumns:
"Look at the total screen width. Automatically figure out the count (**`auto-fill`**) of columns that can fit here. To do that math, assume each column needs to be at least **`250px`** wide. If you have extra space left over after fitting them all, stretch them out equally using **`1fr`** so they flush perfectly with the edges."
### Parameter 1: The Minimum Limit
The first value tells the browser how small a column is allowed to shrink before it is forced to stop.
 your code:_ We used `250px`. This acts as a safety guardrail. It guarantees that an item card will never compress below `250px` wide, protecting your text layouts and buttons from clipping or overlapping on small mobile phones.
### Parameter 2: The Maximum Limit
The second value tells the browser how wide a column is allowed to grow if there is excess space available.
- _In your code:_ We used `1fr`. This means once the minimum requirements are met, the columns are allowed to scale upward symmetrically to absorb any leftover empty space on the right side of the window.