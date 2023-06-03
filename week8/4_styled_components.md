# styled-components

스타일이 적용된 컴포넌트를 쉽게 만들 수 있는 도구

**장점**

React 컴포넌트를 스타일링하기 위해 CSS를 향상시키려고 고민한 결과물

- 자동으로 중요한 CSS 삽입: 현재 화면에 사용중인 스타일만 DOM에 있음, 사용자가 필요한 최소한의 코드만 로드됨

- 클래스 이름 버그 없음: 고유한 클래스 이름을 생성하기 때문에 중복이나 오타로 인한 에러가 생기지 않음

- CSS 제거가 쉬움: 어떤 컴포넌트에서 사용되는지 알기 때문에 쉽게 찾아서 없앨 수 있음

- 간단한 동적 스타일 지정 / 손쉬운 유지 보수

- 자동으로 Vendor Prefixing 입력: 이전에는 Vendor prefixing를 신경써야했지만 이젠 생각할 필요 없음

## 🍀 설치

1. styled-components 설치

2. SSR이나 스타일 축소에 대응할 수 있는 플러그인 설치 (공식 권장 사항)
    - babel 사용한다면 -> [Babel Plugin](https://styled-components.com/docs/tooling#babel-plugin)
    - swc 사용한다면 -> [@swc/plugin-styled-components](https://github.com/swc-project/plugins/tree/main/packages/styled-components)

3. vscode 익스텐션 설치한다면 생산성에 크게 도움이 됨 -> [vscode-styled-components extention](https://marketplace.visualstudio.com/items?itemName=styled-components.vscode-styled-components)

```bash
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

## 🍀 기본 설정

`.swcrc` 파일 작성

```tsx
{
 "jsc": {
  "experimental": {
   "plugins": [
    [
     "@swc/plugin-styled-components",
     {
      "displayName": true,
      "ssr": true
     }
    ]
   ]
  }
 }
}
```

## 🍀 사용법

- nested style 지원: 별도의 class를 정의할 필요 없이, 하나의 block 안에 여러 CSS를 적용할 수 있는 방법

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
 color: #00F;

 strong {
    color: red;
 }
`;

export default function Greeting() {
 return (
  <Paragraph>
   Hello, world!
    <strong>This is red</strong> // 여기만 빨간색
  </Paragraph>
 );
}
```

**스타일 컴포넌트 상속 및 확장**

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
 color: #00F;
`;

const BigParagraph = styled(Paragraph)`
 font-size: 5rem;
`;

export default function Greeting() {
 return (
  <BigParagraph>
   Hello, world!
  </BigParagraph>
 );
}
```

### 기존 컴포넌트에 스타일 입히기

기존 컴포넌트가 Class를 잡아줘야 한다는 점에 주의!

```tsx
import styled from 'styled-components';

function HelloWorld({ className }: React.HTMLAttributes<HTMLElement>) {
 return (
  <p className={className}>
   Hello, world!
  </p>
 );
}

const Greeting = styled(HelloWorld)`
 color: #00F;
`;

export default Greeting;
```
