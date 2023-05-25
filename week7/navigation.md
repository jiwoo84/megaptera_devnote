# Navigation

## 학습 키워드

- Web APIs - History
- React Router - NavLink, Link, Navigate, useNavigate

## 🍀 링크

### History.pushState

HTML5에서 기본 제공하는 기능으로 페이지 갱신 없이 주소만 바꿔줌

```tsx
const state = {}; // 새로운 세션 기록 항목에 연결할 상태 객체
const title = '';
const url = '/about';

history.pushState(state, title, url); // url로 이동하지만 페이지 갱신은 없음
```

### Link (주로 사용)

react-router-dom에서 제공, 클릭시 url로 이동하는 컴포넌트

이동하면서 전체 페이지를 갱신하지 않고 필요한 부분만 재렌더링함

- 안에 content를 넣으면 <a>로 래핑해서 클릭가능한 링크로 만듬
- 필요한 부분만 재렌더링함
- 정보를 가져올 때만 사용 (삭제할 때는 절대 사용x)

**속성**

- `to="path"` : 해당 path로 이동 (location object or 함수도 넣기 가능)
- `replace={true}` : 추가하면 이동 전 경로가 남지 않게 이동

```tsx
function Header() {

return (
 <header>
  <nav>
   <ul>
    <li><Link to="/">Home</Link></li>
    <li><Link to="/about">About</Link></li>
   </ul>
  </nav>
 </header>
 );
}
```

### NavLink

`Link`와 비슷하지만 선택된 항목을 표시하거나 스크린 리더 기술에 대한 유용한 기능을 추가로 제공함

- 클릭한 링크의 class에 `active`가 들어감

```tsx
function Header() {
 return (
  <header>
   <nav>
    <ul>
     <li><NavLink to="/">Home</NavLink></li>
     <li><NavLink to="/about">About</NavLink></li>
    </ul>
   </nav>
  </header>
 );
}
```

## 🍀 리다이렉트

### Navigate

렌더링 시 연결된 url로 연결되며 즉시 리다이렉트 해주는 컴포넌트

```tsx
import { Navigate } from 'react-router-dom';

// LoginPage 컴포넌트로 연결되면 즉시 '/'로 이동함
export default function LoginPage() {
 return (
  <Navigate to="/" />
 );
}
```

**테스트코드 작성**

> 🚨 테스트 에러 "Request is not defined" 발생시
>
> 1. whatwg-fetch 설치
> 2. jest.config.js의 setupFilesAfterEnv에 `'<rootDir>/src/setupTests.ts',` 추가
> 3. setupTest 파일 상단에 `import 'whatwg-fetch'`

```tsx
describe("App", () => {

    function renderRouter(path: string) {
        const router = createMemoryRouter(routes, { initialEntries: [path]});
        render(<RouterProvider router={router} />);
    }

    context('when the current path is "/logout"', () => {
        it('redirects to the home', () => {
            renderRouter('/logout');

            screen.getByText(/Welcome/);
        })
    })
})
```

### useNavigate (주로 사용)

react-router-dom에서 제공하는 hook으로 실행시 리다이렉트

```tsx
import { SyntheticEvent } from "react";
import { Link,  useNavigate } from "react-router-dom";

export default function Header() {
    const navigate = useNavigate();

    const handleClickLogout = (event: SyntheticEvent) => {
        navigate('/')
    }

    // Logout 버튼 클릭시 '/'로 이동
    return (
        <header>
            <button type="button" onClick={handleClickLogout}>
                Logout
            </button>
        </header>
    )
}
```
