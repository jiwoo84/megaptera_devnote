# React State

## 학습 키워드

- React state란?
- DRY 원칙
- SSOT(Single Source of Truth)
- useState
- 1급 객체(first-class object)란?
- Lifting State Up

## 🍀 3. 최소한의 완전한 state 구조화하기

최소한의 state를 사용하여 인터렉티브한 UI 만들기

상태를 구조화할 때 반복되는 부분을 없애는 것이 가장 중요함

### state 원칙

- 변경되는 데이터여야 함
- 부모로부터 전달받을 수 없음 (있다면 props)
- 기존 state나 props를 기반으로 계산할 수 없음
- DRY : 정보 중복 지양
- SSOT : 데이터 요소를 한 곳에서만 제어 또는 편집 가능해야함

**[DRY (Don’t Repeat Yourself)](https://ko.wikipedia.org/wiki/중복배제)**

소프트웨어 개발 원리의 하나로, 모든 형태의 정보 중복을 지양하는 것

특히 다층 구조 시스템에서 유용함

**[SSOT (Single Source of Truth)](https://ko.wikipedia.org/wiki/단일_진실_공급원)**

정보 시스템 설계 및 이론의 관례로, 데이터 요소를 한 곳에서만 제어 또는 편집하도록 조직하는 것

데이터 요소로의 연결은 모두 참조로만 이루어지고, 데이터의 사본이 존재할 가능성이 없기 때문에 변경되면 전역으로 공유됨

**예시**

```tsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 나쁜 예: effect를 줄 필요 없음.
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}

function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ 좋은 예: 어차피 이름값이 바뀌면 랜더링 되면서 할당됨
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

## 🍀 4. 상태가 있어야 할 위치 식별하기

상태를 구조화한 다음, 상태의 변경을 담당하거나 상태를 소유하는 컴포넌트를 식별해야 함

- 상태가 사용되는 컴포넌트의 공통 부모 컴포넌트
- 적합한 컴포넌트가 없다면 생성해서 공통 부모 컴포넌트의 상위에 추가

## 🍀 5. 역방향 데이터 흐름 추가

자식 컴포넌트가 부모의 state를 바꾸기 위해서 setState를 전달해야 함

### Inverse Data Flow (역 데이터 흐름)

하위 컴포넌트의 props로 함수를 전달 (콜백 함수) -> 하위 컴포넌트에서 부모가 가진 state를 변경함

TypeScript(JavaScript)는 함수가 일급 객체라서 가능함

=> 어떤 함수를 다른 함수에 인자로 넘겨주거나, 어떤 함수를 리턴값으로 사용할 수 있다. 익명 함수, 클로저 등과 함께 사용하면 시너지가 큼.

**일급 (first-class) 객체**

다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체

- 조건
  - 함수의 실제 매개변수가 될 수 있다.
  - 함수의 반환 값이 될 수 있다.
  - 할당 명령문의 대상이 될 수 있다.
  - 동일 비교의 대상이 될 수 있다.

## 🍀 참고

아이템 목록에서 key가 value인 것만 선택하기

```jsx
function select<ItemType, ValueType>(
 items: ItemType[],
 key: keyof ItemType,
 value: ValueType,
) {
 return items.filter((item) => item[key] === value);
}
```
