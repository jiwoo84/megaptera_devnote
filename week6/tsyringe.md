# TSyringe

## 학습 키워드

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- sington (싱글톤)

## 🍀 TSyringe란?

External Store를 관리하는데 활용 가능한 TypeScript용 DI 도구(IoC Container)

 이를 통해 store를 만들면 React 컴포넌트 입장에서는 “전역”처럼 여겨짐

 “Prop Drilling” 문제를 해결할 수 있는 방법 중 하나(React로 한정하면 Context도 쓸 수 있음)

 **DI (Dependency injection: 의존성 주입)**

 하나의 객체가 다른 객체의 의존성을 제공하는 테크닉

 클라이언트가 의존성(서비스)에 대해 관여하지 않고, 신경쓰지 않도록 분리하려는 목적으로 사용함

## 🍀 사용법

### 기본 세팅

**의존성 설치**

```bash
npm i tsyringe reflect-metadata
```

**`reflect-metadata`를 실행시키기**

프론트 & 테스트 첫 실행 파일의 상단에 import 하기

(`src/main.tsx` 파일, `src/setupTests.ts` 파일)

```tsx
import 'reflect-metadata';
```

**데코레이터 사용 설정**

- 데코레이터: 클래스 선언, 메서드, 접근자, 프로퍼티 또는 매개 변수에 첨부할 수 있는 특수한 종류의 선언, `@expression` 형식 사용

데코레이터 형식을 사용하기 위해서는 `tsconfig.json`의 `experimentDecorators`, `emitDecoratorMetadata` 옵션을 주석 해제해서 활성화 해야함

### 싱글톤으로 관리할 Store 클래스 생성 및 사용

**생성**

```tsx
// src/stores/Store.ts
import { singleton } from 'tsyringe';

@singleton() // 데코레이터 여기서 사용
class CounterStore {
 count = 0; // 초기값 설정
}
```

**사용**

```tsx
import { container } from 'tsyringe';

const counterStore = container.resolve(CounterStore);
```

테스트에서 TSyringe에서 관리하는 객체를 초기화할 수 있다.

```tsx
container.clearInstances();
```

## 상태 변경 알림

Store는 어떤 식으로든 action을 처리하고, 상태가 바뀌면 연결된 컴포넌트를 forceUpdate한다.

컴포넌트는 해당 Store에서 상태를 얻어서 UI를 업데이트하게 되는데, 선언형 UI가 얼마나 편한지 절실히 느낄 수 있다.
