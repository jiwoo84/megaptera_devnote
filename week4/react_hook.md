# React의 Hook

## 학습 키워드

- React Hook 이란
- Hooks
  - useState
  - useEffect
  - useContext
  - useRef
  - useLayoutEffect
- React StrictMode 란

## 🍀 React Hook의 역사

React 16.8버전부터 추가된 기능으로 기존 Class 바탕의 코드를 작성할 필요 없이 상태값과 여러 React 기능을 사용 가능하게 해줌

React를 쓰는 방식을 완전히 바꾼 변화의 핵심임

### 기존 방식의 문제점

**Wrapper Hell (HoC)**

컴포넌트를 재사용하기 위해 다른 컴포넌트로 감싸서 props로 내려주는 과정이 필요했음

→ 컴포넌트가 계속 래핑됨

**Huge Components**

상태를 가진 컴포넌트는 Class Component로 만들고, props만 사용하는 재사용이 용이한 작은 컴포넌트는 Function Component로 작성해서 구분함

→ 컴포넌트가 많고 복잡해짐

**Confusing Classes**

클래스 컴포넌트의 this가 어떤 것으로 식별되는지 혼동됨

### 현재 방식

**그냥 Function Component만 사용**

**상태 관리 유무를 바로 알기 어려움**

전보다 관리하기 어렵지만 전같이 신경쓰지 않아도 됨

- 관리가 어렵기 때문에 사이드이펙트를 생각해서 무분별한 최적화는 지양해야함

**복잡한 요소는 전부 Hook으로 격리 및 재사용 가능**

### 대표적인 Hooks

- useState → State Hook ⇒ React의 State
- useEffect ⇒ Side-effect
- useContext
- useRef
- useLayoutEffect → useEffect와 조금 다름.

## 🍀 useEffect

함수 컴포넌트에서 side effect를 수행해주는 훅

(side effect: 컴포넌트 렌더링 이후 비동기로 처리되어야 하는 부수적인 효과들)

렌더링 이후 일부 컴포넌트를 외부 시스템을 조작(원문은 '동기화'라고 표현)해야할 때 사용함

