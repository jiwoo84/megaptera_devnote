# JSX

## 학습 키워드

- React에서 JSX를 사용하는 목적
- Syntactic sugar
- React.createElement
- React Element
- React StrictMode
- VDOM(Virtual DOM)이란?
  - DOM이란?
  - DOM과 Virtual DOM의 차이
- Reconciliation(재조정) 과정은 무엇인가?

## 🍀 React에서 JSX를 사용하는 목적

`XML-like syntax extension to ECMAScript`

JSX: 자바스크립트 XML(HTML의 한계를 극복하기 위해 만들어진 마크업 언어)의 약자

⇒ 자바스크립트를 확장한 문법이며 HTML을 React에서 쉽게 쓰기 위해 사용

<details>
<summary>참고자료</summary>
<ul>
<li><a href="https://ko.reactjs.org/docs/faq-build.html">Babel, JSX, 그리고 빌드 과정들</a></li>
<li><a href="https://ko.reactjs.org/docs/jsx-in-depth.html">JSX 이해하기</a></li>
</ul>
</details>

## 🍀 Syntactic sugar

JSX는 `React.createElement(component, props, ...children)`에 대한 Syntactic sugar를 제공함

=> XML처럼 작성된 부분이 React.createElement를 사용해서 JavaScript 코드로 변환됨
(중괄호를 써서 JavaScript 코드를 그대로 쓸 수도 있음)

=> JavaScript 코드와 1:1로 매칭됨

- `React.createElement` 재정의  
    JSX 파일에 `/*@jsx hello */` 주석을 추가하면 `React.createElement` 대신 `hello` 씀

- 변환기 [Babel](https://babeljs.io/repl)로 확인하기  
    “Presets”에서 “react”를 체크하거나, “Plugins”에서 “@babel/plugin-transform-react-jsx”를 추가

### Example

`JSX 코드`

```jsx
<p>Hello, world!</p>
// ↓
React.createElement("p", null, "Hello, world!");


<Greeting name="world" />
// ↓
React.createElement(Greeting, { name: "world" });


<Button type="submit" onClike={() => console.log('hi')}>Send</Button>
// ↓
React.createElement(Button, { type: "submit", onClick: () => console.log('hi') }, "Send");


<div className="test">
 <p>Hello, world!</p>
 <Button type="submit">Send</Button>
</div>
// ↓
React.createElement(
 "div",
 { className: "test" },
 React.createElement("p", null, "Hello, world!"),
 React.createElement(Button, { type: "submit" }, "Send")
);

// 여기까지 들음 20분
<div>
 <p>Count: {count}!</p>
 <button type="button" onClick={() => setCount(count + 1)}>Increase</button>
</div>
// ↓
React.createElement("div", null,
 React.createElement("p", null, "Count: ", count, "!"),
 React.createElement("button", { type: "button", onClick: () => setCount(count + 1) }, "Increase")
);
```

## React Element

- [JSX 없이 사용하는 React](https://ko.reactjs.org/docs/react-without-jsx.html)
- [createElement](https://beta.reactjs.org/reference/react/createElement)

JSX 대신 그냥 React.createElement를 써서 React Element 트리 갱신 가능

JSX Runtime은 _jsx란 함수를, Preact는 h란 함수를 직접 지원:

- [_jsx](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html)
- [h()](https://preactjs.com/guide/v10/api-reference/#h--createelement)

## VDOM (Virtual DOM)

- [VDOM (Virtual DOM)](https://ko.reactjs.org/docs/faq-internals.html)
- [재조정 (Reconciliation)](https://ko.reactjs.org/docs/reconciliation.html)

트리는 프랙탈과 같다. 트리의 구성요소는 트리다. 우리는 매번 작은 React Element 트리, VDOM 트리를 만든다. VDOM은 실제 DOM과 비교를 통해 변경사항을 적용한다.

## React Developer Tools

- [react devtools extensions](https://github.com/facebook/react/tree/main/packages/react-devtools-extensions)

    → [Strict Mode](https://ko.reactjs.org/docs/strict-mode.html)를 쓰지 않으면 경고함.

## VDOM을 쓰는 이유?

<aside>
🤪 미신: VDOM을 쓰는 건 빠르기 때문이다.

👉 [현실](https://twitter.com/dan_abramov/status/842329893044146176)
 → fast enough
 → maintainable

</aside>

Dan Abramov

- [Redux](https://redux.js.org/) 창시자
- [React Core 개발자](https://beta.reactjs.org/learn/meet-the-team)

👉 [다시 VDOM 문서](https://ko.reactjs.org/docs/faq-internals.html)

> “이 접근방식이 React의 **선언적 API**를 가능하게 합니다”
>

VDOM이 무엇이고, 왜 쓰는지 안다면 활용할 수 있는 [최적화 기법](https://ko.reactjs.org/docs/optimizing-performance.html)이 존재함.
