# Router

## 🍀 createBrowserRouter

전에는 BrowserRouter > Routes > Route 이렇게 감싸는 태그 뭉치로 라우터 구현

React Router 버전 6.4부터 라우터 객체를 만들어서 사용하는 방식을 지원함

라우팅 정보만 별도의 파일로 관리 가능

### 기본 사용법

1. 라우트 생성: `const routes = [{ path: '/경로', element: <HomePage />}, ...]`
2. 라우터 생성: `createBrowserRouter(routes)`
3. 라우팅: `<RouterProvider router={router} />`

```tsx
// src/App.tsx

// 라우트 생성
const routes = [
  { path: '/', element: <HomePage /> },
  { path: '/about', element: <AboutPage /> },
];

// 라우터 생성
const router = createBrowserRouter(routes);

// 라우팅
export default function App() {
  return (
    <RouterProvider router={router} />
  )
}

// src/main.tsx
// 위 내용 App으로 분리해도 되고, main 안에 써도 됨
function main() {
  const element = document.getElementById('root');
  if (!element) {
    return;
  }

  const root = ReactDOM.createRoot(element);
  root.render((
    <React.StrictMode>
      <App />
    </React.StrictMode>
  ));
}

main();

```

### Outlet 사용하여 레이아웃 만들기

header나 footer을 고정적으로 놓고, 안의 페이지만 바뀌는 것 같이 레이아웃을 잡아주기

라우터를 따로 분리해서 바로 main에 라우팅 해주는 것도 깔끔함

```tsx
// src/routes.tsx
import { Outlet } from 'react-router-dom';

function Layout() { // Layout만 따로 분리할 수도 있음
 return (
  <div>
   <Header />
   <Outlet />
   <Footer />
  </div>
 );
}

const routes = [
 {
  element: <Layout />,
  children: [
   { path: '/', element: <HomePage /> },
   { path: '/about', element: <AboutPage /> },
  ],
 },
];

export default routes;

// src/main.tsx
// App 컴포넌트를 거치지 않고 바로 브라우저 라우터 만들어서 사용
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import routes from './routes';

const router = createBrowserRouter(routes);

root.render((
 <React.StrictMode>
  <RouterProvider router={router} />
 </React.StrictMode>
));
```

## 🍀 routes 테스트하기

App의 영역이 줄어서 이를 테스트하는 것은 의미없기 때문에 routes파일을 테스트함

### createMemoryRouter

`const router = createMemoryRouter(routes, { initialEntries: ['/path']});`

테스트 환경에서 사용 가능한 라우터를 만드는 도구

initialEntires에 입력한 url로 초기 라우팅 시킬 수 있음

```tsx
describe("App", () => {

  // 1. 라우팅을 공통으로 하고, 실행시마다 라우터를 초기화하고 싶다면 아래와 같이 설정
  // let router;

  // beforeEach(() => {
  //    const router = createMemoryRouter(routes, { initialEntries: [path]});
  //     router = createMemoryRouter(routes);
  // })

  // 2. 테스트마다 라우팅을 다른 경로로 해야 한다면, 이와 같이 함수를 만들어서 실행
  function renderRouter(path: string) {
      const router = createMemoryRouter(routes, { initialEntries: [path]});
      render(<RouterProvider router={router} />);
  }

  context('when the current path is "/"', () => {
      it('renders the home page', () => {
          renderRouter('/');

          screen.getByText(/Welcome/);
      })
  })

  context('when the current path is "/about"', () => {
      it('renders the about page', () => {
          renderRouter('/about');

          screen.getByText(/test/);
      })
})
})

```
