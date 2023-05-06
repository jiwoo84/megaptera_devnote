
# useRef & Custom Hook

## 학습 키워드

- useRef
- Hook의 규칙

## 🍀 useRef

> [beta 문서의 useRef](https://beta.reactjs.org/reference/react/useRef)
>

> [공식 문서의 useRef](https://ko.reactjs.org/docs/hooks-reference.html#useref)
>

렌더링에 영향을 주지도, 받지도 않기에 컴포넌트의 생애주기 전체에 걸쳐서 유지되는 객체 → 컴포넌트가 없어질 때까지 동일한 객체가 유지됨

객체 자체가 값은 아니고, 값을 참조하기 위한 객체 → 언제든지 값 변경 가능

**주요 용도**

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우 ex) input 등의 ID 관리
2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우

```jsx
const [count, setCount] = useState(0);

// count는 1초에 1씩 올라가고 있다고 가정했을 떄

useEffect(() => {
 setTimeout(() => {
  console.log(count); // 초기값을 바인딩해서 가지고 있기 떄문에 5추 후에 0이 출력됨
 }, 5_000);
}, []);

// 이럴 때 useRef를 생성하고 count가 변경될 때마다 useRef에 값을 넣어주는 effect 넣어주고
// 출력은 useRef값으로 한다면 의도대로 5가 출력되며 현재 count의 값을 출력할 수 있음
// 아주 극단적인 예시이고... 참고만 할 것
```

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

## 🍀 Custom Hook

로직을 재사용하기 위한 제일 쉬운 방법

useState, useEffect, useReducer, useCallback 등 Hooks 를 사용하여 원하는 기능을 구현해주고, 컴포넌트에서 사용하고 싶은 값들을 반환하면 됨

“use”로 시작하는 camelCase로 이름을 붙이면 됨

**예시**

컴포넌트 코드도 명확해지고, 밖에서 useFetchProducts를 사용할시 products만 받아볼 수 있으므로 setProducts 실수로 잘못 쓰여 값을 변경할 상황도 막을 수 있음

```jsx
// 컴포넌트 첫 렌더링 시 데이터 불러오는 훅
function useFetchProducts() {
 const [products, setProducts] = useState<Product[]>([]);
 
 useEffect(() => {
  const fetchProducts = async () => {
   const url = 'http://localhost:3000/products';
   const response = await fetch(url);
   const data = await response.json();
   setProducts(data.products);
  };

  fetchProducts();
 }, []);

 return products;
}
```

## 🍀 Hook 규칙

> [Hook의 규칙](https://ko.reactjs.org/docs/hooks-rules.html)

1. Function Component 바로 안쪽(함수의 최상위)에서만 호출
2. Function Component 또는 Custom Hook에서만 호출  
    처음에는 콜백 함수나 조건문 안에서 Hook을 호출하는 실수를 저지르기 쉽다

**나쁜 예**

컴포넌트의 바로 안쪽(최상위)에서 hook을 호출하지 않았음

```jsx
function Component() {
    if (playing) {
        const products = useFetchProducts();
        console.log(products);
    }
    //...
}
```

**좋은 예**

위와 같은 상황을 방지하기 위해 hook부터 설계를 다시 해서 훅

```jsx
export default function useFetchProducts() {
    const [products, setProducts] = useState([]);

    // 확실하게 의존성을 다루지 않았으므로 useEffect 외부에서 선언 가능
    const fetchProducts = async() => {
        const url = 'http://localhost:3000/products';
        const response = await fetch(url);
        const data = await response.json();
        setProducts(data.products);
    };

    useEffect(() => {
        fetchProducts();
    }, []);

    return { products, fetchProducts }
}

function Component() {
    const { products, fecthProducts } = useFetchProducts();

    // playing이 true일 때 데이터를 불러와서 출력
    if (playing) {
        fetchProducts();
        console.log(products);
    }
    //...
}
