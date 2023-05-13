# TDD (Test Driven Development)

## 학습 키워드

- TDD란
- Jest
- Describe - Context - It 패턴
- 단위테스트란

## 🍀 테스트란?

프로그램을 실행하여 오류과 결함을 검증하고 애플리케이션이 요구사항에 맞게 동작하는지 검증하는 절차

### 테스트의 종류 (feat. 테스팅 트로피)

`Static 테스트` : 코드를 실행시키지 않고 하는 테스트

- 구문 오류, 나쁜 코드 스타일 등을 검증
- ex) ESlint, Typescript

`Unit 테스트` : 기능의 개별적인 단위(기능, 컴포넌트 단위)를 떼어 분리된 환경에서 테스트 하는 것

- 장점: 단위가 작아서 작성 비용이 낮고 실행속도가 빠름
- ex) jest

`Integration 테스트` : 어플의 여러 부분들이 통합되어 제대로 상호작용하는지 확인하는 테스트

- 주로 단위보다 큰 범위(페이지) 테스트
- ex) jest, RTL

`E2E 테스트` : 실제 사용자가 사용하는 것과 같은 조건에서 전체 시스템을 테스트

- API 서버, DB 등의 외부 서비스를 모두 사용하여 통합된 시스템 테스트
- 비용이 많이 들고 속도도 느림
- ex) Cypress, Selenium

### 프론트엔드의 테스트

결국 목적은 테스트 작성이 아닌 올바른 구현이니, 제한된 리소스로 테스팅 효율을 높이고 싶다면 Integration 테스트를 주로 작성하는 것이 좋음

`API 서버 통신`

- 브라우저: cypress 사용
- Node.js: testing-library에서 제공하는 `fireEvent`, `userEvent` 사용

`사용자 이벤트 처리` : 실제 API || mock API 사용 (jest로 구현 가능)

`시각적 요소`

- 스냅샷 테스트: html 구조가 의도한 대로 나타나는지 테스트 (jest, storybook)
- 시각적 회귀 테스트: html + css 컴포넌트가 실제로 브라우저에서 의도한대로 렌더링 되는지 확인 (storybook)

### 테스팅 환경 (그래서 뭐로 해야 돼?)

**브라우저**

- Node.js에 비해 무겁고 초기 구동속도가 느리다.
- 사용하기 위해 Headless같은 브라우저의 별도 설치가 필요함.
- ex> Karma, Selenium, Cypress
a

**Node.js**

- 설치 및 실행이 간단하고 속도가 빠르다.
- 모듈 단위로 테스트 가능
- DOM, BOM API가 없음 => 그래서 jsdom을 사용하지만 브라우저 동작을 백퍼 구현하진 못함
- ex) Jest, Mocha

> 결론: 브라우저는 크로스 브라우징 테스트가 '반드시' 필요하거나, 브라우저의 실제 동작 테스트가 필요한 경우에만 고려하고 왠만하면 Node.js 환경에서 해라

### 주의사항

1. 관심사 분리를 통해 UI / 비즈니스 로직 테스트 분리할 것
    - UI를 비즈니스 로직보다 수정이 잦기 때문에 큼직큼직하게 테스트 짤 것
2. 공통적으로 사용하는 부분을 꼼꼼하게 테스트 짜는 것이 중요함

## 🍀 TDD란?

테스트 코드를 먼저 작성하는, 즉 구현보다 인터페이스와 스펙을 먼저 정의함으로써 개발을 진행하는 방식

### TDD Cycle

**1. Red**

실패하는 테스트 코드를 작성. 인터페이스와 스펙에 집중한다.

**2. Green**

재빨리 테스트를 통과시킨다. 올바른 방법이 아니어도 괜찮다.

어렵다면 1로 돌아가 더 작고 쉬운 문제를 정의해야함

**3. Refactor**

리팩터링을 통해 코드를 올바르게 만듬 (가장 중요한 부분)

이를 위해 코드의 의도를 드러내고 중복을 찾아 제거하는 연습이 필요함

