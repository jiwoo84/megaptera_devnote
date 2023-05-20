# TSyringe

## 학습 키워드

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- sington (싱글톤)

## 🍀 TSyringe란?

External Store를 관리하는데 활용 가능한 TypeScript용 DI 도구(IoC Container)

 이를 통해 store를 만들면 React 컴포넌트 입장에서는 “전역”처럼 여겨짐

 “Prop Drilling” 문제를 해결할 수 있는 방법 중 하나(React로 한정하면 Context도 쓸 수 있음)

 **DI (Dependency injection: 의존성 주입)**

 하나의 객체가 다른 객체의 의존성을 제공하는 테크닉

 클라이언트가 의존성(서비스)에 대해 관여하지 않고, 신경쓰지 않도록 분리하려는 목적으로 사용함

## 🍀 Tsyringe로 상태관리 구현하기

redux와 비슷하게 기능하는 구조 만들어보기

### 기본 세팅

**의존성 설치**

```bash
npm i tsyringe reflect-metadata
```

**`reflect-metadata`를 실행시키기**

프론트 & 테스트 첫 실행 파일의 상단에 import 하기

(`src/main.tsx` 파일, `src/setupTests.ts` 파일)

```tsx
import 'reflect-metadata';
```

**데코레이터 사용 설정**

- 데코레이터: 클래스 선언, 메서드, 접근자, 프로퍼티 또는 매개 변수에 첨부할 수 있는 특수한 종류의 선언, `@expression` 형식 사용

데코레이터 형식을 사용하기 위해서는 `tsconfig.json`의 `experimentDecorators`, `emitDecoratorMetadata` 옵션을 주석 해제해서 활성화 해야함

### 싱글톤으로 관리할 Store 클래스 생성 및 사용

Store 안의 값은 전역으로 취급되며, `container`로 불러와서 사용 가능

**생성**

```tsx
// src/stores/Store.ts
import { singleton } from 'tsyringe';

type Listener = () => void;

@singleton() // 데코레이터 여기서 사용
class CounterStore {
 count = 0; // 초기값 설정

 // Counter에서 forceUpdate에 값 넣는데 해당 컴포넌트가 여러번 출력될 경우
 // forceUpdate의 값이 덮어쓰기 되어 맨 마지막 컴포넌트만 제대로 출력되니
 // 이렇게 set으로 감싸서 각 컴포넌트의 강제 렌더링 실행
 // 중복으로 들어가는 것을 막기 위해 set으로 설정
 listeners = new Set<Listener>();

 publish() { // 상태값의 수정사항(listener)을 적용하고 발생
    this.listeners.forEach((listener) => {
        listener();
    })
 }

 addListener(listener: Listener) {
    this.listeners.add(listener);
 }

 removeListener(listener: Listener) {
    this.listeners.delete(listener);
 }

}

export default CounterStore;
```

**사용**

```tsx
// src/Counter.tsx
import { useEffect } from 'react';
import { container } from 'tsyringe';

import CounterStore from '../stores/CounterStore';

import useForceUpdate from '../hooks/useForceUpdate';

export default function Counter() {
    const store = container.resolve(CounterStore); // store 불러옴
    const forceUpdate = useForceUpdate(); // 강제 렌더링 함수 불러옴

    useEffect(() => {
        store.addListener(forceUpdate); // set에 함수 추가

        // 재랜더링, 컴포넌트 사라질 때 실행됨
        return () => {
            // 쓰는 이유
            // 1. forceUpdate 삭제시 store.update를 실행하면 안에 함수를 찾지 못해 에러 발생 가능성 있음
            // 2. increase, decrease 실행할 때마다 set에 forceUpdate가 쌓여가기 때문에 삭제해야함
            store.removeListener(forceUpdate); // set에서 함수 삭제
        }
    }, [store, forceUpdate]) // 두 가지 의존성 설정

    return (
        <p>Count: {store.count}</p>
    )
}

// src/CounterControl.tsx
import { container } from 'tsyringe';

import CounterStore from '../stores/CounterStore';

export default function CounterControl() {
    const store = container.resolve(CounterStore);

    const handleClickIncrease = () => {
        store.count ++;
        store.publish();
    }

    const handleClickDecrease = () => {
        store.count --;
        store.publish();
    }

    return (
        <>
        <button type='button' onClick={handleClickIncrease}>increase</button>
        <button type='button' onClick={handleClickDecrease}>Decrease</button>
        </>
    )
}
```

### 테스트 작성

각 테스트는 독립적이어야 한다.

순차적으로 진행되기 때문에 첫번째 테스트가 store의 객체 값을 변경하면 다음 테스트에도 영향을 미친다.

이는 테스트의 독립성을 망치므로, 테스트 시작 전에는 store를 초기화해주는 과정이 필요하다.

> TSyringe에서 관리하는 객체를 초기화하는 메서드
>
> `container.clearInstances()`

**예시**

```tsx
import { fireEvent, render, screen } from "@testing-library/react"

import { container } from "tsyringe";

import App from "./App"

const context = describe;

test("App", () => {
    render(<App />)
})

// 테스트코드는 순차적으로 실행되게 하면 안됨
// 각 독립적인 테스트여야 함
describe('App', () => {
    beforeEach(() => { // 테스트 시작 전마다
        container.clearInstances(); // 콘테이너 안의 값 초기화
    })
})

// 한 번 누르면 count = 1 로 출력 상태인지 테스트
describe('App', () => {
    context('when press increase button once', () => {
        test('counter', () => {
            render(<App />);

            fireEvent.click(screen.getByText("Increase"));

            expect(screen.getAllByText('count: 1')).toHaveLength(2);
        })
    })
})

// 한 번 누르면 count = 2 로 출력 상태인지 테스트
describe('App', () => {
    context('when press increase button twice', () => {
        test('counter', () => {
            render(<App />);

            fireEvent.click(screen.getByText("Increase"));
            fireEvent.click(screen.getByText("Increase"));

            expect(screen.getAllByText('count: 2')).toHaveLength(2);
        })
    })
})
```

## 상태 변경 알림

Store는 어떤 식으로든 action을 처리하고, 상태가 바뀌면 연결된 컴포넌트를 forceUpdate한다.

컴포넌트는 해당 Store에서 상태를 얻어서 UI를 업데이트하게 되는데, 선언형 UI가 얼마나 편한지 절실히 느낄 수 있다.
