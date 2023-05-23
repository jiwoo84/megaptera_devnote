## React Router

> [React Router](https://reactrouter.com/)
>

### Routes

> [Routes](https://reactrouter.com/en/main/components/routes)
>

> [Route](https://reactrouter.com/en/main/route/route)
>

간단히 코드 옮기기.

```tsx
import { Routes, Route } from 'react-router-dom';

function App() {
 return (
  <div>
   <Header />
   <main>
    <Routes>
     <Route path="/" element={<HomePage />} />
     <Route path="/about" element={<AboutPage />} />
    </Routes>
   </main>
   <Footer />
  </div>
 );
}
```

브라우저 라우터 내려주기.

> [BrowserRouter](https://reactrouter.com/en/main/router-components/browser-router)
>

```tsx
import { BrowserRouter } from 'react-router-dom';

root.render((
 <BrowserRouter>
  <App />
 </BrowserRouter>
));
```

테스트 코드에선 메모리 라우터 사용.

> [MemoryRouter](https://reactrouter.com/en/main/router-components/memory-router)
>