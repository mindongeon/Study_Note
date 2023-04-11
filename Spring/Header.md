# 1. 목차

- [1. 목차](#1-목차)
- [2. 헤더](#2-헤더)
  - [2.1. 공통 헤더](#21-공통-헤더)
  - [2.2. 요청 헤더](#22-요청-헤더)
  - [2.3. 응답 헤더](#23-응답-헤더)


# 2. 헤더


## 2.1. 공통 헤더

---

1. Date
    1. HTTP 메세지가 만들어진 시각
    2. 자동생성
2. Connection
    1. 일반적으로 HTTP/1.1 사용
3. Content-Length
    1. 요청과 응답 메세지의 본문 크기를 바이트 단위로 표시
    2. 메세지 크기에 따라 자동 생성
4. Cache-Control
5. Content-Type
    1. 컨텐츠의 타입과 문자열 인코딩 명싱
    
    ex) Content-Type : text/html; charset=utf-8
    
6. Content-Language
    1. 사용자의 언어
7. Content-Encoding
    1. 응답 컨텐츠를 br, gzip, deflate 등의 알고리즘으로 압축해서 보내면 브라우저가 알아서 풀어 사용
    2. 요청, 응답 전송 속도가 빨라짐
    3. 데이터 소모량 줄어듬
    
    ex) Content-Encoding : gzip, deflate
    

## 2.2. 요청 헤더

---

1. Host
    1. 서버의 도메인 네임
    2. 반드시 하나가 존재해야 함
2. User-Agent
    1. 가장 흔하게 보고 사용하는  헤더
    2. 현재 사용자가 어떤 클라이언트(운영체제, 앱, 브라우저 등)를 통해 요청을 보냈는지 알 수 있음
    3. 접속자 및 사용기기 통계 내기 가능
    
    ex) User-Agent : Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.0.0 Safari/537.36
    
3. Accept
    1. 클라이언트가 허용할 수 있는 파일 형식
    2. 콤마로 여러 타입을 동시에 작성 가능
    3. 와일드카드 사용 가능
    
    ex1) Accept : text/html
    
    ex2) Accept : image/png, image/gif
    
    ex3) Accept : text/*
    

```
💡 **Accept로 원하는 형식 보내고, 서버가 응답하면서 헤더의 
Content를 설정함**
```

1. Cookie
    1. 웹서버가 클라이언트에 쿠키를 저장해 놓았다면
    2. 해당 쿠키의 정보를 이름-값 쌍으로 웹서버에 전송
2. Origin
    1. Protocol + Host + Port
        
        ex) HTTPS:// : Protocol
        ex) [www.xxxx.com](http://www.xxxx.com) : Host
        
        ex) :3000 : Port
        
    2. POST같은 요청을 보낼 때, 
    3. 요청이 어느 주소에서 시작되었는지를 나타내는데
    4. 이때 보낸 주소와 받는 주소가 다르면 
    5. CORS문제가 발생하기도 함
    
    ```
    💡 CORS
    Cross-Origin Resource Sharing
    브라우저에서 출처를 비교, 다른 경우 차단함
    ```
    
3. If-Modified-Since
    1. 페이지가 수정된 것을 알려줌
    2. 최신 버전 페이지 요청을 위한 필드
4. Authorization
    1. 인증 토큰을 서버로 보낼 때 사용하는 헤더
    2. API 요청같은 것을 할 때 토큰이 없으면 거절당하기 때문에 사용

## 2.3. 응답 헤더

---

1. Server
    1. 웹서버 정보를 나타냄
2. Access-Control-Allow-Origin
    1. 요청 Host와 응답 Host가 다르면 CORS 에러 발생
    2. 서버에서 응답 메세지로 프론트 주소를 적어주면 에러가 발생하지 않음
    
    ex) Access-Control-Allow-Origin : hello.naver.com
    
3. Allow
    1. GET요청만 받고 싶은 경우
    
    ex) Allow : GET
    
4. Content-Diposition
    1. 응답 본문을 브라우저가 어떻게 표시해야 할지 알려줌
    - inline : 웹페이지 화면에 표시
    - attachment : 다운로드
    
    ex) Content-Disposition : attachment; filename=’filename.json’
    
    - filename 으로 파일명 지정 가능
5. Location
    1. 300번대 응답이나 201 Created 응답일 때 어느 페이지로 이동할지 알려줌
6. Content-Security-Policy
    1. 다른 외부 파일들을 불러오는 경우,
    2. 차단할 소스와 불러올 소스 명시 가능