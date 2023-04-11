Toby Spring

# 1. 목차

- [1. 목차](#1-목차)
- [2. @Controller](#2-controller)
- [3. 메소드 파라미터의 종류](#3-메소드-파라미터의-종류)
  - [3.1. HttpServletRequest, HttpServletResponse](#31-httpservletrequest-httpservletresponse)
    - [3.1.1. HttpSession](#311-httpsession)
    - [3.1.2. WebRequest, NativeWebRequest](#312-webrequest-nativewebrequest)
    - [3.1.3. Locale](#313-locale)
    - [3.1.4. InputStream, Reader](#314-inputstream-reader)
    - [3.1.5. OutputStream, Writer](#315-outputstream-writer)
    - [3.1.6. @PathVariable](#316-pathvariable)
    - [3.1.7. @RequestParam](#317-requestparam)
    - [3.1.8. @CookieValue](#318-cookievalue)
    - [3.1.9. @RequestHeader](#319-requestheader)
    - [3.1.10. Map, Model, ModelMap](#3110-map-model-modelmap)
    - [3.1.11. @ModelAttribute](#3111-modelattribute)
    - [3.1.12. Errors, BindingResult](#3112-errors-bindingresult)
    - [3.1.13. SessionStatus](#3113-sessionstatus)
    - [3.1.14. @RequestBody](#3114-requestbody)
    - [3.1.15. @Value](#3115-value)
    - [3.1.16. @Valid](#3116-valid)
- [4. 리턴 타입의 종류](#4-리턴-타입의-종류)
  - [4.1. 자동 추가 모델 오브젝트와 자동생성 뷰 이름](#41-자동-추가-모델-오브젝트와-자동생성-뷰-이름)
    - [4.1.1. 메소드 리턴 타입에 상관없이 조건만 맞으면 모델에 자동 추가되는 정보](#411-메소드-리턴-타입에-상관없이-조건만-맞으면-모델에-자동-추가되는-정보)
    - [4.1.2. 메소드 리턴 타입의 종류](#412-메소드-리턴-타입의-종류)
- [5. @SessionAttribues와 SessionStatus](#5-sessionattribues와-sessionstatus)
  - [5.1. @SessionAttributes](#51-sessionattributes)
  - [5.2. SessionStatus](#52-sessionstatus)



# 2. @Controller


DefaultAnnotationHandlerMapping : 컨트롤러 빈의 메소드에 매핑

AnnotationMethodHandlerAdapter : 매핑된 메소드를 호출

# 3. 메소드 파라미터의 종류


## 3.1. HttpServletRequest, HttpServletResponse

---

- 서블릿의 Request, Response 오브젝트 가능

### 3.1.1. HttpSession

- HTTP세션만 필요한 경우 HttpSession 타입 파라미터으로 받기
- 서버에 따라 멀티스레드 환경에서 안정성 보장 X
- 안전하게 사용하고 싶을 시,
핸들러 어댑터의 synchronizeOnSession을 true로 설정

### 3.1.2. WebRequest, NativeWebRequest

- HttpServletRequest의 요청정보를 대부분 갖고 있음
- 서블릿 API에 종속적이지 않은 오브젝트 타입

### 3.1.3. Locale

- java.util.Locale 타입
- DispatcherServlet의 Locale Resolver가 결정한 Locale 오브젝트

### 3.1.4. InputStream, Reader

- HttpServletRequest의 getInputStream()을 통해 받는 콘텐트 스트림 또는 Reader타입 오브젝트

### 3.1.5. OutputStream, Writer

- HttpServletResponse의 getOutputStream()으로 가져올 수 있는 출력용 콘텐트 스트림 또는 Writer타입 오브젝트

### 3.1.6. @PathVariable

- @RequestMapping의 URL에 { } 로 들어가는 패스 변수 받기
- 쿼리 스트링으로 보내는 대신 URL path로 풀어서 쓸 경우 사용

```java
@RequestMapping("/user/view/{id}")
public String view(@PathVariable("id") int id) { ... }
```

- 여러개도 가능

```java
@RequestMapping("/member/{membercode}/order/{orderid}")
public String lookup(
	@PathVariable("membercode") String code,
	@PathVariable("orderid") int orderid) {...}
```

### 3.1.7. @RequestParam

- 단일 HTTP 요청 파라미터를 메소드 파라미터에 넣어줌
- 가져올 요청 파라미터의 이름을 @RequestParam 기본값으로 지정

```java
public String view(@RequestParam("id") int id) {...}
```

- @RequestParam을 사용했다면 해당 파라미터가 반드시 있어야 함
- 원하지 않을시 다음과 같이 사용

```java
@RequestParam(value="id", required=false, defaultValue="-1")
```

- String, int 같은 단순 타입의 경우 생략 가능

```java
public String view(@RequestParam int id) {...}
```

### 3.1.8. @CookieValue

- HTTP 요청과 함께 전달된 쿠키 값을 가져옴

```java
// auth라는 이름의 쿠키를 가져오고 싶을 시
public String check(@CookieValue(value="auth",
	required=false, defaultValue="NONE")String auth) {...} 
```

### 3.1.9. @RequestHeader

- 요청 헤더정보를 가져옴
- 기본 값으로 가져올 HTTP 헤더의 이름을 지정

```java
public void header(@RequestHeader("Host") String host,
	@RequestHeader("Keep-Alive") long keepAlive)
```

- 헤더 값이 반드시 존재해야 함
- 없어도 될 시 required, defuaultValue 사용

### 3.1.10. Map, Model, ModelMap

- 다른 어노테이션이 없다면 위의 3가지 오브젝트 있음
- Model, ModelMap은 addAttribue() 메소드 제공

### 3.1.11. @ModelAttribute

- 메소드 파라미터, 메소드 레벨 적용 가능
- 클라이언트로부터 컨트롤러가 받는 요청정보 중
하나 이상의 갑승ㄹ 가진 오브젝트 형태로 만들 수 있는
구조적인 정보
- 메소드 파라미터에서 1:1로 받는 경우 @RequestParam
- 도메인 오브젝트, DTO의 프로퍼티에 바인딩해서 한 번에 받는 경우 @ModelAttribute
- 컨트롤러가 리턴하는 모델에 파라미터로 전달한 오브젝트를 자동 추가

### 3.1.12. Errors, BindingResult

- @RequestParam과 다르게 비어있는 값 또는 타입 변환에 실패해도 작업은 계속 진행됨
- 메소드 파라미터에 맞게 요청정보를 추출해서 제공해주는 어댑터 핸들러는 실패한 변환 작업에 대한 정보를 컨트롤러에게 제공
- 컨트롤러는 이런 정보를 참고해 적절한 에러 페이지 출력 또는 사용자가 폼을 다시 수정할 기회를 줘야함
- Errors나 BindingResult 파라미터를 함께 사용하지 않으면 스프링은 요청 파라미터의 타입이나 값에 문제가 없도록 어플리케이션이 보장해준다고 생각함
- 파라미터를 바인딩하다가 발생한 변환 오류와 모델 검증기를 통해 검증하는 중에 발견한 오류 저장됨
- 반드시 @ModelAttribute 파라미터 뒤에 나와야 함

```java
@RequestMapping(value="add", method=RequestMethod.POST)
public String add(@ModelAttribue User user,
	BindingResult bingdingResult) {...}
```

### 3.1.13. SessionStatus

- 모델 오브젝트를 세션에 저장했다 사용하는 경우
- 더 이상 세션 내의 정보가 필요없을 경우 제거해줘야 함
- 세션 안에 불필요한 오브젝트를 방치하는 것은 일종의 메모리 누수이므로 필요 없어진 경우 확실하게 제거해야 함

### 3.1.14. @RequestBody

- HTTP 요청의 body 부분이 그대로 전달됨
- HTTP 요청의 미디어 타입, 파라미터 타입 확인
- 메세지 컨버터 중에 해당 타입을 처리할 수 있는 것이 있다면
- HTTP 요청의 본문 부분을 통째로 변환, 지정된 메소드 파라미터로 전달

### 3.1.15. @Value

- 시스템 프로퍼티, 다른 빈의 프로퍼티 값 등을 가져옴

### 3.1.16. @Valid

- 모델 오브젝트를 검증하도록 지시하는 지시자
- 보통 @ModelAttribute와 함께 사용

# 4. 리턴 타입의 종류

---

- 컨트롤러가 DispatcherServlet에게 돌려줘야 하는 정보 : 모델, 뷰
- HttpServletResponse에 직접 결과를 넣어 리턴하는 경우도 있음

## 4.1. 자동 추가 모델 오브젝트와 자동생성 뷰 이름

---

### 4.1.1. 메소드 리턴 타입에 상관없이 조건만 맞으면 모델에 자동 추가되는 정보

**@ModelAttribute 모델 오브젝트 또는 커멘드 오브젝트**

- 기본적으로 모델 오브젝트의 이름은 파라미터 타입 이름을 따름
- 이름을 직접 지정하고 싶다면 @ModelAttribute(”모델명”)

---

**Map, Model, ModelMap 파라미터**

- Map, Model, ModelMap 타입의 파라미터로 전달받아 오브젝트 추가 가능
- 이렇게 추가된 오브젝트는 뷰에 전달되는 모델에 자동 추가됨

---

**@ModelAttribute 메소드**

- 일반 메소드에도 부여 가능
- 뷰에서 참고 정보로 사용되는 모델 오브젝트를 생성하는 메소드를 지정하기 위해 사용
- 컨트롤러 클래스 안에 정의하지만 컨트롤러 기능은 담당하지 않음
- 뷰에서 참조정보가 필요한 경우 사용
- 같은 클래스 내의 모든 컨트롤러 메소드에서 공통적으로 활용하는 정보이면 편리

---

**BindingResult**

- 잘못 입력된 폼 필드의 잘못 입력된 값을 가져오거나 바인딩 오류 메시지를 생성할 때 사용
- 일반적으로 뷰에서 직접 사용할 필요는 없음
- 핸들러 인터셉터를 이용해 바인딩 결과를 로깅, 분석할 때 사용

### 4.1.2. 메소드 리턴 타입의 종류

**ModelAndView**

- 컨트롤러가 리턴해야 하는 정보를 담고 있는 가장 대표적인 타입
- @Controller에서는 다른 편리한 방법이 많아 자주 사용되지는 않음
- Map/Model/ModelMap, @ModelAttribute메소드가 리넡하는 모델에 자동 추가됨

---

**String**

- 리턴 타입이 String이면 리턴 값은 뷰 이름으로 사용
- ModelMap 파라미터로 가져와 추가해주는 방법을 사용

```java
@RequestMapping("/hello")
public String hello(@RequestParam String name,
	Model model) {
	model.addAttribute("name", name);
	return "hello";
}
```

- 모델은 파라미터로 맵을 가져와 넣어줌
- 리턴 값은 뷰 이름을 스트링 타입으로 선언
- 모델을 추가하고 뷰 이름을 지정하는 방법 중 가장 깔끔

---

**void**

- RequestToViewNameResolver를 통해 자동생성되는 뷰 이름이 사용됨
- URL과 뷰 이름을 일관되게 통일할 수 있다면 고려

```java
@RequestMapping("/hello")
public void hello(@RequestParam String name, Model model) {
	model.addAttribute("name", name);
}
```

- URL을 따라 뷰 이름을 hello로 만들어 줌
- 여기에 ViewResolver가 prefix, suffix 를 붙여주면
- /WEB-INF/view/hello.jsp 같은 식으로 완성 가능

---

**모델 오브젝트**

- 뷰 이름은 ViewResolver로 자동생성
- 모델에 추가할 오브젝트가 하나뿐이면 사용

```java
@RequestMapping("/view")
public User view(@RequestParam int id) {
	return userService.getUser(id);
}
```

---

**Map/Model/ModelMap**

- 필요한 모델 맵은 파라미터로 받아서 사용하면 편리
- 직접 코드에 생성하는 건 비효율적

```java
@RequestMapping("/view")
public void view(@RequestParam int id, Model model) {
	model.addAttribute("userMap",userService.getUserMap(id));
}
```

---

**View**

- 뷰 이름 대신 뷰 오브젝트를 사용하고 싶다면 사용

---

**@ResponseBody**

- @RequestBody와 비슷한 방식으로 동작
- 메소드 레벨에 부여 시 메소드가 리턴하는 오브젝트는
HTTP 응답의 메세지 본문으로 전환됨

# 5. @SessionAttribues와 SessionStatus


- HTTP 요청에 의해 동작하는 서블릿은 기본적으로 상태 유지 X
- 매 요청이 독립적으로 처리됨
- 하나의 요청 처리 후 모든 리소스를 정리해버림

## 5.1. @SessionAttributes

---

```java
@Controller
@SessionAttributes("user")
public class UserController {
	
	@RequestMapping(value="/user/edit",method=RequestMethod.GET)
	public String form(@RequestParam int id, Model model) {
		model.addAttribute("user",userService.getUser(id));
		return "user/edit";
	}

	@RequestMapping(value="/user/edit", method=RequestMethod.POST)
	public String submit(@ModelAttribute User user) { ... }

}
```

- 컨트롤러 메소드가 생성하는 모델 정보 중 @SessionAttributes에 지정한 이름과 동일한 것을 세션에 저장해줌
- @ModelAttribute가 지정된 파라미터가 있을 시 세션에서 오브젝트를 가져옴

## 5.2. SessionStatus

---

- @SessionAttribute 사용시 더 이상 필요 없어지면 제거해줘야 함

```java
public String submit(SessionStatus ss) {
	ss.setComplete();
}
```

- sessionStatus.setComplete();
- 현재 컨트롤러에 의해 세션에 저장된 정보를 모두 제거