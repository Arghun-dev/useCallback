# useCallback

`useCallback` and `useMemo` end up being functionally very similar. And in fact `useCallback` is implemented internally with react with `useMemo`

Example: 

```js
import { useState, useEffect, useCallback, memo } from "react";

const ExpensiveComputationComponent = memo(({ compute, count }) => {
  return (
    <div>
      <h1>computed: {compute(count)}</h1>
      <h4>last re-render: {new Date().toLocaleTimeString()}</h4>
    </div>
  )
});

const CallbackComponent = () => {
  const [count, setCount] = useState(1);
  const [time, setTime] = useState(new Date());
  
  useEffect(() => {
    const timer = setTimeout(() => setTime(new Date()), 1000);
    
    return () => clearTimeout(timer);
  }, [time])
  
  const fibonacci = useCallback((n) => {
    if (n <= 1) {
      return 1
    }
    
    return fibonacci(n - 1) + fibonacci(n - 2);
  }, [count])
  
  return (
    <div>
      <h1>useCallback Example: {time.toLocalTimeString()}</h1>
      <button onClick={() => setCount(count + 1)}>
        current count: {count}
      </button>
      <ExpensiveComputationComponent compute={} />
    </div>
  )
};

export default CallbackComponent;
```
