# React Testing Library

## 학습 키워드

- React Testing Library
- given - when - then 패턴
- Mocking
- Test fixture

## 🍀 React Testing Library란?

React 컴포넌트를 사용자 입장에 가깝게 테스트할 수 있는 도구

UI 테스트에 특화된 라이브러리. 거의 E2E Test처럼 쓸 수 있다.

> 🚨 주의사항
>
> F/E 테스트 = only React 컴포넌트 테스트가 아님
>
> 너무 많은 테스트 코드를 작성하지 말 것
>
> => 오히려 유지보수를 저해함

**참고자료**

- [React Testing Library 공식문서](https://testing-library.com/docs/react-testing-library/intro)
- [jest-dom](https://testing-library.com/docs/ecosystem-jest-dom/)
- [프론트엔드(Front-end)도 테스트해야 하나요?](https://youtu.be/-kUmsKRmOnA)
- [Mocking 때문에 테스트 코드를 작성하기 어렵나요](https://youtu.be/RoQtNLl-Wko)

## 🍀 jest + react testing library 작성 방법

[공식문서 참고](https://testing-library.com/docs/)

**react testing library 쿼리함수**

없으면 에러를 발생시키는 `getBy`를 주로 사용함

- `getBy`

  쿼리에 대해 일치하는 노드를 반환 (실패시 오류 발생)

  둘 이상의 요소가 예상되는 경우 getAllBy 사용

- `queryBy`

  쿼리에 대해 일치하는 노드를 반환 (요소가 없으면 null 반환해서 에러 발생 X)

  둘 이상의 요소가 예상되는 경우 queryAllBy 사용

- `findBy` (getBy + waitFor)

  주어진 쿼리와 일치하는 요소가 발견되면 Promise를 반환

  기본 제한 시간 이내에 발견하지 못하면 reject

  둘 이상의 요소를 찾아야 하는 경우 findAllBy 사용

- `waitFor`

  일정 기간동안 기다려야 할 때 waitFor을 사용하여 통과할 때까지 대기함

### 1.test 함수로 개별 테스트를 나열

```jsx
// src/components/Greeting.test.tsx
test("Greeting", () => {
  render(<Greeting name="world" />); // 해당 컴포넌트를 렌더링 시킴

  screen.getByText("Hello, world!"); // 문자열 그대로 존재해야 함

  screen.getByText(/Hello/); // 정규표현식 사용해서 일부만 존재해도 가능하게 함

  expect(screen.queryByText(/Hi/)).not.toBeInTheDocument();
});

// src/components/TextField.test.tsx
import { render, screen } from '@testing-library/react';

import TextField from './TextField';

test('TextField', () => {
  // given
 const label = 'Name';
const text = 'Tester'

 const setText = () => {
  // do nothing...
 };

  // when
  render(( // testing-library에서 제공하는 render
    <TextField
    lable={label}
        placeholder='input your name'
        text={text}
        setText={setText}
    />
  ));

  // then
  // 해당 label과 연결된 input을 찾음 / 없으면 에러남
  screen.getByLabelText('name');
  // value로 값을 가진 input, textarea, select를 찾음
  screen.getByDisplayValue('tester');
  // plalceholder 안의 값 있나 찾기 (정규표현식으로 일부만 찾기 가능)
  screen.getByPlaceholderText(/input your name/)
});
```

### 2. BDD 스타일로 주체-행위 중심으로 테스트를 조직화

```jsx
import { fireEvent, render, screen } from '@testing-library/react';

import TextField from './TextField';

const context = describe;

describe('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester'

  const setText = jest.fn()

  // 각 테스터가 실행되기 전에 이 함수를 실행함
  beforeEach(() => {
    // setText의 모든 정보 삭제
    // -> setText를 한 번 실행하면 실행된 것으로 처리되어
    //    모든 setText를 사용한 테스트코드에 영향을 미치기 떄문에
    //    관련 정보를 모두 삭제해주는 코드
    setText.mockClear();
    // 아니면 이것도 상관 없음. 둘 중 하나만 쓸 것.
    // 모든 mock 함수의 정보를 지움 -> 모든 mock 함수에 대해 .mockClear()를 실행한 것과 같음
    jest.clearAllMocks();
  })

  function renderTextField() {
    // when
    render((
        <TextField
            label={label}
            placeholder='input your name'
            text={text}
            setText={setText}
        />
    ));
  }

  it('renders elements', () => {
    // when
    renderTextField();

    // then
      // 해당 label과 연결된 input을 찾음 / 없으면 에러남
    screen.getByLabelText('Name');
    // value로 값을 가진 input, textarea, select를 찾음
    screen.getByDisplayValue('Tester');
    // plalceholder 안의 값 있나 찾기 (정규표현식으로 일부만 찾기 가능)
    screen.getByPlaceholderText(/input your name/)
  })

  context('when user enters name', () => {
    it('calls "setText" handler', () => {
      // given
      // render 후에 다음 검사들이 진행되야하니 적어줌
      // 의도를 명확히 하려면 beforeEach로 감싸줘도 됨
      renderTextField();

      // when
      // input 요소의 change 이벤트 상황 -> e.target의 value에 텍스트가 들어갔을 상황 가정
      fireEvent.change(screen.getByLabelText(label), {
          target: {value: 'new name'},
      })

      // then
      // 값이 호출됐는지 여부 체크
      expect(setText).toBeCalled();
      // 특정 인수를 사용하여 함수 호출 여부 확인
      expect(setText).toBeCalledWith('new name');
    })
  })
})
```

반복되는 코드를 Extract Function하고, fireEvent 등을 통해 인터랙션만 검증한다. 만약 복잡한 로직이 컴포넌트로부터 분리된다면, 여기서는 이것만 검증하면 된다.

만약 외부 의존성이 큰 코드를 작성한다면, 해당 부분만 가짜로 구현할 수 있다.

## 🍀 mocking하기

- Mock: 가짜

테스트를 수행할 모듈과 연결되는 서버나 다른 모듈을 사용하지 않고 이들을 흉내내는 가짜 모듈을 작성하여 테스트의 효율성을 높임 (함수의 실행 결과를 가짜로 만들어서 넣기)

일반 함수는 `jest.fn()` / 모듈은 `jest.mock()` 사용

**함수 vs 모듈**

- `함수`: 코드를 구성하는 하나의 독립적인 블록  
- `모듈`: 함수, 클래스, 변수를 그룹으로 정의한 것, 함수보다 큰 개념

**`jest.fn()` vs `jest.mock()`**

- `jest.fn()`: 함수의 모의버전을 만들어 호출 여부, 호출 횟수, 전달된 매개변수 등을 추적 가능
- `jest.mock()`: 모듈의 가짜(mock) 버전을 만들어서 의존하는 다른 모듈이나 리소스를 사용하지 않고 동작 확인 가능 (서버와 통신하는 경우 실제로 네트워크 호출은 수행하지 않으면서 반환되는 값 확인 가능)

### `jest.mock()`

`jest.mock(path, moduleFactory)`

모듈 팩토리는 모의 객체를 반환하는 함수 => 함수를 반환하는 함수(고차 함수)

> 위 동작은 서버 호출시 반환되는 값을 가짜로 만들어서 반환하는 모킹 함수임
>
> 그래서 생성자 함수를 모방하는데, 그러려면 모듈 팩토리(두 번째 인수)가 생성자 함수를 반환해야 함
>
> 즉, 모듈 팩토리는 함수를 반환하는 함수 = 고차 함수가 되어야 함

```jsx
import { render, screen } from '@testing-library/react';

import App from './App';

// 해당 경로의 모듈을 호출하고, 내부 함수에 의해 서버와 통신하면 안의 함수가 반환하는 값이 반환됨
jest.mock('./hooks/useFetchProducts', () => () => [
 {
  category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
 },
]);

test('App', () => {
 render(<App />);

// 위에 반환된 값에 Apple이 있기에 통과, grape이라면 통과 아님
// 실제 반환되는 값에는 grape이 있어도 모킹을 통해 반환값을 정해놨기 때문에 통과 안됨
 screen.getByText('Apple');
});
```

### Test Fixture

중복되는 무언가를 한 곳에 고정시켜 관리하는 방식, 개념

유닛 테스트의 Fixture(고정되어 있는 물체) = 테스트 실행을 위해 베이스라인으로서 사용되는 객체들의 고정된 상태

객체의 재사용, 고정된 환경에서의 테스트를 보장할 목적으로 사용

**예시**

위의 코드의 mocking한 객체를 Test Fixture 개념으로 정리한 코드

> mocking 하는 hook 생성
>
> 1. hook 폴더 안에 `__mock__` 폴더 생성 (이름 꼭 지키기)
>
> 2. hook의 파일명과 똑같이 `__mock__` 폴더 안에 파일 생성
>
> 3. 해당 파일 안에서 `jest.fn()`을 사용하여 베이스 객체 모킹
>
> 4. 테스트 코드 안에서 `./hook/파일명`의 훅을 실행하면 `__mock__` 폴더로 지정하지 않아도 서버를 호출하는 것이 아니라 베이스 객체가 모킹되어 들어옴

```jsx
// react-app/fixtures/product.ts
// 모킹 함수가 반환할 베이스 객체를 생성
const products = [
  {
    id: '1',
    category: '중식',
    name: '메가반점',
    menu: [
      { id: '1', name: '짜장면', price: 8000 },
      { id: '2', name: '짬뽕', price: 8000 },
    ],
  },
];

export default products;

// react-app/fixtures/index.ts
// 이렇게 하면 fixtures.products 같이 불러오기 가능
import products from './products';

export default {
  products,
};

// react-app/src/hook/__mocks__/useFetchProducts
import fixtures from '../../../fixtures';

// 베이스 객체를 반환하는 함수를 모킹하는 함수
const useFetchRestaurants = jest.fn(() => fixtures.products);

export default useFetchRestaurants;

// src/App.test.tsx
import { render, screen } from '@testing-library/react';

import App from './App';

import fixtures from '../fixtures';

// __mock__ 폴더 안의 함수가 모킹되어 베이스 객체가 반환된 형태가 됨
jest.mock('./hooks/useFetchRestaurants');

// 만약 __mock__ 폴더를 만들지 않았다면 이렇게 쓸 수도 있음
jest.mock('./hooks/useFetchRestaurants', () => () => fixtures.products);

test('App ', () => {
  render(<App />);

  screen.getByText('메가반점');
  // 실제 서버 호출값에는 메리김밥이 있지만, 모킹된 베이스 객체에 없기에 통과 안됨
  screen.getByText('메리김밥');
});

```
