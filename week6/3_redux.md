# Redux 따라하기

## 학습 키워드

- Redux
- Reflect

## 🍀 redux를 사용하지 않고 비슷하게 만들어보기

### `src/stores/BaseStore.ts` 파일

```tsx
export type Action<Payload> = {
 type: string;
 payload?: Payload;
}

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Reducers<State> = Record<string, Reducer<State, any>>;

type Listener = () => void;

export default class BaseStore<State> {
 state: State;

 reducer: Reducer<State, any>;

 listeners = new Set<Listener>();

 constructor(initialState: State, reducers: Reducers<State>) {
  this.state = initialState;

  this.reducer = (state: State, action: Action<any>): State => {
   const reducer = Reflect.get(reducers, action.type);
   if (!reducer) {
    return state;
   }
   return reducer(state, action);
  };
 }

 dispatch<T>(action: Action<T>) {
  this.state = this.reducer(this.state, action);
  this.listeners.forEach((listener) => listener());
 }

 addListener(listener: Listener) {
  this.listeners.add(listener);
 }

 removeListener(listener: Listener) {
  this.listeners.delete(listener);
 }
}
```

### `src/stores/Store.ts` 파일

```tsx
import { singleton } from 'tsyringe';

import BaseStore, { Action } from './BaseStore';

const initialState = {
 count: 0,
 name: 'Tester',
};

export type State = typeof initialState;

function increase(state: State, action: Action<number>) {
 return {
  ...state,
  count: state.count + (action.payload ?? 1),
 };
}

function decrease(state: State) {
 return {
  ...state,
  count: state.count - 1,
 };
}

const reducers = {
 increase,
 decrease,
};

@singleton()
export default class Store extends BaseStore<State> {
 constructor() {
  super(initialState, reducers);
 }
}
```

### `src/hooks/useDispatch.ts` 파일

```tsx
import { container } from 'tsyringe';

import { Action } from '../stores/BaseStore';

import Store from '../stores/Store';

export default function useDispatch<Payload>() {
 const store = container.resolve(Store);
 return (action: Action<Payload>) => store.dispatch(action);
}
```

`src/hooks/useSelector.ts` 파일

```tsx
import { container } from 'tsyringe';

import { useEffect, useState } from 'react';

import Store, { State } from '../stores/Store';

import useForceUpdate from './useForceUpdate';

type Selector<T> = (state: State) => T;

export default function useSelector<T>(selector: Selector<T>): T {
 const store = container.resolve(Store);

 const [state, setState] = useState<T>(selector(store.state));

 const forceUpdate = useForceUpdate();

 useEffect(() => {
  const update = () => {
   const newState = selector(store.state);
   // TODO: T가 object일 때 처리 필요함.
   if (newState !== state) {
    setState(newState);
    forceUpdate();
   }
  };

  store.addListener(update);
  return () => store.removeListener(update);
 }, [store, forceUpdate]);

 return state;
}
```

### Dispatch와 Selector 사용

```tsx
const dispatch = useDispatch();
const count = useSelector((state) => state.count);

dispatch({ type: 'increase' });
dispatch({ type: 'decrease' });

dispatch({ type: 'increase', payload: 10 });
```

### Action을 메서드로 처리하기

`src/stores/ObjectStore.ts` 파일

```tsx
type Listener = () => void;

export default class ObjectStore {
 private listeners = new Set<Listener>();

 addListener(listener: Listener) {
  this.listeners.add(listener);
 }

 removeListener(listener: Listener) {
  this.listeners.delete(listener);
 }

 protected publish() {
  this.listeners.forEach((listener) => listener());
 }
}
```

### `src/stores/CounterStore.ts` 파일

```tsx
import { singleton } from 'tsyringe';

import ObjectStore from './ObjectStore';

@singleton()
export default class CounterStore extends ObjectStore {
 count = 0;

 increase(step = 1) {
  this.count += step;
  this.publish();
 }

 decrease() {
  this.count -= 1;
  this.publish();
 }
}
```

### `src/hooks/useObjectStore.ts` 파일

```tsx
import { useEffect } from 'react';

import useForceUpdate from './useForceUpdate';

import ObjectStore from '../stores/ObjectStore';

export default function useObjectStore<T extends ObjectStore>(store: T) {
 const forceUpdate = useForceUpdate();

 useEffect(() => {
  store.addListener(forceUpdate);
  return () => store.removeListener(forceUpdate);
 }, [store]);

 return store;
}
```

### `src/hooks/useCounterStore.ts` 파일

```tsx
import { container } from 'tsyringe';

import useObjectStore from './useObjectStore';

import CounterStore from '../stores/CounterStore';

export default function useCounterStore() {
 const store = container.resolve(CounterStore);

 return useObjectStore(store);
}
```
