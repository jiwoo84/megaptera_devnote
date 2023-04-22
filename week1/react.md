# 3. React

**참고자료**

- [**React 공식문서**](https://ko.reactjs.org/)
- [**React Beta 문서**](https://beta.reactjs.org/) : 먼저 보기
- [Thinking in React](https://beta.reactjs.org/learn/thinking-in-react) : React로 작업하는 프로세스 참고. “상태”를 골라내는 게 핵심
- [예전 문서의 설명](https://ko.reactjs.org/docs/thinking-in-react.html) : 한국어로 써있어서 읽기 쉬움, 살짝 참고만 하고 코드 참고 x
- [React 코어 개발자가 쓴 React에 대한 이해를 돕는 글](https://overreacted.io/ko/react-as-a-ui-runtime/) : 필독!

## 🍀 렌더링

```tsx
function Greeting() {
  return <p>Hello, world!</p>;
}

function main() {
  const element = document.getElementById("root");

  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);

  root.render(<Greeting />);
}

main();
```

- 여러 개의 Root를 만들거나, 여러 번 render 가능

- 여러 번 render하는 카운터 예제. 변경된 부분만 업데이트하기 때문에 하단 입력 컨트롤에 입력한 값이 그대로 유지된다.

- [updating-a-root-component](https://beta.reactjs.org/reference/react-dom/client/createRoot#updating-a-root-component)

- 참고 자료
  - [createRoot](https://beta.reactjs.org/reference/react-dom/client/createRoot)
  - (한국어) [예전 문서](https://ko.reactjs.org/docs/react-dom-client.html#createroot)

### 리렌더링

- [React는 컴포넌트를 언제 다시 리렌더링 할까요?](https://velog.io/@surim014/react-rerender)
- [왜 리액트에서 리렌더링이 발생하는가.](https://medium.com/@yujso66/%EB%B2%88%EC%97%AD-%EC%99%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C-%EB%A6%AC%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94%EA%B0%80-74dd239b0063)
- [React 렌더링 동작에 대한 (거의) 완벽한 가이드](https://velog.io/@superlipbalm/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior)

## 🍀 React는 라이브러리 or 프레임워크?

[React 개발자의 답변](https://twitter.com/trueadm/status/1194567962784653312)

IoC [(IoC: Inversion of Control 제어의 역전)](https://martinfowler.com/bliki/InversionOfControl.html)는 Framework의 주요한 특징  
React는 IoC를 통해 상태와 업데이트가 얽힌 복잡한 상황을 간단히 선언형 UI로 구성함  
=> 이런 면을 보면 프레임워크에 가까움

하지만 일반적으로는 IoC는 IoC 컨테이너와 엮여서 DI와 동의어처럼 쓰이고, Next.js, Remix 같은 걸 Framework이라고 부름

> 결론적으로 리액트는 프레임워크와 라이브러리, 둘 다의 속성을 가지고 있어서 딱 잘라 말하기 어려움
