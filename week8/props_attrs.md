# props와 attrs

## 🍀 Props 활용

활성화 여부를 표현하거나, 특정 스타일을 잡아주고 싶을 때 유용함

### 기본 활용법

```tsx
import styled from "styled-components"
import { useBoolean } from "usehooks-ts"

type ButtonProps = {
    active: boolean;
}

const Button = styled.button<ButtonProps>`
    background: ${props => props.active ? 'blue' : 'white'};
`

// 버튼을 누를 때마다 버튼 배경색이 바뀜
export default function Switch() {
    const { value: active, toggle } = useBoolean()

    return (
        <Button
            type="button"
            onClick={toggle}
            active={active}
        >On & Off</Button>
    )
}
```

### 복잡한 응용법

```tsx
import { useBoolean } from 'usehooks-ts';

import styled, { css } from 'styled-components';

// 스타일 컴포넌트로 들어오는 props의 타입 잡아주기
type ParagraphProps = {
 active?: boolean; // active 안 넣어도 되게 처리
}

// 스타일 변수 생성
const trueStyle = css`
 color: green;
`

const Paragraph = styled.p<ParagraphProps>`

 color: ${(props) => (props.active ? '#F00' : '#888')};

 ${(props) => props.active && css`
  font-weight: bold;
 `}

 ${({ active }) => active && trueStyle }
`;

export default function Greeting() {
 const { value: active, toggle } = useBoolean(false);
 
 return (
  <div>
   <Paragraph>
    Inactive
   </Paragraph>
   <Paragraph active> // active 값이 변화하지 않고 초기값이 들어가서 유지됨
    Active
   </Paragraph>
   <Paragraph active={active}> // toggle이 실행될 때마다 active값 변경됨
    Hello, world
    {' '}
    <button type="button" onClick={toggle}>
     Toggle
    </button>
   </Paragraph>
  </div>
 );
}
```

## 🍀 속성 추가

`attrs(props => { attrs: ... })`

안에 들어가는 객체가 속성값으로 들어감

**props를 다루는 것과의 차이점**

위 내용처럼 props 내용을 받아 스타일에 사용할 때는 따로 props의 내용을 받아오는 형태를 갖추지 않았음

또한 이를 사용해도 결국 외부에서 클래스로 스타일을 적용하고, 그 className을 해당 태그에 삽입한다.

하지만 `attrs()`는 내부에 작성한 객체의 내용이 inline으로 컴포넌트의 속성에 들어감

=> html tag가 가지고 있는 고유의 attribute를 넣어 중복을 줄이고 재사용하기 좋음

ex) button의 `type='button'`

**타입 선언 형태**

```tsx
type propsType = {
    attr: ~;
    active: ~;
}
styled.tag.attrs<propsType>(props => ({
    attr: props.attrs ?? ...; 
}))<propsType>`
    color: ${props.active} ? blue : null;
    ...
    `
```

### `attrs({정적 객체})`

기본 속성으로 추가됨 => 불필요하게 반복되는 속성을 처리할 때 유용

ex) 버튼의 `type="button"`

```tsx
import styled from 'styled-components';

const Button = styled.button.attrs({
 type: 'button', // 여기에 넣어주면 속성을 넣지 않아도 기본으로 들어감
})`
 border: 1px solid #888;
 background: transparent;
 cursor: pointer;
`;

export default Button;
```

### `attrs({객체를 반환하는 함수})`

다이나믹하게 속성을 넣어야 할 때 사용

```tsx
import styled from "styled-components"
import { useBoolean } from "usehooks-ts"

type ButtonProps = {
    type?: 'button' | 'submit' | 'reset';
    active?: boolean;
}

// 정말 나누고 싶다고 하면 아래와 같이 따로 attrs의 타입만 나눠서 넣을 수 있음
// type ButtonAttrs = {
//     type?: 'button' | 'submit' | 'reset';
// }

const Button = styled.button.attrs<ButtonProps>(props => ({
    type: props.type || 'button'
})) <ButtonProps>`
    color: ${(props) => (props.active ? '#F00' : '#888')};
`

export default function Switch() {
    const { value: active, toggle } = useBoolean()

    return (
        <Button
            type="submit" // 안 넣으면 button이 됨
            onClick={toggle}
            active={active}
        >On & Off</Button>
    )
}
```

**상속해서 다른 컴포넌트 만들기**

```tsx
import styled from "styled-components"
import { useBoolean } from "usehooks-ts"

type ButtonProps = {
    type?: 'button' | 'submit' | 'reset';
    active?: boolean;
}

const Button = styled.button.attrs<ButtonProps>(props => ({
    type: props.type || 'button'
})) <ButtonProps>`
    color: ${(props) => (props.active ? '#F00' : '#888')};
`

// Button과 같지만 새롭게 지정한 스타일만 바뀐 버튼 완성
const PrimaryButton = styled(Button)`
    ${({ active }) => active && `
        background: #40ccb9;
        color: white;
    `}
`

export default function Switch() {
    const { value: active, toggle } = useBoolean()

    return (
        <Button
            type="submit" // 안 넣으면 button이 됨
            onClick={toggle}
            active={active}
        >On & Off</Button>
    )
}
```

## 🍀 Reset CSS

초기에 설정된 모든 CSS 없애기

- [Reset Css](https://meyerweb.com/eric/tools/css/reset/): 가장 유명하고 고전적인 방식 -> 그대로 style 파일에 넣어주면 됨
- [styled-reset](https://github.com/zacanger/styled-reset): 위 코드를 styled-components에 적용하도록 만들어진 라이브러리

**패키지 설치**

```bash
npm i styled-reset
```

**App 컴포넌트에서 사용**

```tsx
import * as React from 'react'
import { Reset } from 'styled-reset'

const App = () => (
  <React.Fragment>
    <Reset />
    <div>Hi, I'm an app!</div>
  </React.Fragment>
)

// React.Fragment 사용 안해도 됨
const App = () => (
  <div>
    <Reset />
    <div>Hi, I'm an app!</div>
  </div>
)
```
