# VDOM (Virtual DOM)

## 학습 키워드

- VDOM(Virtual DOM)이란?
  - DOM이란?
  - DOM과 Virtual DOM의 차이
- Reconciliation(재조정) 과정은 무엇인가?

## 🍀 VDOM이란?

DOM을 추상화한 객체로, 이를 통해 DOM을 관리함

⇒ DOM을 직접 조작하지 않고 변경사항을 하나로 모았다가 DOM에서 달라지는 부분만 변경해서 렌더링함

- 일어난 변화들을 오프라인 DOM에 적용하고 묶어서 한 번에 실제 DOM에 적용함 (이건 꼭 virtual DOM이어야 할 수 있는 일 아니고 DOM fragment에 적용한 다음 기존 DOM에 전달하는 방식도 가능)
- 위 DOM fragment를 관리하는 과정을 자동화, 추상화한 것이  VDOM
- 바뀐 부분, 바뀌지 않은 부분을 파악해서 바뀐 부분만 재렌더링

⇒ DOM관리를 virtual DOM이 함으로써 컴포넌트가 DOM 조작 요청을 할 떄 다른 컴포넌트들과 상호작용하지 않아도 되고, DOM 조작에 대한 정보를 공유할 필요가 없음

⇒ 각 변화들의 동기화 작업을 거치지 않으면서 모든 작업을 하나로 묶어줌

## 🍀 VDOM을 쓰는 이유?

`VDOM을 쓰는 것은 DOM으로 바로 렌더링 하는 것보다 빠르지는 않음`

=> 적당히 빠르고, 유지보수가 편함

`React의 선언적 API를 가능하게 함`

=> React에게 원하는 UI의 상태를 알려주면 DOM이 거기에 맞춰 변화함

## 🍀 렌더링 성능 최적화

`1. 프로덕션 빌드 활용`

배포할 때 축소된 프로덕션 빌드 사용할 것

`2. DevTools Profiler로 컴포넌트 프로파일링`

`3. 긴 목록 가상화하기`

긴 코드를 렌더링 하는 경우 `windowing` 기법 사용하여 주어진 시간에 목록의 부분만 렌더링하며 리렌더링하는 시간과 DOM 노드 수를 줄일 수 있음

ex) 라이브러리 react-window, react-virtualized

`4. 불필요한 리렌더링 줄이기`

- useMemo / useCallback 사용 (클래스형 컴포넌트는 React.memo)
- 자식 컴포넌트의 props로 새 객체를 넘겨주지 말 것 ⇒ 참조 주소가 달라지기 때문에 메모제이션 되지 않음
- 컴포넌트를 매핑할 때에는 key값으로 index를 사용하지 않는다 ⇒ 엘리먼트를 생성할 때가 아니라 한 단계 위인 데이터를 생성하는 단계에서 고유한 id를 만들고, 이를 받아오는 것이 좋다 (고유한 아이디는 nanoid 라는 라이브러리를 사용해서 생성하면 간편함)
- state 선언 위치는 상태값을 사용하는 컴포넌트 상위에 할 것 (더 상위 X)

### `useRef` vs `Variable` vs `global Variable`

컴포넌트의 DOM 내부의 id를 변수로 지정할 때, 그냥 const 상수가 아닌 `useRef`를 사용하는 이유가 뭘까?

```tsx
export default function CheckBoxField({ label }) {
  const id = useRef(`checkbox-${label}`.replace(/ /g, '-').toLocaleLowerCase());

  return (
    <div>
      <input
        type="checkbox"
        id={id.current}
      />
      <label htmlFor={id.current}>{label}</label>
    </div>
  );
}
```

**`useRef` vs `variable` (컴포넌트 내부 선언)**

- 컴포넌트 내부에 선언된 변수는 리렌더링될 때마다 값이 초기화됨 => 컴포넌트 생애주기동안 같은 값을 유지 못함
- 반면 `ref.current`는 렌더링의 영향을 받지 않음 => 컴포넌트 생애주기동안 같은 값을 유지할 수 있음
- 렌더링에 영향을 받지 않아야 하는 값을 다룰 때 useRef 사용함

**`useRef` vs `global Varibale` (컴포넌트 외부 선언)**

- 컴포넌트 외부에 선언된 global 변수는 리렌더링 될 때마다 초기화되지 않음 => 컴포넌트의 생애주기와 변수의 값은 상관 없음
- 그러나 global 변수의 값은 모든 컴포넌트가 공유하고, 해당 컴포넌트와 완전히 관련없이 관리되기 때문에 해당 용도로 사용하기는 부적절함

> 컴포넌트 별로 다른 변수 값을 갖고 싶은 경우 UseRef를 사용할 것
> 컴포넌트 재사용시마다 변수도 각 컴포넌트에 맞게 새롭게 초기화되기 때문

### `useRef` vs `useMemo`

- 둘 다 리렌더링될 때마다 값이 초기화되지 않음
- `useRef`는 단순한 값 저장할 때 적절함
- `useMemo`는 계산이 필요한 값 저장할 때 적절: 첫번째 인수로 들어가는 함수가 return하는 값을 저장하고, 두 번째 인자의 배열 안에 들어가는 값이 변경될 때마다 계산을 새로 해주기 때문임
