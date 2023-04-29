# React Component

## 학습 키워드

- REST API 와 GraphQL
  - REST API 란 무엇인가
  - GraphQL은 왜 등장했는가?
  - REST API vs GraphQL
- JSON
- DSL(Domain-Specific Language)
- 선언형 프로그래밍
- 명령형 프로그래밍
- SRP(단일 책임 원칙)
- Atomic Design
- React component 와 props

참고 자료: [Thinking in React](https://beta.reactjs.org/learn/thinking-in-react)

## 🍀 1. UI를 컴포넌트 계층 구조로 나누기

앱의 모든 구성 요소를 컴포넌트로 나누어 이름을 지정하기

컴포넌트가 너무 커진다면 하위 컴포넌트로 나누기 => 부품(컴포넌트)를 만들어서 조립함

- 예시  
    <img src="https://react.dev/images/docs/s_thinking-in-react_ui_outline.png" width="400px" />

### 나누는 기준

- SRP(Single Responsibility Principle: 단일 책임 원칙) : 컴포넌트는 이상적으로 한 가지 일만 수행해야 하며, 늘어난다면 하위 컴포넌트로 분해할 것
- Information Architecture (JSON schema의 영향) : 실제로 많이 쓰고 자연스러운 SRP를 위해 사실상 강제됨
- CSS나 디자인 관점에서 클래스 생성자나 레이아웃에 맞게 분할

### Extract Function(함수 추출)

> [Extract Function](https://refactoring.com/catalog/extractFunction.html) / [Inline Function](https://refactoring.com/catalog/inlineFunction.html)

아주 흔히 쓰이는 SRP를 위한 수단으로 변화의 크기(영향 범위)를 제약함

일단 길게 코드를 작성하고, 적절히 자를 수 있는 부분이 보일 때 or 코드를 작성하기 어려운 상황에 직면했을 때 함수로 추출

컴포넌트 나누는 기준이 애매하면 다시 하나의 컴포넌트로 합쳤다가(Inline Method) 다시 나눠줘도 됨

### Atomic Design

우리가 잘 알고 있는 계층형 구조를 몇 가지 카테고리로 묶어서 추상화한 구조
[(참고자료)](https://bradfrost.com/blog/post/atomic-web-design/)

## 🍀 2. React의 정적 버전 빌드하기

인터렉션을 추가하지 않고 데이터 그대로 UI를 렌더링하는 정적 버전을 먼저 빌드

props를 사용하여 데이터를 전달하는 부분 먼저 구현 (아직 state는 X)

- 일반적으로 하향식이 쉽고, 대규모일수록 상향식이 쉬움

### props 타입 정의

props의 타입 정의는 적절하게 균형점을 잡아야하며, 이를 어떻게 하냐에 따라 Typescript의 장단점이 나타난다.

엄격한 타입이 가독성은 안 좋아보이지만, 전체적으로 코드를 파악할 때는 더 이해하기 쉽다.

**엄격한 타입 정의 (모든 속성에 적용)**

`장점`

- 코드 안정성 향상: 런타임 오류 방지
- 유지보수성 향상: 코드를 이해하는데 타입이 정의되어있는게 더 도움이 됨
- 개발 생산성 향상: 자동완성 기능 활용 가능
- 리팩토링 편의성: 변경 필요한 부분 빨리 찾을 수 있음
- 안정성 향상: 컴포넌트 간 타입 호환성 오류를 사전 방지 가능

`단점`

- 코드 가독성 떨어짐
- 유연성 감소: 다른 타입 데이터 전달 어려움
- 타입 오류 처리 시간 증가: 자잘한 타입 오류가 늘어나 해결하는데 시간이 걸림

**느슨한 타입 정의 (일부 속성 `any`로 처리)**

`장점`

- 개발 생산성 향상: 코드 작성 시간 감소, 타입 오류 빠르게 해결 가능
- 유연성 향상: 여러 타입의 데이터 전달 가능, 초기에는 느슨하게 하고 나중에 엄격하게 바꾸기도 함
- 가독성 향상

`단점`

- 디버깅이 어려워짐: 타입 오류를 빨리 발견하지 못할 수 있음
- 유지보수성 하락: 느슨한 타입이 가독성은 좋아 보이지만, 거대한 코드들을 파악하는데 타입이 제대로 명시되어 있지 않아서 어려움을 높임

## 🍀 REST API & GraphQL

대부분은 B/E에서 REST API 또는 GraphQL를 사용하여 JSON 형태의 데이터를 돌려주는 API를 제공함

### REST API

REST를 기반으로 서비스 API를 구현한 것

**REST**

- `Representational State Transfer` 의 약자 => 자원(resource)의 표현(representation) 에 의한 상태 전달
- HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고, HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미
- [참고자료](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)

### GraphQL

API 생성을 위한 쿼리 언어이자 서버측 런타임

- Graph 자료 구조 사용
- Query에서 얻고자 하는 데이터 지정 가능
- Query(Read), Mutation(Command: Create, Update, Delete), Subscription(Event) 사용해서 데이터 얻음

**등장 배경**

원하는 데이터를 받아오기 위해서 API를 여러 번 호출하고, 받아온 데이터를 조합해서 사용해야 함

=> 앱이 복잡해질수록 많은 호출 & 데이터 조합을 위한 순차적인 처리가 복잡해짐

=> 기존 API는 서버에서 정의한 데이터 구조만은 한 번에 하나씩 가져올 수 있기에 REST API나 다른 API 호출 방식은 이를 해결하지 못함 (클라이언트에서 근본적인 해결을 할 수 없음)

=> GraphQL의 등장
클라이언트에서 필요한 데이터에 대한 쿼리를 선언한 후, GraphQL로 넘기면 쿼리를 해석해 서버에서 필요한 데이터를 가져와서 반환함

**한계**

클라이언트와 서버 사이에서 데이터를 절차적으로 처리하려면 서버와 클라이언트 사이에 GraphQL이라는 Service Broker 레이어가 들어가야함

=> 기존 클라이언트에서 데이터를 조합하던 역할을 GraphQL이 해주는 것 뿐임

=> 클라이언트에서 하던 일을 모듈로 분리해서 백에게 넘긴 것과 같기 때문에 큰 서비스가 아니라면 오히려 서버의 일이 가중될 수도 있음

**장점**

큰 서비스라면 사용하는 것이 좋다.

유지보수, 확장성에 용이한 API를 구현 가능하여 반복되는 작업을 줄임

필요한 정보를 유연하게 받아올 수 있음

여러 번 요청을 한 번으로 줄일 수 있기에 서버 리소스 최적화 가능

### `REST API` vs `GraphQL`

|   | REST API | GraphQL |
|---|---|---|
| 전달받는 데이터 | 원하는 부분 선택 불가 | 클라이언트가 원하는 데이터만 받을 수 있음 |
| 전달 요청 | 비교적 간단 | 비교적 복잡 (원하는 데이터 적어야함) |
| API 생성 난이도| 비교적 어려움 | 비교적 간단함 |

## 🍀 명령형 vs 선언형 프로그래밍

### 명령형 프로그래밍

컴퓨터가 수행할 명령들을 이해하기 쉽게 순서대로 작성하는 방식

### 선언형 프로그래밍

방법이나 알고리즘은 숨기고 그것이 담긴 '무엇'을 선언하는 방식

많은 경우 선언형 프로그래밍 뒤에 명령형 프로그래밍이 존재함

상위 레벨에서 사용되기 때문에 코드의 재사용이 용이함

- DSL(Domain-specific language: 특정한 도메인을 적용하는데 특화된 컴퓨터 언어)의 형태로 자주 사용됨
- ex) javascript에서 `map`, `filter` 사용
