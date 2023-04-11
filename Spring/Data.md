### @ModelAttribute

---

1. 메소드의 매개변수로 선언
    - 클라이언트에서 요청한 multipart/form-data 형태의 HTTP Body를
    - setter를 통해 오브젝트 형태로 매핑해줌
    
    ⇒ 외부 데이터가 bean에 등록된 객체로 자동 변환 후 사용
    
    - 자동으로 Model 객체에 추가됨
    - 넘어오는 변수명과 파라미터 변수명이 같은 경우 생략 가능
2. 메소드에 선언
    - View에서 사용할 데이터를 설정하는 용도
    - @RequestMapping이 적용된 메소드보다 먼저 호출
    - 리턴되는 객체는 자동으로 Model에 저장

### @RequestParam

---

- 메소드 파라미터에 붙일 수 있고, 변수명으로 선언된 값이 항상 Request에 존재해야 함
- Servlet API가 query parameter, form data 등을 request body에서 하나의 map형태로 변환해 전달해줌
- 파라미터 타입이 Collection타입이고, 변수명이 같으면 해당 형태로 변환해줌
- String, int 같은 단순 타입의 경우 생략 가능
- Form태그에서 method가 POST 전송일 때는 받을 수 있음
- Map<String, String>으로 받을 경우 모든 request parameter를 받음

### @PathVariable

---

- 메소드 파라미터에서 사용
- URL의 중괄호 안의 명과 변수명이 같아야 함
- Map<String, String>으로 받을 경우 모든 변수 가져옴

### @MatrixVariable

---

- “;”로 구분함
- key=value 값 형태로 사용
- PathVariable과 함께 사용 가능

```java
//URL : /hello/42;q=11;r=12/pets/21;q=22;s=23
@GetMapping(value = "/hello/{ownerId}/pets/{petId}")
  public void findPet(
        @MatrixVariable Map<String, String> matrixVars,
        @MatrixVariable(pathVar="petId"") Map<String, String> petMatrixVars) {
    // matrixVars: ["q" : [11,22], "r" : 12, "s" : 23]
    // petMatrixVars: ["q" : 11, "s" : 23]
  }
```

- Map<String, String>으로 모든 값 가져오기 가능

### @RequestHeader

---

- 메소드 파라미터로 사용
- request header 내의 정보 가져올 수 있음
- Map<String, String>으로 모든 값 가져오기 가능
- Accept, Accept-length, Host 등 지정해서 가져오기 가능

### @RequestBody

---

- 메소드 파라미터로 사용
- 요청의 body부분을 가져와 사용
- xml, json 기반의 메세지를 사용하는 요청의 경우 유용
- HTTP 요청의 body 내용을 자바 객체로 변환 후 메소드 파라미터로 전달

### @ResponseBody

---

- 클래스, 메소드 레벨에서 사용
- 응답의 body 부분에 return 값을 넣어서 전송

### `HttpEntity<T>`

---

- HTTP 요청이나 응답에 해당하는 HttpHeader, HttpBody를 포함하는 클래스
- RequestEntity, REsponseEntity가 상속받고 있음
- ResponseEntity는 HttpStatus, HttpHeaders, HttpBody를 포함
- 클라이언트 측으로 상태코드(Status), 헤더(headers), 응답데이터(ResponseData) 전달 가능
- HttpEntity 는 수정이 안됨
- ResponseEntity는 수정 가능
- `ResponseEntity<T>` 의 타입은 Body에 넣을 타입
- Accept를 명시하지않으면 모두 가능으로 판단함

### HandlerMethodArgumentResolver

---

- 컨트롤러 메소드에서 특정 조건에 맞는 파라미터가 있을 때 원하는 값을 바인딩 해줌
- @RequestBody를 사용해 request의 body값,
@PathVariable를 사용해 request의 path parameter 값을 가져 와줌

### ArgumentResolver

---

> Servlet과 Controller 사이에서 전달되어진 값을 가공 or 추가할때 사용
> 

`HandlerMethodArgumentResolver`를 상속 받아 사용