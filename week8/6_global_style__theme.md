# Global Style & Theme

## 학습 키워드

- Reset CSS
- `box-sizing` 속성
- `word-break` 속성
- Theme
- ThemeProvider

## 🍀 Global Style

### 참고 자료

아래 자료를 바탕으로 GlobalStyle을 만들어서 적용할 예정

- [createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle) : GlobalStyle 만드는 방식

- [대체 CSS box model](https://developer.mozilla.org/ko/docs/Learn/CSS/Building_blocks/The_box_model#대체_css_box_model) / [box-sizing](https://developer.mozilla.org/ko/docs/Web/CSS/box-sizing) : 박스 사이징 커스텀

- [The 62.5% Font Size Trick](https://www.aleksandrhovhannisyan.com/blog/62-5-percent-font-size-trick/): rem, px을 적절히 사용할 수 있도록 커스텀

- [word-break](https://developer.mozilla.org/ko/docs/Web/CSS/word-break) : 언어에 따라 띄어쓰기, 줄넘김 설정

### 전역 스타일 만들기

```tsx
// src/styles/GlobalStyle
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
 html {
  box-sizing: border-box;
 }
 
 *,
 *::before,
 *::after {
  box-sizing: inherit;
 }
 
 html {
  font-size: 62.5%;
 }
 
 body {
  font-size: 1.6rem;
 }
 
 :lang(ko) {
  h1, h2, h3 {
   word-break: keep-all;
  }
 }
`;

export default GlobalStyle;
```

**설명**

```tsx
const GlobalStyle = createGlobalStyle`

// 가로,세로 길이가 테두리 기준으로 되도록 설정
 html {
  box-sizing: border-box;
 }
 
 // 민약 위 설정 바꾼다면 모두 바뀜
 *,
 *::before,
 *::after {
  box-sizing: inherit;
 }
 

// 1rem = 10px이 됨 (rem은 font-size로 결정됨)
// 유저가 폰트 크기를 바꾸면 rem을 사용한 모든 것의 크기가 변경됨
// 크기를 키우면 다 커지는게 맞기 때문에 이렇게 설정
 html {
  font-size: 62.5%;
 }


 // 기본 폰트 사이즈를 16px로 설정 
 body {
  font-size: 1.6rem;
 }
 
 // 한글은 띄어쓰기가 있어서 문장이 끊기지 않도록 설정
 :lang(ko) {
  h1, h2, h3 {
   word-break: keep-all;
  }
 }
`;
```

### App 컴포넌트에서 사용하기

```tsx
// src/App
import { Reset } from 'styled-reset';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
 return (
  <>
   <Reset />
   <GlobalStyle />
   <Greeting />
  </>
 );
}
```

## 🍀 Theme

특정 테마를 만들어 전체적으로 적용할 수 있어서 디자인 시스템의 근간을 마련하는데 활용 가능

ex) 다크 모드 / 밝은 모드를 각각 테마로 만들어서 적용 (눈에 보이는 단편적인 정보를 넘어서, “의미”에 집중할 수 있게 됨)

- 참고
  - [Theming](https://styled-components.com/docs/advanced#theming)
  - [Create a declarations file](https://styled-components.com/docs/api#create-a-declarations-file)

### 기본 Theme 생성 및 적용

**기본 Theme 생성**

테마의 요소명은 의미를 담아야 함

만약에 색깔이라면 단순히 blue, red가 아닌 아래와 같이 어디에 쓰이는지 의미를 담아서 해놓아야 진짜 테마를 유용하게 사용할 수 있음

- 참고
  - <https://code.visualstudio.com/api/references/theme-color>
  - <https://getbootstrap.com/docs/5.3/customize/color/>

```tsx
const defaultTheme = {
 colors: {
  background: '#FFF',
  text: '#000',
  primary: '#F00',
  secondary: '#00F',
 },
};

export default defaultTheme;
```

**기본 Theme의 타입 생성**

기본 Theme을 확장해서 다른 Theme를 만들거나, 하위 컴포넌트에서 `props.theme`를 사용하면서 타입을 지정할 때 기본 Theme의 타입이 필요함

따로 만들어놓으면 편함

```tsx
import defaultTheme from './defaultTheme';

type Theme = typeof defaultTheme;

export default Theme;
```

**App 컴포넌트에서 사용하여 테마 지정하기**

```tsx
import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
 return (
  <ThemeProvider theme={defaultTheme}>
   <Reset />
   <GlobalStyle />
   <Greeting />
  </ThemeProvider>
 );
}
```

### `props.theme` 사용해서 테마의 요소 받아오기

ThemeProvider로 theme를 적용하여 감싸게 되면 하위 모든 태그에서 theme 객체를 `props.theme`으로 받아서 사용할 수 있음

```tsx
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
 body {
  background: ${(props) => props.theme.colors.background};
  color: ${(props) => props.theme.colors.text};
 }
 
 a {
  color: ${(props) => props.theme.colors.text};
 }
 
 button,
 input,
 select,
 textarea {
  background: ${(props) => props.theme.colors.background};
  color: ${(props) => props.theme.colors.text};
 }
`;

export default GlobalStyle;
```

**`props.theme` 타입 지정하기**

타입 지정이 되지 않으니 `props.theme`을 쳐도 사용 가능한 키값이 자동완성되지 않아 불편함

=> 문제를 해결하기 위해 `styled.d.ts` 파일을 작성

```tsx
// src/styled/styled.d.ts
import 'styled-components';

declare module 'styled-components' {
 export interface DefaultTheme extends Theme {
  colors: {
   background: string;
   text: string;
   primary: string;
   secondary: string;
  }
 }
}

// 위에서 DefaultTheme의 타입을 만들었다면
import 'styled-components';

import Theme from './Theme';

declare module 'styled-components' {
 export interface DefaultTheme extends Theme {}
}
```

### 다른 Theme 생성

다른 theme을 추가할 때 Theme 타입을 사용

항상 defaultTheme에 먼저 항목을 추가/삭제하고, 나머지를 여기에 맞추면 된다.

```tsx
import Theme from './Theme';

const darkTheme: Theme = {
 colors: {
  background: '#000',
  text: '#FFF',
  primary: '#F00',
  secondary: '#00F',
 },
};

export default darkTheme;
```

### 상태에 따라 테마 변경하기

예시로 다크모드, 밝은모드 변경하기

isDarkMode를 props로 받아서 스타일링에 적용시킨다면 테마에 따라 해당 컴포넌트의 스타일도 변경시킬 수 있음

```tsx
import { useDarkMode } from 'usehooks-ts';

import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';
import darkTheme from './styles/darkTheme';

import GlobalStyle from './styles/GlobalStyle';

import Greeting from './components/Greeting';
import Button from './components/Button';

export default function App() {
const { isDarkMode, toggle } = useDarkMode();

const theme = isDarkMode ? darkTheme : defaultTheme;

return (
 <ThemeProvider theme={theme}>
  <Reset />
  <GlobalStyle />
  <Greeting />
  <Button onClick={toggle}> // 버튼을 누르면 설정한대로 테마가 변경됨
   Dark Theme Toggle
  </Button>
 </ThemeProvider>
 );
}
```

## 테스트 “window.matchMedia” 에러

Jest 테스트 쪽에서 “window.matchMedia” 문제 발생

=> `src/setupTests.ts` 파일에 공식 문서에 나온 코드를 넣으면 해결된다.
참고: [Mocking methods which are not implemented in JSDOM](https://jestjs.io/docs/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)

```tsx
// src/setupTests.ts
Object.defineProperty(window, 'matchMedia', {
 writable: true,
 value: jest.fn().mockImplementation((query) => ({
  matches: false,
  media: query,
  onchange: null,
  addListener: jest.fn(), // deprecated
  removeListener: jest.fn(), // deprecated
  addEventListener: jest.fn(),
  removeEventListener: jest.fn(),
  dispatchEvent: jest.fn(),
 })),
});
```
