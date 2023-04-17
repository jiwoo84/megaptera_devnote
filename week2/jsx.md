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

> **XML-like syntax extension to ECMAScript**
>
- [facebook의 JSX 소개](https://facebook.github.io/jsx/)
- [React 공식문서의 JSX 소개](https://ko.reactjs.org/docs/introducing-jsx.html)
- [Babel, JSX, 그리고 빌드 과정들](https://ko.reactjs.org/docs/faq-build.html)
- [JSX 이해하기](https://ko.reactjs.org/docs/jsx-in-depth.html)

📚 JSX는 XML처럼 작성된 부분을 React.createElement을 쓰는 JavaScript 코드로 변환한다.
중괄호를 써서 JavaScript 코드를 그대로 쓸 수 있고, 결국은 JavaScript 코드와 1:1로 매칭된다.

변환기 중 제일 유명한 [Babel](https://babeljs.io/repl)로 확인 가능.

→ “Presets”에서 “react”를 체크하거나, “Plugins”에서 “@babel/plugin-transform-react-jsx”를 추가하면 JSX를 실험할 수 있다.

JSX 파일에 /*@jsx 어쩌고*/ 주석을 추가하면 React.createElement 대신 “어쩌고”를 쓰게 된다.

### Example #1

JSX 코드

```jsx
<p>Hello, world!</p>
```

변환된 JS 코드

```jsx
React.createElement("p", null, "Hello, world!");
```

### Example #2

JSX 코드

```jsx
<Greeting name="world" />
```

변환된 JS 코드

```jsx
React.createElement(Greeting, { name: "world" });
```

### Example #3

JSX 코드

```jsx
<Button type="submit">Send</Button>
```

변환된 JS 코드

```jsx
React.createElement(Button, { type: "submit" }, "Send");
```

### Example #4

JSX 코드

```jsx
<div className="test">
 <p>Hello, world!</p>
 <Button type="submit">Send</Button>
</div>
```

변환된 JS 코드

```jsx
React.createElement(
 "div",
 { className: "test" },
 React.createElement("p", null, "Hello, world!"),
 React.createElement(Button, { type: "submit" }, "Send")
);
```

### Example #5

JSX 코드

```jsx
<div>
 <p>Count: {count}!</p>
 <button type="button" onClick={() => setCount(count + 1)}>Increase</button>
</div>
```

변환된 JS 코드

```jsx
React.createElement(
 "div",
 null,
 React.createElement("p", null, "Count: ", count, "!"),
 React.createElement("button", { type: "button", onClick: () => setCount(count + 1) }, "Increase")
);
```

## React Element

- [JSX 없이 사용하는 React](https://ko.reactjs.org/docs/react-without-jsx.html)
- [createElement](https://beta.reactjs.org/reference/react/createElement)

JSX 대신 그냥 React.createElement를 써서 React Element 트리를 갱신하는데 쓸 수 있다.

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
