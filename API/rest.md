# 간단.

## 사용하는 이유

데이터를 text로 전송하기 때문에 환경에 제약되지 않는다.

## method

### Web에서의 method

- GET = SELECT
- POST = CREATE

### Rest API 에서의 method

- GET = SELECT
- POST = CREATE
- PUT = UPDATE (column 하나씩 가능)
- PATCH = UPDATE (column을 통째로) => PUT으로 대체됨
- DELETE = DELETE

## 동기 vs 비동기

- 동기 : Block, 리턴값이 없음
- 비동기 : Non-Blcok, 리턴값이 있음

## 3가지

- Web에서 보낼 때 : axios
- URL, Method
- Rest -> Web : Java 객체 -> JSON

# REST API의 탄생

```Representational State Transfer```

웹 설계의 우수성에 비해 제대로 사용되어지지 못하는 모습에
웹의 장점을 최대한 활용할 수 있는 아키텍처로써 REST를 발표했다.

# 구성

- 자원(resource) - URI
- 행위 (verb) - HTTP METHOD
- 표현(Representations)

# 특징

## Uniform (유니폼 인터페이스)

URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일

## Stateless (무상태성)

작업을 위한 상태정보를 따로 저장하고 관리하지 않는다.   
세션 정보나 쿠키 정보를 별도로 저장하고 관리하지 않기 때문에
API 서버는 들어오는 요청만을 단순히 처리하면 된다.   
=> 서비스의 자유도가 높아지고 서버에서 불필요한 정보를 관리하지 않음으로써 구현이 단순해진다.

## Cacheable (캐시 가능)

HTTP가 가진 캐싱 기능이 적용 가능하다. HTTP 프로토콜 표준에서 사용하는 Last-Modified 태그나 E-Tag를 이용하면 캐싱 구현이 가능하다.

## Self-descriptiveness (자체 표현 구조)

REST API 메세지만 보고도 쉽게 이해할 수 있는 자체 표현 구조로 이루어져 있다.

## Client - Server 구조

REST 서버는 API 제공, 클라이언트는 사용자 인증, 컨텍스트(세션, 로그인 정보) 등을 직접 관리하는 구조로
각각의 역할이 확실히 구분되기 떄문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 의존성이 줄어든다.

## 계층형 구조

다중 계층으로 구성될 수 있으며 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있고
PROXY, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.

# 디자인 가이드

1. URI는 정보의 자원을 표현해야 한다.
2. 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현한다.

## 중심 규칙

### URI는 정보의 자원을 표현해야 한다. (리소스명은 동사보다는 명사를 사용)

```
GET /members/delete/1
```

URI는 자원을 표현하는데 중점을 두어야 한다.   
delete와 같은 행위에 대한 표현이 들어가면 안된다.

### 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE 등)로 표현

```
DELETE /members/1
```

회원 정보를 가져올 때는 GET, 회원 추가 시의 행위를 표현하고자 할때는 POST METHOD를 사용해 표현한다.

#### 회원정보를 가져오는 URI

```
GET /members/show/1 (x)
GET /members/1      (o)
```

#### 회원 추가시

```text
GET /members/insert/2 (x) - GET 메소드는 리소스 생성에 맞지 않는다.
POST /members/2       (o)
```

#### [참고]HTTP METHOD의 알맞은 역할

| METHOD | 역할                                                              |
|--------|-----------------------------------------------------------------|
| POST   | POST를 통해 해당 URI를 요청하면 리소스를 생성한다.                                |
| GET    | GET을 통해 해당 리소스를 조회한다. <br/> 리소스를 조회하고 해당 도큐먼트에 대한 자세한 정보를 가져온다. |
| PUT    | PUT을 통해 해당 리소스를 수정한다.                                           |
| DELETE | DELETE를 통해 리소스를 삭제한다.=                                          |

### URI 설계 시 주의할 점

#### 슬래시 구분자(/)는 계층 관계를 나타내는데 사용

```text
http://restapi.example.com/houses/apertments
http://restapi.example.com/animals/mammals/whales
```

#### URI 마지막 문자로 슬래시(/)를 포함하지 않는다.

URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며
URI가 다르다는 것은 리소스가 다르다는 것이고, 역으로 리소스가 다르며 URI도 달라져야 한다.
REST API는 분명한 URI를 만들어 통신을 해야하기 때문에 혼동을 주지 않도록 URI 경로의 마지막에는
슬래시(/)를 사용하지 않는다.

```text
http://restapi.example.com/houses/apartments/ (x)
http://restapi.example.com/houses/apartments  (o)
```

#### 하이픈(-)은 URI 가독성을 높이는데 사용

URI를 쉽게 읽고 해석하기 위해, 불가피하게 긴 URI 경로를 사용하게 된다면 하이픈을 사용해 가독성을 높일 수 있다.

#### 밑줄(_)은 URI에 사용하지 않는다.

글꼴에 따라 다르긴 하지만 밑줄은 보기 어렵거나 밑줄 때문에 문자가 가려지기도 한다.
이런 문제를 피하기 위해 밑줄 대신 하이픈(-)을 사용하는 것이 좋다. (가독성)

#### URI 경로에는 소문자가 적합하다.

URI 경로에 대문자 사용은 피하도록 해야 한다.
대소문자에 따라 다른 리소스로 인식하게 되기 때문이다.
RFC 3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하기 때문이다.

```text
RFC 3986 is the URI (Unified Rseource Identifier) Syntax document
```

#### 파일 확장자는 URI에 포함시키지 않는다.

```text
http://restapi.example.com/members/soccer/345/photo.jpg (x)
```

메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.
Accept header를 사용한다.

```text
GET /members/soccer/345/photo HTTP/1.1 Host: restapi.example.com Accept: image/jpg
```

### 리소스 간의 관계를 표현하는 방법

REST 리소스 간에는 연관 관계가 있을 수 있고, 이런 경우 다음과 같은 표현방법으로 사용한다.

```text
/리소스명/리소스ID/관계가 있는 다른 리소스명
ex) GET : /uses/{userid}/devices (일반적으로 소유 'has'의 관계를 표현할 때)
```

만약 관계명이 복잡하다면 이를 서브 리소스에 명식적으로 표현하는 방법이 있다.   
예를 들어 사용자가 '좋아하는' 디바이스 목록을 표현해야 할 경우

```text
GET : /users/{userid}/likes/devices (관계명이 애매하거나 구체적 표현이 필요할 때)
```

### 자원을 표현하는 Collection과 Document

Collection과 Document에 대해 알면 URI 설계까 한 층 더 쉬워진다.   
DOCUMENT는 단순히 문서로

