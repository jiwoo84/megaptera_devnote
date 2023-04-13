# 4. Testing Library

## 👽 테스트란?

프로그램을 실행하여 오류과 결함을 검증하고 애플리케이션이 요구사항에 맞게 동작하는지 검증하는 절차

### 테스트의 종류 (feat. 테스팅 트로피)

- Static 테스트  
  코드를 실행시키지 않고 하는 테스트

  - 구문 오류, 나쁜 코드 스타일 등을 검증
  - ex) ESlint, Typescript

- Unit 테스트  
  기능의 개별적인 단위(기능, 컴포넌트 단위)를 떼어 분리된 환경에서 테스트 하는 것

  - 장점: 단위가 작아서 작성 비용이 낮고 실행속도가 빠름
  - ex) jest

- Integration 테스트
  어플의 여러 부분들이 통합되어 제대로 상호작용하는지 확인하는 테스트

  - 주로 단위보다 큰 범위(페이지) 테스트
  - ex) jest, RTL

- E2E 테스트  
  실제 사용자가 사용하는 것과 같은 조건에서 전체 시스템을 테스트
  - API 서버, DB 등의 외부 서비스를 모두 사용하여 통합된 시스템 테스트
  - 비용이 많이 들고 속도도 느림
  - ex) Cypress, Selenium

### 프론트엔드의 테스트

결국 목적은 테스트 작성이 아닌 올바른 구현이니, 제한된 리소스로 테스팅 효율을 높이고 싶다면 Integration 테스트를 주로 작성하는 것이 좋음

- API 서버 통신
  - 브라우저: cypress 사용
  - Node.js: testing-library에서 제공하는 `fireEvent`, `userEvent` 사용
- 사용자 이벤트 처리: 실제 API || mock API 사용 (jest로 구현 가능)
- 시각적 요소
  - 스냅샷 테스트: html 구조가 의도한 대로 나타나는지 테스트 (jest, storybook)
  - 시각적 회귀 테스트: html + css 컴포넌트가 실제로 브라우저에서 의도한대로 렌더링 되는지 확인 (storybook)

### 테스팅 환경 (그래서 뭐로 해야 돼?)

- 브라우저
  - Node.js에 비해 무겁고 초기 구동속도가 느리다.
  - 사용하기 위해 Headless같은 브라우저의 별도 설치가 필요함.
  - ex> Karma, Selenium, Cypress
- Node.js

  - 설치 및 실행이 간단하고 속도가 빠르다.
  - 모듈 단위로 테스트 가능
  - DOM, BOM API가 없음 => 그래서 jsdom을 사용하지만 브라우저 동작을 백퍼 구현하진 못함
  - ex) Jest, Mocha

  > 결론: 브라우저는 크로스 브라우징 테스트가 '반드시' 필요하거나, 브라우저의 실제 동작 테스트가 필요한 경우에만 고려하고 왠만하면 Node.js 환경에서 해라

## 📌 jest란?

거의 모든 것을 갖춘 테스팅 도구 [(Jest 공식문서)](https://jestjs.io/)

- Mocha와 Chai처럼 RSpec의 describe-it 지원, expect로 단언(assertion) 가능
- Mocking도 다양한 레벨에서 쉽게 사용 가능 신

### 참고 자료

- [BETTER SPECS](https://www.betterspecs.org/) → RSpec 베스트 프랙티스 모음. 그대로 쓸 수는 없지만, 참고하자.
- [Ginkgo - Go 언어 개발자를 위한 BDD 테스팅 프레임워크](https://youtu.be/gfTsSBRvdqI) (Go 언어 사례)
- [JUnit5로 계층 구조의 테스트 코드 작성하기](https://johngrib.github.io/wiki/junit5-nested/) (Java 언어 사례)
- [Let’s RSpec](https://github.com/ahastudio/til/blob/main/ruby/20161206-rspec-let.md) → Jest는 RSpec의 let 같은 걸 지원하지 않기 때문에, 핵심 아이디어를 가져와서 적당한 수준에서 잘 써야 한다.
- [Given-When-Then](https://www.notion.so/Given-When-Then-c4b62b46710942a181a6d477e502e458)

## 📌 jest로 테스트하기

### 테스트 실행

- `파일명.test.ts` 같은 형태의 ts/ js/ tsx/ jsx 파일을 만든다
- 실행 명령어 (script에 저장해서 사용하면 편함)

  ```bash
  # 테스트 실행
  npx jest

  # 저장할 때마다 테스트 실행
  npx jest --watchAll
  ```

### 기본적인 테스트 코드

```jsx
test("add", () => {
  expect(add(1, 2)).toBe(3);
});
```

### BDD 스타일의 테스트 코드

표현력이 좋아지고, 좀 더 고민할 기회를 제공.

```jsx
describe("add(주어)", () => {
  it("returns sum of two numbers(설명)", () => {
    expect(add(1, 2)).toBe(3);
  });
});

// 조건 context 추가하기
let context = describe; // context가 제공되지 않아 describe로 구조를 만들어주는 것

describe("add(주어)", () => {
  context("두 수가 양수라면", () => {
    it("returns sum of two numbers(설명)", () => {
      expect(add(1, 2)).toBe(3);
    });
  });
});
```

## 📌 React Testing Library

UI 테스트에 특화된 라이브러리. 거의 E2E Test처럼 쓸 수 있다.

> 🚨 주의사항
> F/E 테스트 = only React 컴포넌트 테스트가 아님
> 본질에 집중해서 너무 많은 테스트 코드를 작성하지 말 것
> => 오히려 유지보수를 저해함

- 참고 자료
- [React Testing Library 공식문서](https://testing-library.com/docs/react-testing-library/intro)
- [jest-dom](https://testing-library.com/docs/ecosystem-jest-dom/)
- [프론트엔드(Front-end)도 테스트해야 하나요?](https://youtu.be/-kUmsKRmOnA)
- [Mocking 때문에 테스트 코드를 작성하기 어렵나요](https://youtu.be/RoQtNLl-Wko)

### 테스트 코드

```tsx
test("Greeting", () => {
  render(<Greeting name="world" />); // 해당 컴포넌트를 렌더링 시킴

  screen.getByText("Hello, world!"); // 문자열 그대로 존재해야 함

  screen.getByText(/Hello/); // 정규표현식 사용해서 일부만 존재해도 가능하게 함

  expect(screen.queryByText(/Hi/)).not.toBeInTheDocument();
});
```

- getBy vs queryBy
  엘리먼트 존재하지 않을 때, `getBy`는 에러 / `queryBy`는 에러가 나지 않음
