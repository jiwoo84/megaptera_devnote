# 2. Typescript

- 간단히 REPL 사용하려면 ts-node 실행하면 됨 (`npx ts-node`)

## 타입 지정

---

타입 추론이 되기 때문에 너무 완벽히 쓰지 않아도 됨 [(참고)](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%ED%83%80%EC%9E%85-%EC%B6%94%EB%A1%A0-types-by-inference)

```tsx
let name: string;
let age: number;

name = "홍길동";
age = 13;

// object
let human: {
  name: string;
  age: number;
};

human = { name: "홍길동", age: 13 };

// array
let numbers: number[];

numbers = [1, 2, 3];

let anythings: any[];

anythings = ["hp", 256];

// 더 엄격하게 타입 관리하고 싶다면 => tuple
let pair: [string, number];

pair = ["hp", 256];
```

### 타입 & 인터페이스

```tsx
type Human = {
  name: string;
  age: number;
};

let boy: Human = { name: "홍길동", age: 13 };

interface Person {
  name: string;
  age: number;
}

let girl: Person;

girl = { name: "홍길동", age: 13 };

function add(x: number, y: number): number {
  return x + y;
}

add(1, 2);

add("Hello", "World"); // error
```

> 💡 **타입 vs 인터페이스**
>
> - 타입은 새 프로퍼티를 추가하도록 개방 x
> - 인터페이스는 확장 가능

### 정해진 값 타입 지정

```tsx
let category: "food";

category = "food";
```

### Union Type

여러 타입 중 하나일 수 있음을 선언하는 방식 [(참고)](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html#%EC%9C%A0%EB%8B%88%EC%96%B8-unions)

```tsx
type bool = true | false;

let flag: bool;

flag = true;
flag = false;
flag = 3; // error
```

- 매개변수 제한하기

  ```tsx
  type Category = "food" | "toy" | "bag";

  function fetchProducts({ category }: { category: Category }) {
    console.log(`Fetch ${category}`);
  }
  ```

### Optional Parameter

값의 유무가 불분명할 때 사용 [(참고)](https://www.typescriptlang.org/docs/handbook/2/functions.html#optional-parameters)

```tsx
function greeting(name?: string): string {
  return `Hello, ${name || "world"}`;
}

// 기본값 넣기
function greeting(name: string = "world"): string {
  return `Hello, ${name}`;
}

// 매개변수가 객체일 때
type Human = {
  name: string;
  age?: number;
};

function greeting({ name, age }: Human = {}): string {
  return age ? `${name} (${age})` : name;
}

greeting({ name: "홍길동" }); // 에러 안남
```

### Intersection Type

타입을 확장하는 방식 (참고: [교집합](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes-func.html#%EA%B5%90%EC%A7%91%ED%95%A9) /[Intersection Types](https://www.typescriptlang.org/docs/handbook/2/objects.html#intersection-types))

```tsx
type Human = {
  name: string;
  age: number;
};

type Creature = {
  hp: number;
  mp: number;
};

type Person = Human & Creature;

let person: Person;

person = { name: "홍길동", age: 13, hp: 256, mp: 16 }; // 하나만 빼먹어도 error
```

### Generics

어떤 함수나 클래스가 사용할 타입을 생성 단계가 아닌 사용 단계에서 정의하는 방식 [(참고)](https://www.typescriptlang.org/docs/handbook/2/generics.html)

- Tip: [더 좋은 타입스크립트 프로그래머로 만드는 11가지 팁](https://velog.io/@lky5697/11-tips-that-help-you-become-a-better-typescript-programmer)

```tsx
function identity<Type>(arg: Type): Type {
  return arg;
}

let output = identity<string>("myString");
```

### Utility Types

공통 타입 변환을 용이하게 하기 위해 TS가 전역으로 제공하는 방식 [(참고)](https://www.typescriptlang.org/docs/handbook/utility-types.html)

- **keyof**

  T의 모든 프로퍼티의 키값을 union형태로 반환

  ```tsx
  interface User {
    id: number;
    name: string;
    haveCar: boolean;
  }

  type UserKey = keyof User; // = 'id' | 'name' | 'haveCar'

  const uk: UserKey = "name"; // error 안나고 통과
  ```

- **Partial<type>**

  해당 타입의 모든 프로퍼티를 optional로 설정(있어도 되고 없어도 됨)한 새로운 타입 반환

  ```tsx
  /*interface User { // 아래 partial을 적용하면 이런 상태가 됨
      id?: number;
      name?: string;
      haveCar?: boolean;
  } */

  let admin: Partial<User> = {
    // 프로퍼티가 일부만 있어도 에러 안남
    id: 1,
    name: "Bob",
  };
  ```

- **Required<type>**

  모든 프로퍼티를 필수로 지정 (`?` 연산자가 있어도)한 새로운 타입 반환

  ```tsx
  interface User {
    id: number;
    name: string;
    haveCar?: boolean;
  }

  let admin: Required<User> = {
    // haveCar 없어서 에러
    id: 1,
    name: "Bob",
  };
  ```

## Visual Studio Code 자동 완성 + 실시간 오류 검사

---

현실적으로 TypeScript를 쓰는 가장 큰 이유

오래된 라이브러리의 경우 d.ts 파일만 따로 패키지로 제공됨 (패키지 이름은 `@types/블라블라`)

- [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)
- [DefinitelyTyped/types](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types)
- [DefinitelyTyped/types/react](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react): React 확인 가능. React 18이 바로 나오고(index.d.ts), 이전 버전은 하위 폴더(디렉터리)로 따로 관리되고 있음