[예시: Jest를 이용한 간단한 TDD 예제](https://github.com/ahastudio/til/blob/main/jest/20201204-simple-tdd-example.md)

>이 과정들이 익숙하지 않으면 TDD를 하는 게 거의 불가능하고, 일반적인 개발 또는 클린 코드를 작성하는 것 또한 매우 힘듬

## 🍀 Jest

페이스북에서 만든 자바스크립트 프레임워크, 거의 모든 것을 갖추고 있어서 대세

- 최근에는 백엔드에서도 사용하는 추세
- Mocha와 Chai처럼 RSpec의 describe-it 지원, expect로 단언(assertion) 가능
- Mocking도 다양한 레벨에서 쉽게 사용 가능
- Jest 등장 전에는 여러 테스팅 라이브러리를 조합해서 사용했었음 (예로 Mocha나 Jasmin을 test runner로 사용하고, Chai나 Expect같은 Test Mathcer과 Sinon, Testdouble 같은 Test Mock 라이브러리도 필요했음)  
    => Jest = test runner + test mathcer + test mock (하나만 설치하면 모든 프레임워크 제공)
- <details>
    <summary><b>참고자료</b></summary>

  - [BETTER SPECS](https://www.betterspecs.org/) → RSpec 베스트 프랙티스 모음. 그대로 쓸 수는 없지만, 참고하자.
  - [Ginkgo - Go 언어 개발자를 위한 BDD 테스팅 프레임워크](https://youtu.be/gfTsSBRvdqI) (Go 언어 사례)
  - [JUnit5로 계층 구조의 테스트 코드 작성하기](https://johngrib.github.io/wiki/junit5-nested/) (Java 언어 사례)
  - [Let’s RSpec](https://github.com/ahastudio/til/blob/main/ruby/20161206-rspec-let.md) → Jest는 RSpec의 let 같은 걸 지원하지 않기 때문에, 핵심 아이디어를 가져와서 적당한 수준에서 잘 써야 한다.
  - [Given-When-Then](https://www.notion.so/Given-When-Then-c4b62b46710942a181a6d477e502e458)

</details>

### 테스트 실행

1. `파일명.test.ts` 같은 형태의 ts/ js/ tsx/ jsx 파일을 만든다
2. 실행 명령어 (script에 저장해서 사용하면 편함)

  ```bash
  # 테스트 실행
  npx jest

  # 저장할 때마다 테스트 실행
  npx jest --watchAll
  ```

- swc가 타입을 제외하고 테스팅하기 때문에 타입 검사는 따로 ts로 돌려야함  
  `npx tsc --noEmit`

### 작성 과정 템플릿 - `Given-When-Then`

테스트 코드 작성 방법 템플릿인 Given-When-Then에 따라 작성하는 것이 초반에는 쉬울 수 있음

1. `Given` : 어떤 요소가 갖춰지면 (동작에 필요한 요소)
2. `When` : 어떤 시간에, 어떤 것이 트리거가 되어
3. `Then` : 최종 동작을 한다

이 세 가지를 간단하게 정의하고, 디테일하게 작성하여 완성시키면 됨

> [예제가 나온 참고글](https://github.com/ahastudio/til/blob/main/blog/2018/12-08-given-when-then.md)

### 작성 방식

테스트 케이스를 정의할 때 크게 두 가지 방법을 사용한다:

**1.test 함수로 개별 테스트를 나열***

```jsx
describe("add(주어)", () => {
  it("returns sum of two numbers(결과)", () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

**2. BDD 스타일로 주체-행위 중심으로 테스트를 조직화**

테스트 대상과 행위를 명확히 드러내어 표현력이 좋아지고, 좀 더 고민할 기회를 제공

```jsx
describe('add(주어)', () => {
 it('returns sum of two numbers(결과)', () => {
    expect(add()).toBe(0);
    expect(add(1)).toBe(1);
    expect(add(1, 2)).toBe(3);
    expect(add(1, 2, 3)).toBe(6);
 });
});

// context로 조건문 만들어 다양한 상황 고려하기
// 위 코드보다 의도를 명확하게 보여줄 수 있음

// context가 제공되지 않아 describe로 만듬
const context = describe;

describe('add(주어)', () => {
 context('with no argument(조건)', () => {
  it('returns zero(결과)', () => {
   expect(add()).toBe(0);
  });
 });

 context('with only one number', () => {
  it('returns the same number', () => {
   expect(add(1)).toBe(1);
  });
 });

 context('with two numbers', () => {
  it('returns sum of two numbers', () => {
   expect(add(1, 2)).toBe(3);
  });
 });

 context('with three numbers', () => {
  it('returns sum of three numbers', () => {
   expect(add(1, 2, 3)).toBe(6);
  });
 });
});
```
