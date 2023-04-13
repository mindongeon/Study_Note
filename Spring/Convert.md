# 1. 목차

- [1. 목차](#1-목차)
- [2. **Converter와 Formatter의 작동 원리**](#2-converter와-formatter의-작동-원리)
	- [2.1. Converter](#21-converter)
	- [2.2. Formatter](#22-formatter)
- [3. PropertyEditor](#3-propertyeditor)
- [4. Converter](#4-converter)
- [5. Formatter](#5-formatter)
- [6. ConversionService](#6-conversionservice)
- [7. 예시 코드](#7-예시-코드)
	- [7.1. @Before](#71-before)
	- [7.2. primitive()](#72-primitive)
	- [7.3. andExpect( )](#73-andexpect-)
	- [7.4. valueOf()](#74-valueof)
	- [7.5. custom()](#75-custom)



# 2. **Converter와 Formatter의 작동 원리**


## 2.1. Converter

---

```
💡 Converter 인터페이스를 상속받아 구현해 사용
S의 타입을 받아 T의 타입으로 리턴
```

```java
@FunctionalInterface
public interface Converter<S, T> {
    @Nullable
    T convert(S source);

    default <U> Converter<S, U> andThen(Converter<? super T, ? extends U> after) {
        Assert.notNull(after, "After Converter must not be null");
        return (s) -> {
            T initialResult = this.convert(s);
            return initialResult != null ? after.convert(initialResult) : null;
        };
    }
}
```

## 2.2. Formatter

---

```
💡 Formatter 인터페이스를 상속받아 구현해 사용
```

```java
public interface Formatter<T> extends Printer<T>, Parser<T> {}

@FunctionalInterface
public interface Printer<T> {
	String print(T object, Locale locale);
}

@FunctionalInterface
public interface Parser<T> {
	T parse(String text, Locale locale) throws ParseException;
}
```

# 3. PropertyEditor

---

- 자바에서 기본 데이터 타입을 위해 제공
- 자바에서 제공하는 PropertyEdiotr는 기본적인 타입만 제공해
- 스프링은 설정 등의 편의를 위해 PropertyEditor 추가 제공
- 자바빈 규약에 따라 문자열을 특정 타입의 프로퍼티로 변환 시 사용
- PropertyEditor 인터페이스를 상속 받아 구현
- 문자열과 오브젝트 사이의 양방향 변환 기능 제공
- 매번 바인딩을 할 때마다 새로운 오브젝트를 만들어야 함 ( 단점 )

# 4. Converter

---

- 변환 과정에서 메소드가 한 번만 호출됨
- 멀티스레드 환경에서 안전하게 공유해서 사용 가능
- PropertyEditor처럼 문자열과 오브젝트 사이의 상호 변환이라는 특화된 변환 작업보다 유연
- 특화되어 있지 못해 불편하기도 함 ⇒ Formatter

# 5. Formatter

---

- Converter의 특화되어 있지 않아 불편한 점과 반대로
- 오브젝트 → 문자열, 문자열 → 오브젝트 기능에 특화
- GenericConversionService에 직접 등록이 불가,
- FormattingConversionService를 통해서만 적용 가능
- FormattingConversionServiceFactoryBean을 사용시
- 자동으로 `@NumberFormat`, `@DateTimeFormat`
- 두가지 포맷터가 등록됨
- 지역 정보를 사용하고 싶을 때 사용

# 6. ConversionService

---

- 타입과 타입간의 변환을 처리하기 위한 기능을 정의
- 컨트롤러에서 사용시 ConversionService를 통해 WebDataBinder에 설정해줘야 함
- 여러 종류의 컨버터를 이용해서 하나 이상의 타입 변환 서비스를 제공해주는 오브젝트 생성 시 사용되는 인터페이스

```
💡 WebDataBinder

- 요청으로 넘어온 값들을 실제 객체에 바인딩하는 과정의 중간 역할
- 타입변환, 데이터 검증을 함
- @InitBinder를 사용해 초기화 가능
```
![Untitled](/img/Untitled%20(1).png)

# 7. 예시 코드

---

## 7.1. @Before

---

```java
@Before
	public void setup() throws Exception {
		//FormattingConversionService : 포맷터를 모아서 편리하게 사용할 수 있는 기능
		//DefaultFormattingConversionService : 컨버터, 포맷터 등록 및 사용 가능
		FormattingConversionService cs = new DefaultFormattingConversionService();

		//Converter Registry의 메소드
		//포맷터 추가
		cs.addFormatterForFieldAnnotation(new MaskFormatAnnotationFormatterFactory());

		/*
			standaloneSetup : MockMvc에서 지원하는 메소드
			컨트롤러를 파라미터로 받아 MockMvc빌더를 리턴
		*/
		this.mockMvc = standaloneSetup(new ConvertController())
				//mockMvcBuilder에 컨버젼 추가
				.setConversionService(cs)
				//항상 검증
				.alwaysExpect(status().isOk())
				//mockMvc 생성
				.build();
	}
```

## 7.2. primitive()

---

```java
@Test
	public void primitive() throws Exception {
		this.mockMvc.perform(get("/convert/primitive").param("value", "4"))
		.andDo(print())
				.andExpect(content().string("Converted primitive 4"));
	}

//perform( ) 요청에 대한 설정
	//param( ) 쿼리 스트링 설정
//andExpect( ) 응답 검증
	//content( ) 응답 본문 검증
```

```java
@GetMapping("primitive")
	public String primitive(@RequestParam Integer value) {
//@RequestParam : Form 형태로 들어온 값 ex) input
		return "Converted primitive " + value;
	}
```

- **응답**
    
    ```java
    MockHttpServletRequest: //spring에서 제공
          HTTP Method = GET //http 메소드 종류
          Request URI = /convert/primitive //요청 url
           Parameters = {value=[4]} //요청 파라미터
              Headers = [] //헤더 정보
                 Body = <no character encoding set>
        Session Attrs = {}
    
    Handler: // Controller
                 Type = org.springframework.samples.mvc.convert.ConvertController
               Method = org.springframework.samples.mvc.convert.ConvertController#primitive(Integer)
    
    Async: // 비동기 여부
        Async started = false
         Async result = null
    
    Resolved Exception: //해결된 예외
                 Type = null
    
    ModelAndView: // 전달할 모델, 뷰
    			// 모델 : 뷰로 전달할 데이터
            View name = null // View Resolver 가 이용해 View 생성
                 View = null
                Model = null
    
    FlashMap: // 사용 후 삭제되는 데이터
           Attributes = null
    
    MockHttpServletResponse:
               Status = 200 // 성공
        Error message = null
              Headers = [Content-Type:"text/plain;charset=ISO-8859-1", Content-Length:"21"]
    					//일반적인 text, ISO-8859-1로 인코딩됨,본문의 바이트 길이
         Content type = text/plain;charset=ISO-8859-1
                 Body = Converted primitive 4
        Forwarded URL = null //이동 대상의 경로
       Redirected URL = null //리다이렉트 경로
              Cookies = []
    ```
    

## 7.3. andExpect( )

---

**인수에 실행결과를 검증**하는 MockMvcResultMatchers에서 제공하는 ResultMatcher 지정

- status : [HTTP 상태 코드](Http%20Status.md) 검증
- header : 응답 헤더의 상태 검증
- cookie : 쿠키 상태 검증
- content : 응답 본문 내용 검증
- view : 컨트롤러가 반환한 뷰 이름 검증
- model : 스프링 MVC 모델 상태 검증

## 7.4. valueOf()

---

```java
public final class SocialSecurityNumber {

	@MaskFormat("###-##-####")
	private final String value;

	public SocialSecurityNumber(String value) {
		this.value = value;
	}

//	@MaskFormat("###-##-####")
	public String getValue() {
		return value;
	}

// 정적 팩토리 메소드
// 생성자와 같은 역할을 하면서
// 리턴값을 줄 수 있음
// 장점
// 1. 이름을 가질수 있음
// 2. 호출 때마다 객체를 생성할 필요 없음
// 3. 하위 자료형 객체를 반환할 수 있음
// 4. 객체 생성을 캡슐화할 수 있음
	public static SocialSecurityNumber valueOf(@MaskFormat("###-##-####") String value) {
		return new SocialSecurityNumber(value);
	}
```
[정적팩토리메소드](/Design%20Pattern/Static%20Factory%20Method)
```java
@Test
	public void valueOf() throws Exception {
		this.mockMvc.perform(get("/convert/value?value=123456789")) //객체 자체를 찍어서 항상 true
			 .andDo(print()).andExpect(content().string(startsWith(
						"Converted value object org.springframework.samples.mvc.convert.SocialSecurityNumber")));
	}
```

## 7.5. custom()

---

```java
@Test
	public void custom() throws Exception {
		this.mockMvc.perform(get("/convert/custom?value=123-45-678C"))
			.andDo(print())	.andExpect(content().string("Converted '12345678' with a custom converter"));
								// '12345678C'가 나와야 함
	}
```