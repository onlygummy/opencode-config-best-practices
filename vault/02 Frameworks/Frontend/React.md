---
type: framework
category: frontend
tags: [react, javascript, ui]
created: 2026-09-01
last-reviewed: 2026-09-01
---

# React

## Overview

JavaScript library for building UI using component-based architecture and virtual DOM.

## When to Use

- Need interactive UI
- Team has JavaScript experience
- Need reusable components
- Need a large ecosystem

## When NOT to Use

- Small projects that do not need complexity
- Need good SEO (use Next.js instead)
- Team has no JavaScript experience

## Best Practices

1. Use functional components + hooks
2. Split components into small pieces following single responsibility
3. Use React.memo for performance
4. Manage state with Context API or state management library

## Common Mistakes

1. Using class components instead of functional components
2. Not using keys in lists
3. Mutating state directly
4. Not using cleanup in useEffect

## Example

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

## Related

- [[Next.js]]
- [[Vue]]
- [[JavaScript]]
