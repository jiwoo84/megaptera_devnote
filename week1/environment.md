# 1. 개발 환경 세팅

## 🍀 fnm 설치

```bash
# mac, linux
brew install fnm

# window
scoop install fnm
```

- (window) scoop 설치하기

  ```bash
  # 차례대로 실행
  Set-ExecutionPolicy RemoteSigned -Scope CurrentUser # Optional: Needed to run a remote script the first time

  irm get.scoop.sh | iex

  # 잘 설치됐는지, 어떤 명령어가 있는지 확인
  scoop help
  ```

### `nvm` vs `npm` vs `fnm`

`nvm` or `fnm`을 설치 -> 원하는 조건으로 Node.js를 설치 -> `npm`으로 필요한 패키지 설치

- `nvm` : node version manager  
  Node.js 설치를 위한 툴로, 여러 버전의 설치와 전환을 도와주는 버전 관리자

- `fnm` : Fast Node Manager  
  nvm보다 빠른 Node.js 설치를 위한 툴

- `npm` : Node Package Manager  
  Node.js로 개발된 프로그램(npm 패키지)를 설치, 업데이트, 설치하기 위한 툴

## 🍀 Node.js 설치

```bash
# 설치 가능한 버전 확인
fnm ls-remote

# fnm으로 설치하고, 기본 사용 설정
fnm install --lts
fnm use lts-latest
fnm default $(fnm current)

# 설치된 상태 확인
fnm list
fnm current
```

## NPM 업그레이드

```bash
npm install -g npm
```

## 🍀 TypeScript + React + Jest + ESLint + Parcel 개발 환경 세팅

### 작업 폴더 준비

```bash
mkdir my-app

cd my-app

# vscode로 바로 열기
code .
```

### npm 패키지 준비

```bash
npm init -y
```

- `-y`는 yes의 줄임말로 묻고 따지지 않고 파일을 만들어줌

### `.gitignore` 파일 생성

```json
// root의 폴더일 때
/node_modules/

// 폴더일 때
/node_modules

// 폴더 or 파일 상관없이
node_modules
```

- 완성된 gitignore 가져오기
  - [gitignore 만들어주는 사이트](https://www.toptal.com/developers/gitignore)
  - [github 언어별 gitignore 모음](https://github.com/github/gitignore)

### 타입스크립트 설정

```bash
# 설치
npm i -D typescript

# tsconfig.json 파일 생성 -> ts 프로젝트 어떻게 필드할지 설정하는 파일
npx tsc --init
```

- `-D` (=`--save-dev`)  
  생략하면 package.json의 dependencies, 붙이면 devDependencies 안에 설치됨

  - dependencies: 프로그램에 직접 사용됨
  - devDependencies: 개발 환경에서 사용됨 (주로 개발툴 / 배포시 제외 가능)

- `npx`
  - 설치 패키지 실행: `node_modules`/`.bin` 안의 파일 실행
  - 미설치 패키지 실행: 실행됨 (npx는 패키지를 설치하지 않고 사용할 수 있게 해주는 도구)

### `tsconfig.json` 파일의 jsx 속성 변경

```json
// before
// "jsx": "preserve", /* Specify what JSX code is generated. */

// after
"jsx": "react-jsx", /* Specify what JSX code is generated. */
```

### ESLint 설정

1. 설치 & 초기 설정

   ```bash
   # 설치
   npm i -D eslint

   # 설정
   npx eslint --init
   ```

2. `.eslintrc.js` 파일을 적절히 수정

   - jest 사용한다면 미리 `env`에 `jest: true` 넣어주면 좋음
   - 상단에 매번 React import하기 싫다면 extends에 `plugin:react/jsx-runtime` 추가

3. `.eslintignore` 파일 작성  
   eslint로 문법 검사하지 않을 파일 세팅

> 💡 eslint 적용하여 파일 수정하기
>
> ```bash
> # 현재 폴더 아래 모든 파일의 내용 검사
> npx eslint .
> # 에러난 내용 모두 고치기
> npx eslint . --fix
> ```

### 리액트 설치

```bash
npm i react react-dom

npm i -D @types/react @types/react-dom
```

### 테스팅 도구 설치

```bash
# jest + swc
npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom
```

`jest.config.js` 파일을 작성해 테스트에서 SWC를 사용하기
[참고](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/jest.config.js)

### Parcel 설치

```bash
npm i -D parcel
```

### `package.json` 파일의 scripts 적절히 수정

scripts에 등록하면 반복되는 명령어를 단축어로 만들어 실행 가능

```json
  "source": "./index.html", // 기본으로 index.html을 띄움
  "scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll"
  },
```

[참고](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/package.json)

### 기본 코드 작성하기

아래와 같이 파일을 만들어서 작성하면 됨

- `index.html`
- `src/main.tsx`
- `src/App.tsx`
- `src/App.test.tsx`
- `src/components/Greeting.test.tsx`
- `src/components/Greeting.tsx`
