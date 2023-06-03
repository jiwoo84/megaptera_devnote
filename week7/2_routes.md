# Routes

## 학습 키워드

- 라우터란?
- React Router
  - Browser Router
  - Route
  - Memory Router

## 🍀 React Router

클라이언트 측에서 라우팅을 가능하게 하는 라이브러리

페이지 이동시 앱이 서버에 문서를 다시 요청하지 않고 링크 클릭에서 URL을 업데이트함

대신 데이터 요청을 만들어 변경된 UI와 데이터를 받아와 리렌더링함

페이지 이동에 있어 빠른 사용자 경험을 제공 가능

**라우팅이란?**

사용자가 요청한 URL에 따라 맞는 페이지를 보여주는 것

라우터는 이를 도와주는 도구

 BrowserRouter > Routes > Route 형태로 감싸 return해주면 라우팅 완성

### BrowserRouter

브라우저에서 동작하게 하는 라우터로 라우터의 최상위 부모

HTML5 의 History API 를 사용하여 , UI와 URL의 싱크를 맞추는 역할

### Routes

여러 Route를 감싸고, 위에서부터 URL이 맞는 라우터를 하나 선택하여 렌더링 함

- 매칭되는 Route 가 없으면 아무것도 보여주지 않음
- 정확히 일치되는 url만 매칭됨

### Route

path와 컴포넌트를 매칭하는 라우트

- Route로 렌더링 되는 최상위 컴포넌트는 match, location, history를 prop으로 받음
- path와 정확히 일치해야 매칭됨
- 하위경로에 여러 라우팅을 매칭시키고 싶다면 url 뒤에 `*` 붙이기

**예시**

BrowserRouter는 테스트환경에서 작동하지 않으니

테스트도 따로 처리하기 위해 App파일과 분리해 main에 적용

```tsx
// src/App.tsx
import { Routes, Route } from 'react-router-dom';

function App() {
 return (
  <div>
   <Header />
   <main>
    <Routes>
     <Route path="/" element={<HomePage />} />
     <Route path="/about" element={<AboutPage info="hi"/>} />
    </Routes>
   </main>
   <Footer />
  </div>
 );
}

// src/main.tsx
import { BrowserRouter } from 'react-router-dom';

function main() {
  const element = document.getElementById('root');
  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);
  root.render((
    <React.StrictMode>
      <BrowserRouter>
      <App />
      </BrowserRouter>
    </React.StrictMode>
  ));
}

```

### MemoryRouter

테스트 코드에선 사용하는 라우터

**기본 사용 형태**

```tsx
import { MemoryRouter } from "react-router-dom"

import { render } from "@testing-library/react"

import App from "./App"

test("App", () => {
    render((
        <MemoryRouter initialEntries={['/']}> // 초기 주소값을 입력함
            <App />
        </MemoryRouter>
    ))
})
```

**페이지 별로 테스트 진행하기**

```tsx
const context = describe;

describe("App", () => {
    context('when the current path is "/"', () => {
        it('renders the home page', () => {
            render((
                <MemoryRouter initialEntries={['/']}>
                    <App />
                </MemoryRouter>
            ));

            screen.getByText(/welcome/);
        })
    })

    context('when the current path is "/about"', () => {
        it('renders the about page', () => {
            render((
                <MemoryRouter initialEntries={['/about']}>
                    <App />
                </MemoryRouter>
            ))
        })
    })
})
```
