# JSX

## 학습 키워드

- React에서 JSX를 사용하는 목적
- Syntactic sugar
- React.createElement
- React Element
- React StrictMode

## 🍀 React에서 사용하는 목적

`XML-like syntax extension to ECMAScript`

JSX: 자바스크립트 XML(HTML의 한계를 극복하기 위해 만들어진 마크업 언어)의 약자

⇒ 자바스크립트를 확장한 문법이며 HTML을 React에서 쉽게 쓰기 위해 사용

## 🍀 Syntactic sugar

JSX는 `React.createElement(component, props, ...children)`에 대한 Syntactic sugar를 제공함

**JSX는 React.createElement를 사용하는 JavaScript 코드로 변환됨**

- React가 스코프 내에 존재해야 하므로 상단에 import로 참조하기
- JavaScript 코드와 1:1로 매칭됨

**`React.createElement` 재정의**

- JSX 파일에 `/*@jsx hello */` 주석을 추가하면 `React.createElement` 대신 `hello` 씀

**변환기 [Babel](https://babeljs.io/repl)로 확인하기**

- “Presets”에서 “react”를 체크하거나, “Plugins”에서 “@babel/plugin-transform-react-jsx”를 추가

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

## 🍀 React Element

JSX 대신 React.createElement를 써서 React Element 트리 생성 가능

(JSX Runtime은 `createElement` 대신 `_jsx`, Preact는 `h`란 함수를 직접 지원)

- React element의 컴포넌트 자리에 표현식 사용 불가 => 대문자로 시작하는 변수에 할당해서 사용할 것

- React Componenet는 React Element를 반환함

## 🍀 React StrictMode

애플리케이션 내의 잠재적인 문제를 알아내기 위한 도구

Fragment와 같이 UI를 렌더링하지 않으며 자손들에 대한 검사과 경고 활성화

### 활성화

`React.StrictMode`의 자식에 한하여 적용됨

```jsx
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```

### 역할

- 안전하지 않은 생명주기를 사용하는 컴포넌트 발견: 지원 종료한 메서드 사용시 경고

- 레거시 문자열 ref 사용에 대한 경고
- 권장되지 않는 findDOMNode 사용에 대한 경고 : 자식 중 특정 엘리머트를 찾는 함수인데, 유지보수가 어려워 대신 ref를 사용하도록 권장함
- 예상치 못한 부작용 검사
- 레거시 context API 검사
- 재사용 가능한 상태 보장
