# External Store

## 학습 키워드

- 관심사의 분리
- Layered Architecture
- Flux Architecture
- useReducer
- useCallback

## 🍀 Separation of Concerns (관심사의 분리)

작은 부품이 모여서 하나의 시스템이 되듯, 작은 컴포넌트가 합쳐저 큰 컴포넌트가 됨

이때 컴포넌트를 나누는 기준을 관심사라고 하고, 이에 따라 구분하는 방법론의 종류가 다양해짐

나눠진 각 부분은 하나의 역할, 관심사로 격리됨으로써 복잡도가 낮아짐

### Layered Architecture

가장 흔하게 사용되며, 사용자와의 거리를 기준으로 나누는 **설계 방법론**

(아래는 가까운 순서대로 나열)

**Presentation Layer**

UI를 다루는 부분으로 사용자와 가장 가까움

- 사용자의 input을 받는 동작 수행
- `Controller`가 해당됨

**Business Layer**

비즈니스 기능을 담당하며, 어플리케이션의 중심

- `Service`, `Domain`가 해당됨

**Persistence Layer**

데이터를 관리하는 부분

- `DAQ`, `Repository`가 해당됨

### MVC

웹 서비스의 가장 대표적인 구성 패턴이자 **설계 방법론**

Model / View / Controller를 구분하여 기능을 분리하고 프로젝트 구조를 구성함

- bidirectional data flow (양방향 데이터 흐름)
    하나의 유저 인터렉션 발생 시 그 인터렉션으로 발생한 업데이트가 다른 연쇄 업데이트를 만들어낼 수 있음 ⇒ 업데이트의 근원을 추적하기 힘듬

**Model**

데이터에 접근하는 기능 or 데이터 그 자체

데이터의 읽기/쓰기는 Model를 통해서만 이루어져야함

**View**

전달된 데이터를 화면에 출력하는 기능

주로 Controller에 의해 데이터를 전달받고, 전달받은 데이터를 화면에 표시해주는 기능 담당

**Controller**

Model을 통해 데이터에 접근하여 처리 결과를 View로 전달하는 기능을 수행

웹 서비스에서는 주로 라우팅 함수가 해당 기능을 수행함

- 요청을 받아 데이터를 처리하고 응답하는 코드
- 데이터에 직접 접근 x

### IPO (Input → Process → Output)

위 세 단계로 코드를 나누고, 각자의 역할에만 충실하면 되는 **프로세스 구성 방식**

거대한 프로그램이 아니라도 이 3단계로 코드를 적절히 구분할 시 이해하고 유지보수하는데 크게 도움이 됨

하나의 Output은 다시 사용자에게 Input을 요청하게 되고, 일반적인 프로그램은 다음과 같이 계속 순환하는 구조가 됨

1. Input: 프로그램 시작
2. Process: 프로그램 초기화
3. Output: 사용자에게 초기 UI 보여주기
4. Input: 사용자의 입력
5. Process: 사용자의 입력에 따라 처리
6. Output: 처리 결과 보여주기
7. Input: 사용자의 또 다른 입력
8. …반복…

**MVC와 매핑하자면...**

MVC는 설계 방법론이고, 이는 프로세스 구성 방식이기에 완벽히 매핑되는 것은 아님

- Model → Process
- View → Output
- Controller → Input

## 🍀 Flux Architecture

> [Flux](https://facebook.github.io/flux/docs/in-depth-overview/)
> [Flux (한국어)](https://haruair.github.io/flux/docs/overview.html)

> [Redux의 핵심](https://ko.redux.js.org/tutorials/essentials/part-1-overview-concepts)

Facebook(현 Meta)에서 MVC의 대안으로 내세운 아키텍처

`unidirectional data flow`(단방향 데이터 흐름)를 강조

- MVC의 단점: 전통적인 MVC는 이를 지양하지만, 흔하게 bidirectional data flow (양방향 데이터 흐름)이 나타나게 되고 이는 Model-View의 관계를 복잡하게 함

### 구조

1. Action → 이벤트/메시지 같은 객체.
2. Dispatcher → (여러) Store로 Action을 전달. 메시지 브로커와 유사하다.
3. Store (여러 개) → 받은 Action에 따라 상태를 변경. 상태 변경을 알림.
4. View → Store의 상태를 반영.

Redux는 단일 Store를 사용함으로써 좀 더 단순한 그림을 제안한다.

1. Action
2. Store → dispatch를 통해 Action을 받고, 사용자가 정의한 reducer를 통해 State를 변경한다 (기존의 state 객체를 망가뜨리지 않고, 이에 새로운 값을 추가해서 만든 새로운 객체를 현재 state에 할당함)
3. View → State를 반영.

Action을 어떻게 표현하느냐가 사용성에 큰 차이를 만든다. 하지만 상태를 UI에 반영하는 방법은 모두 동일하다.

3단계 프로세스와 거칠게 매핑하면 다음과 같다.

- Input → Action + dispatch
- Process → reducer
- Output → View(React)

## External Store

> [forceUpdate와 같은 것이 있습니까?](https://ko.reactjs.org/docs/hooks-faq.html#is-there-something-like-forceupdate)
>

특별히 쓰이지 않는 상태라고 해도(React는 이걸 판단하기 어려움), “상태가 바뀌면” 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링한다.

```tsx
const [, setState] = useState({});
const forceUpdate = () => setState({});
```

커스텀 Hook으로 만들자.

```tsx
function useForceUpdate() {
 const [, setState] = useState({});
 return useCallback(() => setState({}), []);
}
```

이런 접근을 잘 하면, React가 UI를 담당하고, 순수한 TypeScript(또는 JavaScript)가 비즈니스 로직을 담당하는, 관심사의 분리(Separation of Concerns)를 명확히 할 수 있다. 자주 바뀌는 UI 요소에 대한 테스트 대신, 오래 유지되는(바뀌면 치명적인) 비즈니스 로직에 대한 테스트 코드를 작성해 유지보수에 도움이 되는 테스트 코드를 치밀하게 작성할 수 있다.
