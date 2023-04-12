# Exception

## @Ignore : 테스트 무시하기

## @ExceptionHandler

- 컨트롤러의 메소드 레벨에서 사용
- 해당 컨트롤러에 해당하는 빈에서 `@ExceptionHandler`에 선언된 Excpetion 발생시 호출됨
- 인자로 여러개의 Exception 받아서 처리
- 여러 개의 Exception 설정 시 IOException.class 같은 포괄적인 명시보다 {FileSystemException.class, RemoteException.class}로 구체적인 명시 권장

## @ControllerAdvice

- 클래스 레벨에서 사용
- 모든 [@Controller](/Spring/A.Controller.md)에서 발생할 수 있는 Exception 처리
- `@ExceptionHandler`와 함께 사용
- `@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`
- Exception 발생 시 응답으로 객체를 리턴해야 하는 경우 `@RestControllerAdvice` 사용
- `@ControllerAdvice`만 사용해도 @RestController, `@Controller`의 Exception 처리 가능

# Redirect

- redirectAttrributes : GET 방식으로 가버림 → 데이터가 공개되어버림
- UriCompnentsBuilder : url 형식을 만들어줌

# MessageConverter

HandlerMapping → HandlerAdapter → ArgumentResolver → 처리 불가 시 → MessageConverter

처리 불가 : K=V 형식, 단순 데이터 외의 JSON, XML 같은 것

Header의 content-type을 확인해서 가져옴

---

`@XmlRootElement` : XML데이터를 해당 객체를 받고 싶을 때

---

jsonPath(”$.XXX”)