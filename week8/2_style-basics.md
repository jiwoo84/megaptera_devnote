# Style Basics

## 학습 키워드

- className
- 의미있는 마크업

## Basic: Class

파일을 분리하고 className을 사용해서 스타일을 설정하는 방법

- CSS는 컴포넌트를 전제로 하고 있지 않음 => 컴포넌트 중심으로 빠르게 작업하려고 하면 불편할 때가 많음
- 절충안으로 아주 작은 스타일 그룹을 클래스로 지정하는 방법도 있긴 하다(Bootstrap, Tailwind CSS 등의 접근법)

**`index.html` 파일에 간단히 CSS 추가**

```tsx
// index.html or css파일
<style>
.greeting {
color: #00F;
}
</style>
```

**“className” 지정**

```tsx
// src/Greeting
export default function Greeting() {
return (
<p className="greeting">
Hello, world!
</p>
);
}
```

## Basic: Inline Style

“style” 속성 활용해서 스타일 속성을 객체로 만들어 적용하기

- 평범한 JavaScript 객체를 활용하므로 변수, 함수 등의 재사용이 쉬움
- 텍스트가 아니라서 실수하거나 불편할 때가 있다.
- TypeScript의 힘으로 자동완성, 타입 검사 등의 도움을 받을 수 있다.

### 예시

**style 객체 만들어서 적용**

```tsx
// src/Greeting
export default function Greeting() {
 const style = {
  color: '#00F',
 };
 
 return (
  <p style={style}>
   Hello, world!
  </p>
 );
}
```

**객체 만들지 않고 바로 쓰기도 가능**

```tsx
export default function Greeting() {
 return (
  <p
   style={{
    color: '#00F',
   }}
  >
   Hello, world!
  </p>
 );
}
```

**함수 사용하기**

```tsx
const darkMode = false;

function primaryColor() {
    return darkModeo ? '#F00' : '#00f';
}
export default function Greeting() {
 return (
  <p
   style={{
    color: primaryColor(),
   }}
  >
   Hello, world!
  </p>
 );
}
```
