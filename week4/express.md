# Express

## 학습키워드

- Express 란
- URL 구조
- REST API
- HTTP Method(CRUD)

## 🍀 Express

**Express 설치**

1주차의 기본 세팅에 추가적으로 express 설치

```bash
npm i express
npm i -D @types/express
```

**ts-node**

자바스크립트를 웹이 아닌 콘솔에서 실행하려면 Node.js를 사용함

Node.js는 ts를 해석하지 못하기 때문에 컴파일러를 통하지 않고 Node.js 상에서 ts 코드나 파일을 실행하려면 ts-node가 필요함

```bash
# 설치
npm i -D ts-node

# 서버 실행
ts-node app.ts
```

**nodemon**

코드를 수정할 때마다 서버를 재실행해야 하는 문제를 피하기 위해 사용

- ts-node에 의존하고 있기 때문에 nodemon 사용하려면 ts-node 필요함
- nodemon으로 서버를 실행하면 프로젝트 내의 모든 파일의 변경을 감지해서 재실행함

```bash
# 설치
npm i -D nodemon

# 서버 실행
npx nodemon app.ts
```

### 서버 실행 & 라우터 생성

```jsx
// 루트에 생성된 app.ts
import express from 'express';

const app = express();
const port = 3000;

// Port(번호)로 서버를 실행함
app.listen(port, () => {
 console.log(`Server running at http://localhost:${port}`);
});

// 라우팅 추가
app.get('/', (req, res) => {
 res.send('Hello, world!');
});

// JSON으로 전달되는 req.body 받아오기위해 사용
app.use(express.json())
```

### 데이터 전달하는 REST API 만들기

`http://localhost:3000/products`으로 접속시 데이터 전달됨

- 서버 콘솔에 `curl localhost:3000/products` 입력하면 전달하는 데이터 볼 수 있음

```jsx
app.get('/products', (req, res) => {

 const products = [
  {
   category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
  },
  {
   category: 'Fruits', price: '$1', stocked: true, name: 'Dragonfruit',
  },
  {
   category: 'Fruits', price: '$2', stocked: false, name: 'Passionfruit',
  },
  {
   category: 'Vegetables', price: '$2', stocked: true, name: 'Spinach',
  },
  {
   category: 'Vegetables', price: '$4', stocked: false, name: 'Pumpkin',
  },
  {
   category: 'Vegetables', price: '$1', stocked: true, name: 'Peas',
  },
 ];
 
 res.send({ products }); // 자동으로 JSON으로 전달됨
});
```

## 🍀 REST API

REST는 Representational State Transfer의 약자로 해당 용어는 로이필딩에 의해 만들어짐

앱 사이의 결합도를 낮추게끔 설계하는 아키텍처 스타일로, 서버와 클라이언트가 별도로 구축되고 결합될 수 있게 함

### Richardson Maturity Model

<img src="https://martinfowler.com/articles/images/richardsonMaturityModel/overview.png" width="400px" />

<br />

리차드슨이 정의한 REST API의 성숙도 모델

대개는 4가지를 모두 만족하지 않고, Resource와 HTTP Verb만 도입하는 수준으로 사용

0. HTTP 사용

    단일 URI과 HTTP 프로토콜 메소드를 사용 (아주 기본적인 단계)

1. URI Resource

    리소스 개념을 도입하여, 모든 요청을 하나의 endpoint로 보내는 것이 아니라 개별 리소스와 통신함

    - HTTP method는 get, post만 사용
    - StatusCode는 200만으로 전달
    - header에 Content-Type이나 Cache 정보도를 제공하지 않는 단계

2. HTTP Verbs

    4가지 HTTP method를 사용해서 CRUD를 표현하는 단계

    - StatusCode를 다양하게 활용함
    - header에 Content-Type이나 Cache 정보를 제공함
    - 보통 2단계까지만 수행함

