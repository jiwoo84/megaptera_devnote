# Playwright

## 학습 키워드

- E2E(End to End) Test
- Headless Chrome
- Puppeteer
- Playwright
- CodeceptJS

## 🍀 Playwright란?

웹 브라우저 기반 E2E 테스트 자동화 도구, 테스트 API와 테스트 러너까지 지원함

(브라우저에서 반복적으로 수행하는 작업을 자동화하여 테스트를 수행하고 웹 애플리케이션 개발을 도움)

Headless Chrome을 기반으로 한 Puppeteer를 계승하면서, 더 많은 웹 브라우저를 지원함

- 참고자료
  - [Playwright Configuration](https://playwright.dev/docs/test-configuration)
  - [Ashal의 Playwright](https://github.com/ahastudio/til/blob/main/test/playwright.md)

## 🍀 기본 세팅

**Playwright 패키지 설치**

eslint 플러그인도 같이 설치하기

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

**`playwright.config.ts` 파일 생성 및 작성**

playwright의 기본 설정 세팅 (프엔 파일이 따로 있다면 그 안에)

- headless모드: 창을 열지 않고 백그라운드에서 테스트가 실행되도록 하는 모드
  - 테스트 실행시 앞에 CI=true/false를 붙여줘서 제어 가능 (기본은 false)  
    ex) `CI=true npx playwright test` => 화면 안 띄우며 테스트 실행
  - 웹 애플리케이션을 자동화하면서 더 빠르고 안정적인 테스트 수행 가능
  - GUI(Graphical User Interface)가 없으므로 시스템 자원을 덜 사용하며, 결과를 자동으로 수집하고 분석하기 쉬움

```jsx
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
 testDir: './tests', // 테스트에 사용할 폴더
 retries: 0,
 use: {
  // channel: 'chrome', // 크로미움 아니고 크롬으로 띄워서 테스트 하려면 작성
  baseURL: 'http://localhost:8080', // 기본으로 하는 서버 url
  headless: !!process.env.CI, // CI 설정 여부에 따라 headless 여부가 결정되게 설정
  screenshot: 'only-on-failure',
 },
};

export default config;
```

**`tests/.eslintrc.js` 파일 생성 및 작성**

```jsx
module.exports = {
 env: {
  jest: false,
 },
 extends: ['plugin:playwright/playwright-test'],
 rules: {
  'import/no-extraneous-dependencies': 'off',
 },
};
```

**테스트 실행**

```bash
npx playwright test
```

- 에러 상황의 스크린샷 등이 담기는 `test-results` 폴더가 생김 (`.gitignore` 파일에 추가할 것)

## 🍀 테스트 코드 작성

### `tests/home.spec.ts` 생성 및 작성

**input 값으로 식당 목록 필터링 작동 테스트**

```jsx
import { test, expect } from '@playwright/test';

test('Filter restaurants', async ({ page }) => {
 await page.goto('/');

 await expect(page.getByText('Apple')).toBeVisible();

 const searchInput = page.getByLabel('Search');

 await searchInput.fill('a');

 await expect(page.getByText('Apple')).toBeVisible();

 await searchInput.fill('aa');

 await expect(page.getByText('Apple')).toBeHidden();
});
```

**increase 버튼 누르면 값이 1씩 증가하나 테스트**

`getText()` 같은 함수는 모두 비동기이므로 반복해서 실행하려면 Promise.all 같이 프라미스 API를 사용하는 것이 좋음

```jsx
test('Click the “Increase” button', async ({ page }) => {
 await page.goto('/');

 const count = 13;

// 13번 반복해서 누르기
// 안에서 promise를 반환하는 것을 명시적으로 나타내기 위해 내부에 async, await을 작성함
 await Promise.all((
  [...Array(count)].map(async () => {
   await page.getByText('Increase').click();
  })
 ));

// 이것도 가능
//  await Promise.all((
//   [...Array(count)].map(() => page.getByText('Increase').click())
//  ));

 await expect(page.getByText(`${count}`)).toBeVisible();
});
```

## 🍀 CodeceptJS

인간 친화적인 E2E 테스팅 도구

jest, react-test-library를 사용한 playwright를 사용할 때보다 코드는 쉽고 직관적

=> 비개발자와 소통하기 편함

=> 디테일한 테스트 하기에는 부족할 수 있음

- 참고자료
  - [CodeceptJS 3 시작하기](https://github.com/ahastudio/til/blob/main/test/20201207-codeceptjs.md)
  - [Codecep tJS 사용](https://github.com/ahastudio/CodingLife/tree/main/20211012/react#codeceptjs-사용)

### 작성 예시

링크로 연결된 사이트 테스트하기

**playwright 사용**

기존 jest, react-test-library 문법 사용하여 확인

```jsx
test('link test', async ({ page }) => {
 await page.goto('https://github.com');

// .all()을 붙이지 않고 검사하면 너무 많다고 에러남
 const element = await page.getByText(/Github/).all();
 expect(element.lenght).toBEGreaterThan(0);
});
```

**CodeceptJS 사용**

Feature라는 하나의 기능에 여러 시나리오로 테스트 가능

```jsx
Feature('Google');

Scenario('Search “CodeceptJS”', ({ I }) => {
  I.amOnPage('https://www.google.com/ncr'); // ncr 붙이면 영문으로 검색 가능해짐 (아니면 언어에 따라 검색 언어 설정됨)
  I.fillField('[name="q"]', 'CodeceptJS'); // [selector, 입력값] name='q'인 요소 찾아서 CodeceptJS 입력
  I.click('Google Search');
  I.see('codecept.io'); // 해당값이 화면에 뜨는지 검사
});
```
