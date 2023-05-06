# Fetch API & CORS

## 학습 키워드

- Fetch API 란
- Promise
- ReableStream
- Unicode
- CORS 란

## 🍀 Fetch API

ES6에 도입된 자바스크립트 내장 함수로, 클라이언트가 서버에 네트워크 요청을 보내서 정보를 받아올 때 사용함

- Promise 기반이라 비동기로 응답 받은 데이터를 다루기 쉬움
- 내장 함수라서 모듈 설치할 필요가 없음

**Promise**

일반 함수처럼 값을 받아오면 비동기적인 동작을 기다려주지 않기 때문에 비동기적인 함수의 값을 받는 방식이 따로 필요함. 그 방식 중 하나가 Promise

- 만약 프라미스 사용하지 않는다면?

    데이터를 받고 콜백 함수를 중첩해서 불러오는 과정에서 에러 핸들링이 어렵다. 가독성이 나쁘고 콜백지옥이 생성되며 오류를 일으킬 확률↑

- **프라미스의 사용 효과**

    > 데이터를 받음
    >
    > → (성공) 실행
    >
    > → (실패) 에러 대응

    프라미스는 성공/실패 각각의 경우에 js 내부 함수를 활용해서 실행되는 구조를 만 들 수 있음

### 기본 문법

fetch의 기본 메소드(생략시)는 `get`임

`json()`을 사용해서 JSON으로 변환 후, await를 걸어주면 바로 사용 가능한 객체로 변환해줌

```jsx
const response = await fetch('http://localhost:3000/products');
const data = await response.json();

// 다른 HTTP Method 사용
const response = fetch(url, {
 method: 'POST',
});
```

> **await**
>
> `let response = await promise`
>
> promise.then에 연결한 것처럼 비동기처리에 순서를 부여하고, 반환값을 받음

**`json()` 미사용해서 데이터 변환하기**

`await`를 통해 response를 받아온 다음, 그 안에 있는 body를 읽어오고 그걸 디코더로 바꾸는 등 복잡한 과정을 거쳐야 함

=> 데이터를 json으로 바로 변환해주는 `json()`을 바로 사용하는게 편함!

```jsx
fetch('http://localhost:3000/products');
// → Promise 반환

await fetch('http://localhost:3000/products');
// → Promise 안의 Response 반환

const response = await fetch('http://localhost:3000/products');
// → response.body는 ReadableStream

const reader = response.body.getReader();

const chunk = await reader.read();
// → chunk.value는 Uint8Array 타입
// → chunk.done은 데이터를 모두 읽어왔는지 표시됨 (데이터가 클 경우 한 번에 읽어오지 못할 수 있어서 true가 될 때까지 반복해야함)

const body = new TextDecoder().decode(chunk.value);
// TextDecoder를 사용해서 유니코드를 json으로 변환

const data = JSON.parse(body);
```

## 🍀 Ajax / Axois / Fetch

Ajax -> Axios -> Fetch

오래된 순서이며, 이전 방식의 문제를 해결하기 위해 다음 방식이 생겨남

### Ajax

과거 브라우저에서 화면을 갱신하지 않고 서버로부터 데이터를 받아오기 위해 사용하던 프로그래밍 방식

브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신함

- 기본 메소드(XMLHttpRequest 등)을 사용한 구현이 어려워서 라이브러리 JQuery를 주로 사용했음
- Promise 기반이 아님

### Axios

Promise 기반의 HTTP 통신 라이브러리

- Ajax 방식보다 비동기로 데이터를 다루기 편리해짐

### Axios vs Fetch

| | Axios | Fetch|
|--|-------|-------|
| 브라우저 호환성 | `좋음` | `안좋음` <br /> 지원되지 않는 브라우저 있음 |
| response timeout 처리 방법 | `있음` | `없음` <br /> 네트워크 에러 발생시 대기시간 설정 불가 |
| 응답 데이터 JSON 변환 | `불필요` | `필요` <br /> 자동으로 변환 안 됨 |
| 모듈 설치 | `필요` | `불필요` <br /> 내장 함수라서 import 필요 없음 |

## 🍀 CORS

### 동일 출처 정책

웹 브라우저는 Same Origin Policy에 따라 웹 페이지와 리소스를 요청한 곳이 서로 다른 출처(포트까지 포함)일 때 서버에서 얻은 결과를 사용할 수 없게 막는다

서버에 요청하고 응답을 받아오는 것까지는 이미 진행이 다 된 상황에서 막음 [(참고)](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)

ex) `http://localhost:8080` 페이지에서 `http://localhost:3000/products`의 데이터를 불러오는 경우

### 해결방법 => CORS

교차 출처 리소스 공유(Cross-Origin Resource Sharing)

추가 HTTP 헤더를 사용하여, 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제 [(참고)](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

**적용 방법**

> 데이터를 제공하는 서버에서 설정해야함

- REST API 서버에서 Headers에 “Access-Control-Allow-Origin” 속성을 추가
- Express에서는 [CORS 미들웨어](https://expressjs.com/en/resources/middleware/cors.html)를 설치해서 사용하면 됨

### CORS 미들웨어 적용법

**설치**

```bash
npm i cors
npm i -D @types/cors
```

**사용**

```jsx
import express from 'express';
**import cors from 'cors';**

const app = express();

**app.use(cors());**
```

이렇게 설정하면 현재 서버에서 전달하는 데이터를 다른 모든 출처에서 불러올 수 있음

(데이터를 받아갈 수 있는 특정 서버를 상세 설정할 수 있지만 깊은 내용이라 안 다룸)