참고: [동기화](https://beta.reactjs.org/learn/synchronizing-with-effects), [사용법](https://ko.reactjs.org/docs/hooks-effect.html), [제작자가 쓴 useEffect 완벽 가이드](https://overreacted.io/ko/a-complete-guide-to-useeffect/)

- 외부 시스템과 연동해야하는 상황이 아니라면 굳이 쓰지 않아도 됨 [(You Might Not Need an Effect)](https://beta.reactjs.org/learn/you-might-not-need-an-effect)
- 의존성 배열을 통해 이펙트 실행 시점을 정할 수 있음
- 종료 처리: 함수를 리턴함

**사용되는 경우의 예**

- React 상태에 따라 비 React 컴포넌트를 제어할 때
- 컴포넌트가 화면에 나타날 때 분석 로그를 전송하고 싶을 때
- 컴포넌트 실행시 서버 연결을 설정해야할 때

### 종료 처리

useEffect가 설정된 컴포넌트가 있고, 어떤 조건에 의해 화면에 나타났다가 없어짐

→ 컴포넌트가 사라진다고 해서 생겼을 때 실행된 useEffect 안의 함수가 종료되는 것은 아님

> → 컴포넌트가 사라질 때(언마운트), useEffect 안의 함수도 종료하고 싶다면
>
> **useEffect에서 함수를 return하고 안에 종료하는 동작을 넣어준다**

**타이머 예제**

TimerControl의 Toggle 버튼을 누르고 Timer 컴포넌트를 없애면 `setInterval`이 멈출 것 같지만 멈추지 않는다

- 의도대로 동작하지 않는 예시

```jsx
function Timer() {
 useEffect(() => {
  setInterval(() => {
   document.title = `Now: ${new Date().getTime()}`;
  }, 100);
 });

 return (
  <p>Playing</p>
 );
}

  export default function TimerControl() {
  const [playing, setPlaying] = useState(false);
  
  const handleClick = () => {
    setPlaying(!playing);
  };

  return (
    <div>
    {playing ? (
      <Timer />
    ) : (
      <p>Stop</p>
    )}
    <button type="button" onClick={handleClick}>
      Toggle
    </button>
    </div>
  );
  }
  ```

- 올바른 예시

  useEffect의 return에 `clearInterval`로 `setInterval`을 종료하는 동작을 넣어준다

  ```jsx
  useEffect(() => {
  const savedTitle = document.title;

  const id = setInterval(() => {
    document.title = `Now: ${new Date().getTime()}`;
  }, 100);

  return () => {
    document.title = savedTitle; // 시작했던 숫자로 돌린 다음, 종료
    clearInterval(id);
  };
  });
  ```

### 처음에 한번만 실행하기

의존성 배열에서 아무 것도 지정하지 않으면 맨 처음에 딱 한번만 실행하게 할 수 있다.

주로 API를 호출해서 데이터를 얻을 때 사용한다.

```jsx
const [products, setProducts] = useState<Product[]>([]);

useEffect(() => {
 const fetchProducts = async () => {
  const url = 'http://localhost:3000/products';
  const response = await fetch(url);
  const data = await response.json();
  setProducts(data.products);
 };

 fetchProducts();
}, []);
```

**useEffect 내부에서 실행되는 함수는 내부에 써주자**

외부에 effect 함수를 선언한다면, 렉시컬 환경에 따라 선언 위치에 따라 함수의 스코프가 결정됨 => 선언시 상위에서 변수를 찾아 결과값에 적용됨

무조건 effect에서 사용할 함수는 내부에 적어주는 것이 좋음

```jsx
function SearchResults() {
  const [num, setNum] = useState(0);

  function fnc() {
    console.log(num); // 여기서 num은 0
  }

  function effectFnc() {
    setNum(1); // 1로 바꿨지만
    fnc(); // 콘솔에는 0이 찍힘
  }

  useEffect(() => {
    effectFnc();
  }, []);

  // ...
}

// 변경한 예시
function SearchResults() {
  const [num, setNum] = useState(0);

  function effectFnc() {
    function fnc() {
      console.log(num); 
    }
    setNum(1); 
    fnc(); // 콘솔에 1로 찍힘
  }

  useEffect(() => {
    effectFnc();
  }, []);

  // ...
}
```

### Effect가 두 번 실행되는 문제

<React.StrictMode>로 컴포넌트 전체를 감쌀 경우, 예상치 못한 Side Effect를 찾으려고 Effect 등을 두 번씩 실행함. 평소에는 큰 문제가 없지만, API 등을 사용하면 이상하다고 느낄 수 있으니 참고할 것.

> 아샬님은 사용하는 것을 추천!

**strictMode가 하는 일**

- 안전하지 않은 생명주기를 사용하는 컴포넌트 발견
- 레거시 문자열 ref 사용에 대한 경고
- 권장되지 않는 findDOMNode 사용에 대한 경고
- 예상치 못한 부작용 검사
- 레거시 context API 검사
- Ensuring reusable state

### 의존성 배열을 이용해 Fetch할 때 주의사항

어떤 값의 변경사항을 일회성으로 반영하고, 추가 변경사항에는 반응하지 않도록 할 때 boolean 플래그를 사용하면 됨 [(참고)](https://beta.reactjs.org/learn/synchronizing-with-effects#fetching-data)

```jsx
useEffect(() => {
  let ignore = false; // 변경사항 무시할지 말지 여부

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json); 
    }
  }

  startFetching();// 첫 userId 변경시에 실행됨

  return () => {
    ignore = true; // 한 번 처리되고 무시하도록 처리됨
  };
}, [userId]);
```
