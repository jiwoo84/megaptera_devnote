# MSW (Mock Service Worker)

## 학습 키워드

- Service worker
- MSW(Mock Service Worker)
- polyfill(폴리필)

> [아샬의 Mock Service Worker (MSW)](https://github.com/ahastudio/til/blob/main/mock-api/msw.md)
>

> [Mocking REST API](https://mswjs.io/docs/getting-started/mocks/rest-api)
>

> [Integrate mocking into Node](https://mswjs.io/docs/getting-started/integrate/node)
>

## 🍀 MSW (Mock Service Worker)란?

코드 레벨이 아니라 네트워크 레벨에서 가짜 구현. 오프라인 작업 등을 지원하기 위한 서비스 워커의 기능을 유용히 활용한 것

테스트 환경(Node.js 기반) 외에 웹 브라우저도 지원하기 때문에, API 스펙은 나왔지만 아직 구현되지 않은 경우 임시로 사용할 수도 있다.

단순히 임시 서버를 만들 거라면 Express를 쓰는 게 더 낫지만, 테스트 코드도 지원하면서 겸사겸사 웹 브라우저를 지원하는 용도로는 나쁘지 않은 선택이다.

**Serveice Worker API**

웹 응용 프로그램, 브라우저, 네트워크 사이의 프록시 서버 역할을 함

네트워크 요청을 가로채서 네트워크 사용 가능 여부에 따라 적절한 행동을 취함 => 오프라인에서 온라인처럼 통신하는 경험 생성 가능

- 워커 맥락에서 실행되기 때문에 DOM에 접근 불가
- JS와는 다른 스레드에서 동작하므로 연산을 중단하지 않음 (논 블로킹)
- 비동기적으로 동작하며, 동기적 XHR이나 웹 저장소 등의 API는 서비스 워커 내에서 사용 불가
- 보안상의 이유로 HTTPS에서만 동작 (firefox의 사생활 보호 모드도 불가)

## 🍀 사용 방법

### 1. 설치 및 기본 설정

**MSW 패키지 설치**

```bash
npm i -D msw
```

**`jest.config.js`파일 설정**

`jest.config.js` 파일의 “setupFilesAfterEnv” 속성에 `setupTests.ts` 파일 추가

```jsx
module.exports = {
 testEnvironment: 'jsdom',
 **setupFilesAfterEnv**: [
  '@testing-library/jest-dom/extend-expect',
  '<rootDir>/src/setupTests.ts', // 이 부분 추가
 ],
 // ...
};
```

### 2. msw 서버 생성

src 안에 mocks 폴더 생성 -> `server.ts` 파일 생성

```jsx
// src/mocks/server.ts
import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;
```

### 3. 핸들러 만들기

server에서 네트워크 요청을 가로채서 실행할 handler 만들기

```jsx
// src/mocks/handlers.ts

// eslint-disable-next-line import/no-extraneous-dependencies
import { rest } from 'msw'; // 불러올 때 eslint 에러가 발생한다면, 위에 주석 추가하거나 설정 파일 만들기

const BASE_URL = 'http://localhost:3000';

const handlers = [
 rest.get(`${BASE_URL}/products`, (req, res, ctx) => {
  const products = [
   {
    category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
   },
  ];
  // 베이스 객체 만들었다면 이렇게 불러와도 됨
  // const { products } = fixtures;

  // 위 url로 들어오는 요청은 모두 이렇게 반환됨
    return res(
      ctx.status(200), // 안써도 됨 (기본이 200임)
      ctx.json({ restaurants }),
      // express라면 return ({ products }) 쓰면 끝이지만 이건 express가 아니기 떄문에 이렇게 작성
   );
  }),
 ];

export default handlers;
```

### 4. 서버 실행 설정

**테스트시 서버 실행 및 필요한 동작 세팅**

`src/setupTests.ts` 파일 생성 및 설정

```jsx
import server from './mocks/server';

// 모든 테스트 실행 전에 MSW 서버 실행
// onUnhandledRequest : 핸들러 없다면 에러내서 실수 방지
beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

// 모든 테스트 실행 후에 서버 닫기
afterAll(() => server.close());

// 각 테스트 실행 후에 서버 리셋
afterEach(() => server.resetHandlers());
```

**브라우저 환경에서 사용하기**

위 코드만 설정하면 Node.js 환경에서만 서버가 실행되기 때문에 브라우저로 화면을 띄울 때는 데이터가 받아와지지 않음

보통 브라우저 환경에서 API를 모킹할 때는 worker를 사용하지만 server를 생성했다면 worker를 또 만들 필요 없이, 초기 실행되는 파일 상단에 서버를 실행시켜주면 된다.

```tsx
// src/main.tsx
import ReactDOM from 'react-dom/client';

import App from './App';
import server from './mocks/server';

function main() {
  server.listen(); // 여기에 적어주면 됨

  const container = document.getElementById('root');

  if (!container) {
    return;
  }

  const root = ReactDOM.createRoot(container);
  root.render(<App />);
}

main();
```

### 테스트 파일 작성

테스트 과정에서 파일에서 서버 통신을 하면 msw에서 가로채서 handlers에 설정한대로 동작하기 때문에 따로 mocking이 필요 없고, 실행을 기다리는 동작으로 감싸주면 됨

**waitFor(fnc)**

내부 함수가 동작할 때까지 기다렸다가 확인함

프로미스 기반 동작이기 때문에 `async`& `await`으로 감싸줘야함

```jsx
// App.test.ts`
import { render, screen, waitFor } from '@testing-library/react';

import App from './App';

// jest.mock 불필요

test('App', async () => {
 render(<App />);

 await waitFor(() => {
  screen.getByText('Apple');
 });
});
```

> 🚨 주의사항
>
> 너무 본격적으로 코딩하면 사실상 백엔드를 개발하게 되니, 이 부분에 주의할 것.

## 🍀 Node에서 fetch 사용하기

GitHub에서 만든 fetch polyfill인 whatwg-fetch 설치해서 사용 [(저장소 링크)](https://github.com/github/fetch)

**테스트 파일에 whatwg-fetch 불러오기**

```ts
import 'whatwg-fetch';
```

테스트에서 사용시 모든 파일 상단에 쓰거나, `setupTests` 파일 상단에 넣으면 됨

### 폴리필(polyfill)

오래된 브라우저나 기기에서도 새로운 기능을 사용할 수 있도록 지원하는 코드

브라우저에 내장된 기능을 사용하여 새로운 기능을 구현하는 것이 일반적임

예로 최신 버전의 JavaScript 코드를 구형 브라우저에서도 동작할 수 있는 코드로 변환하는 기능을 제공하는 babel이 있음.

이를 통해 개발자는 최신 기능을 사용하면서도, 모든 브라우저에서 해당 기능을 사용할 수 있게 됨.
