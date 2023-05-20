# usestore-ts 사용

## 학습 키워드

- usesotre-ts
- useSyncExternalStore

## 🍀 usestore-ts 사용해서 상태관리 하기

참고: [usestore-ts](https://usestore-ts.com/)

### Store 작성

```tsx
import { singleton } from 'tsyringe';

import { Store, Action } from 'usestore-ts';

@singleton()
@Store()
export default class CounterStore {
 count = 0;

 @Action()
 increase(step = 1) {
  this.count += step;
 }

 @Action()
 decrease() {
  this.count -= 1;
 }
}
```

### 커스텀 Hook 작성

```tsx
import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import CounterStore from '../stores/CounterStore';

export default function useCounterStore() {
 const store = container.resolve(CounterStore);
 return useStore(store);
}
```

비동기 함수에 `@Action`을 붙이면 다르게 작동할 수 있다는 점에 주의! 별도의 액션을 만들면 신경 쓸 부분이 줄어든다.

```tsx
@singleton()
@Store()
class PostStore {
 posts: Post[] = [];

 async fetchPosts() {
  this.startLoading();

  const posts = await fetchPosts();

  this.completeLoading(posts);
 }

 @Action()
 startLoading() {
  this.posts = [];
 }

 @Action()
 completeLoading(posts: Post[]) {
  this.posts = posts;
 }
}
```

### 참고

- [useSyncExternalStore](https://beta.reactjs.org/reference/react/useSyncExternalStore)
- [FECONF 2022 - 상태관리 이 전쟁을 끝내러 왔다](https://youtu.be/KEDUqA9JeIo)