3. Hypermedia Controls

    API 서비스의 모든 End-point를 최초 진입점이 되는 URI를 통해 Hypertext Link 형태로 제공

    - 클라이언트가 요청에 응답받은 후, 다음 단계의 작업을 알려주는 링크 요소를 제공하는 것

### REST API 설계 기본 규칙

**동사 사용 X (행위는 HTTP method로 표현) / 명사 O**

URL은 리소스 그 자체를 표현해야하고, CRUD 행위는 HTTP method로 표현되야함

- 나쁜 예: `/read-posts` → url에 행위가 표시되어 있음
- 좋은 예: `/posts` → 리소스 자체, 이걸로 get 요청을 하면 포스트 읽기가 됨

> **HTTP method**
> Create와 Update는 요청시 JSON으로 데이터 전달함
>
> - Read - GET
> - Create - POST
> - Update - PUT(전체)/PATCH(일부)
> - Delete - DELETE

**Collection/Document/Store/Controller**

- Collection : 단일 리소스(Document)들의 묶음 (복수)  
    ex) `http://<host>/poducts`
- Document: Collection내의 단일 리소스 (단수)  
    ex) `http://<host>/poducts/1` 상품의 아이디가 1인 경우
- Store: 클라이언트 쪽에서 저장하는 리소스 저장소 (보통 복수)  
    ex) `http://<host>/users/2/carts` 2번 유저의 장바구니 or 위시리스트
- Controller: CRUD를 제외한 특정 기능 요구시 예외적으로 동사 표현 허용
    ex) `http://<host>/users/2/register`

**이외 URL Rules**

- 마지막에 `/`를 포함하지 않음
- `_` 대신 `-` 사용
- 소문자 사용

## 🍀 URI & URL & URN

<img src="https://images.velog.io/images/kimdukbae/post/f613cb08-de69-4cbb-b410-cc0a1a574f2c/image.png" width="350px" />

<img src="https://lh4.googleusercontent.com/K9t30Sjp6cIbsLg67B-amTH927yS3q06E68DvSLK_4t364b38otQLLibFbyj_I0JH6fLB8tp5zEgVObcaSj810X1Xs2XWuwxD-_ibK8JtfsUaGgm5JNVhUhfjcH3vairIchwhKfviglEmKYYk5aH4cc74ncEaW6N9fRS5VZtKS4EGmvGSYIUSLvx04r0xQ" width="400px" />

<br />

- `Scheme`: 리소스에 접근하는데 사용할 프로토콜. 웹에서는 http나 https 사용
- `Host`: 접근할 대상(서버)의 호스트명
- `Path`: 접근할 대상(서버)의 경로에 대한 상세정보

<br />

**URI**

Uniform Resource Identifie = 인터넷상의 리소스 자체를 식별하는 고유한 문자열 시퀀스

- Uniform: 리소스을 식별하는 통일된 방식
- Resource: URI로 식별이 가능한 모든 종류의 리소스(웹 브라우저 파일 등)을 지칭함
- Identifier: 다른 항목과 구분하기 위해 필요한 정보

=> 통합 리소스 식별자

ex) `google.com`, `https://google.com`, `google.com/posts/1` 모두 포함

**URL**

Uniform Resource Locator = 네트워크상에서 통합 리소스의 위치를 나타내기 위한 규약

=> 식별자 + 위치 (`https://google.com`)  

- 컴퓨터 네트워크와 검색 메커니즘에서의 위치를 지정하는 웹 리소스에 대한 참조  
    (흔히 우리는 URL을 웹 사이트의 주소라고만 인식하지만 이는 웹 사이트 뿐만 아니라 컴퓨터 네트워크 상의 리소스을 모두 나타내는 표기법임)
- 해당 리소스의 서버가 지원하는 프로토콜(http, https, ftp 등)이 앞에 결합된 형태임

**URN**

Uniform Resource Name = 통합 리소스 이름

=> 리소스의 위치, 프로토콜, 호스트와는 상관없이 리소스에 부여된 이름
