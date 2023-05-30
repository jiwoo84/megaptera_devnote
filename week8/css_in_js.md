# CSS-in-JS

## 학습 키워드

- CSS in JS 란
- CSS

## 🍀 CSS in JS 란?

Javascript를 사용하여 구성요소의 스타일을 지정하는 기술

Javascript가 구문 분석될 때 CSS가  생성되어 DOM의 `<style>`에 첨부됨

### 만들어진 이유

참고: [A Unified Styling Language (2017)](https://blog.rhostem.com/posts/2017-06-24-unified-styling-language)

**1. Scoped styles: 범위가 지정된 스타일**

- CSS는 효율적으로 구조화하고, 한 눈에 파악하기 쉽지 않음

  => 이를 해결하기 위해 BEM(클래스 이름 지정 규칙)이 널리 쓰임

  => CSS-in-JS 라이브러리들이 BEM 기반의 사고방식을 따르며 UI에 스타일을 지정함

- 라이브러리들은 스타일의 범위를 지정하도록 강제하도록 해서 스타일 코드의 기본 품질을 향상함

  => 이 과정에서 BEM이 선택 가능한 옵션이 아닌 시스템에 포함된 규칙이 됨

  => 이때까지만 해도 라이브러리는 인라인 스타일이 아니라 CSS를 직접 생산해서 런타임 과정에서 전역에 추가하는 방식을 사용함

- 스타일과 컴포넌트의 의존성을 줄이고, 컴포넌트의 재사용성을 높이기 위해 styled-component 등장

**2. Critical CSS: 필수적인 CSS**

전에는 스타일을 document의 head에 추가해서 최초 렌더링과 함께 적용되도록 하기 위해서 따로 도구를 사용했다.

CSS-in-JS는 이를 시스템에 포함하여 기본적으로 CSS가 필수적으로 우선해서 작동하도록 함

**3. Smarter optimisations: 더 똑똑한 최적화**

중복을 줄이고 낭비가 없도록 최적화함

변수로 재사용하고, tailwind같은 atomic CSS를 사용해서 코드 길이를 줄임

**4. Package management: 패키지 관리**

CSS을 공유할 때, 공유한 라이브러리별로 서로 다른 CSS 의존성에 기대고 있음

CSS-in-JS를 사용한다면 같은 패턴을 아예 복사해와서 새로 임명하는 등 같은 패턴과 도구, 생태계를 사용하면서 얼마든지 스타일 코드를 조합하고 공유할 수 있음

**5. Non-browser styling: 브라우저가 아닌 환경의 스타일링**

CSS-in-JS를 사용하면 브라우저가 아닌 다른 플랫폼으로 렌더링하는 기능을 만들 수 있음

ex) react-sketchapp은 sketch로 렌더링해서 개발자가 아닌 디자이너가 보기 쉽게 할 수 있음

### 장점

> 참고: [All You Need To Know About CSS-in-JS (2017)](https://d0gf00t.tistory.com/22)
>

1. Thinking of Component: 컴포넌트 단위로 설계하고 스타일 지정 가능

2. Javascript 환경을 최대한 활용하여 CSS 향상 가능

3. 진정한 분리 법칙: 명시적으로 정의하지 않으면 부모의 요소에서 상속되는데, CSS-in-JS는 그렇지 않음 (jss-isolate 플러그인 때문임)

4. 스코프가 있는 선택자: JSS는 JSON 표현을 CSS로 컴파일할 때 기본적으로 고유한 클래스 이름을 생성함 -> 선택자 충돌이나 중복 클래스를 피할 수 있음

5. Vendor Prefixing: 이전에는 Vendor prefixing를 신경써야했지만 CSS-in-JS에서는 생각할 필요 없음

6. Code sharing: JS와 CSS 사이에서 상수와 함수를 공유 가능

7. 현재 화면에 사용중인 스타일만 DOM에 있음

8. 죽은 코드 제거 가능

9. CSS 유닛 테스트를 할 수 있음

## 성능 이슈

JS파일의 크기를 줄이는 것이 성능에 유리하기 때문에 CSS를 분리시키는 것이 좋음

### 대안

**styled-component를 르내리아(Linaria)로 교체해서 빌드할 때 CSS만 뽑아내기**

- [Linaria](https://linaria.dev/)
  - CSS를 평범한 텍스트로 작성.
  - React에 종속적이지 않지만, React Styled Component도 지원함.

**vanilla-extract나 페이스북 스타일링 라이브러리 사용하기**

- [vanilla-extract](https://vanilla-extract.style/)
  - CSS를 오브젝트 형태로 표현. React의 Inline Style과 유사함.
  - React와 무관하게 사용 가능.
