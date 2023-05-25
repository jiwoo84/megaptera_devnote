# Routing

## 학습 키워드

- HTML DOM API
  - Location
  - pathname

## 🍀 location

현재 브라우저의 위치에 대한 정보를 얻을 수 있는 객체

- [Window.location](https://developer.mozilla.org/ko/docs/Web/API/Window/location)
- [Location](https://developer.mozilla.org/ko/docs/Web/API/Location)

### 메서드 종류

|Property|설명|예시|
|------|---|---|
|hash|주소값에 붙어있는 anchor값 반환|#cart|
|host|URL의 도메인과 포트 반환|'www.example.com:8080'|
|hostname|URL의 도메인 반환|'www.example.com'|
|href|URL 반환|`http://www.example.com:8080/search?q=devmo#test`|
|origin|프로토콜 + URL의 도메인 + 포트|`http://www.example.com:8080`|
|pathname|URL 경로 반환|/search|
|port|서버포트 반환|8080|
|protocol|프로토콜 반환|http:|
|search|URL에 붙은 매개변수 반환(물음표 뒤의 값)|?q=devmo|

### location 객체를 사용한 Routing

일반적인 웹 사이트는 URL에 따라 다른 웹 페이지를 보여줌

하나의 웹 페이지를 하나의 컴포넌트로 만들고, URL에 따라 적절한 컴포넌트가 보이게 함으로써 구현 가능

```tsx
function App() {
 const { pathname } = window.location;
 
 return (
  <div>
   <Header />
   <main>
    {pathname === '/' && <HomePage />}
    {pathname === '/about' && <AboutPage />}
   </main>
   <Footer />
  </div>
 );
}
```

**심화**

```tsx
import Footer from "./components/Footer";
import Header from "./components/Header";

import AboutPage from "./pages/AboutPage";
import HomePage from "./pages/HomePage";

const pages: Record<string, React.FC> = {
  '/': HomePage,
  '/about': AboutPage,
}

export default function App() {

  const path = window.location.pathname;

  // 위 pages에서 타입을 잡아주지 않았다면
  // Reflect.get(pages, path) || HomePage 로 쓸 수도 있음
  const Page = pages[path] || HomePage;

  return (
    <>
    <Header />
    <main>
      <Page />
    </main>
    <Footer />
    </>
  );
}
```
