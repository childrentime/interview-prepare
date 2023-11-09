# Hooks

## useInternal

```tsx
function useInternal(callback: () => void, timeout: number | null) {
  const savedCallback = useRef(callback);
  useEffect(() => {
    savedCallback.current = callback;
  });
  const timer = useRef(null);
  useEffect(() => {
    if (timeout !== null) {
      timer.current = setTimeout(() => {
        savedCallback.current?.();
      }, timeout);
    } else {
      timer.current && clearTimeout(timer.current);
    }

    return () => {
      timer.current && clearTimeout(timer.current);
    };
  }, [timeout]);
}
```

## usePersistFn

```tsx
function usePersistFn(callback: () => void) {
  const savedCallback = useRef(callback);
  useEffect(() => {
    savedCallback.current = callback;
  });

  return useCallback((...args) => {
    savedCallback.current?.(...args);
  }, []);
}
```

# Class Components

## createRef

```tsx
function createRef(): RefObject {
  const refObject = {
    current: null,
  };
  if (__DEV__) {
    Object.seal(refObject);
  }
  return refObject;
}
```
