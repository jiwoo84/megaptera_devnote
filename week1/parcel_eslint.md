# 5. Parcel & Eslint

## Parcel

---

- 특별한 설정 없이 바로 사용 가능한 빌드 도구 (Zero Configuration)
- 내부적으로 SWC를 사용해 기존 도구보다 빠르다(ES Module을 적극 활용하는 Vite도 엄청나게 빠름)
- 참고
  - [Parcel 공식문서](https://parceljs.org/)
  - [https://github.com/ahastudio/til/tree/main/parcel](https://github.com/ahastudio/til/tree/main/parcel)
  - [https://github.com/ahastudio/til/tree/main/vite](https://github.com/ahastudio/til/tree/main/vite)

### 설정

`package.json` 파일에 source 속성 추가

추가 안한다면 명령어에 파일명도 추가해야함  
ex) `npx parcel index.html --port 8080`

```json
"source": "./index.html",
```

### 정적 파일 출력하기

1. [parcel-reporter-static-files-copy](https://github.com/elwin013/parcel-reporter-static-files-copy) 패키지 설치

2. `.parcelrc` 파일 작성

   ```json
   {
     "extends": ["@parcel/config-default"],
     "reporters": ["...", "parcel-reporter-static-files-copy"]
   }
   ```

3. static 폴더의 파일을 정적 파일로 Serving할 수 있음 (이미지 등 Assets)

### 빌드 + 정적 서버 실행

- 정적 서버 실행시 servor 사용 [(servor)](https://github.com/lukejacksonn/servor)

```bash
# 빌드
npx parcel build
# build하면 dist 폴더 안에 빌드 된 파일 만들어짐 (npm run start만 해도 만들어지긴 함)

# 배포
npx servor ./dist
```

## ESLint

---

- **사용 목적**

  - 스타일 통일
  - 잠재적 문제 발견
  - 베스트 프랙티스 추천 → 최신 트렌드를 학습하는데 활용

- **참고자료**
  - [ESLint 공식문서](https://eslint.org/)
  - [린트](<https://ko.wikipedia.org/wiki/린트_(소프트웨어)>)
  - [정적 프로그램 분석](https://ko.wikipedia.org/wiki/정적_프로그램_분석)
  - [Coding_conventions](https://en.wikipedia.org/wiki/Coding_conventions)

[VS Code ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

### 저장할 때마다 수정하게 설정하기

`.vscode/settings.json`파일을 추가해 JS/TS 파일을 저장할 때마다 ESLint를 실행하고 문제점을 고치게 설정 가능
(console.log 같이 스타일에 영향이 주지 않는 것은 수정하지 않음)

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

- 아샬이 쓰는 VS Code 기본 세팅:
  - [VS Code 기본 세팅](https://github.com/ahastudio/CodingLife/blob/main/20211008/react/.vscode/settings.json)
  - [Trailing Spaces](https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces)
