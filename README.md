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
      <ExpensiveComputationComponent compute={fibonacci} count={count} />
    </div>
  )
};

export default CallbackComponent;
```

So, why we wrapped our function inside `useCallback`, because fibonacci is actually being calculated down here in the `ExpensiveComputationComponent`, and the reason why that's bad, if I pass in fibonacci directly => notice this is now re-rendering every single time.

Why? 

Well, Fibonacci here which exists inside of callback component gets recreated every single time that we re-render the component. So every time, time state changes the whole component re-renders and the function of `fibonacci` re-created. And `fibonacci` is actually going to be a different function that does the same.

But react down here, in the ExpensiveComputationComponent says, hey `compute` is changing here every single time, and as you know 

1. whenever a parent component re-renders the child component also re-renders.
2. but to avoid this and just to re-render child component whenever one of the props changing we use HOC memo.
3. but here compute function is changing every single time
4. So to solve this, we use `useCallback` to change the compute function only when count is changed. so in this case compute is not changing anytime the state of time changes. and our problem solves.
