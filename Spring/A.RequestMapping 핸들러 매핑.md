Toby Spring

- **@MVC의 가장 큰 특징**
핸들러 매핑과 핸들러 어댑터의 대상이 오브젝트가 아닌 메소드

# 클래스/메소드 결합 매핑정보


- DefaultAnnotationHandlerMapping은 매핑정보로 `@RequestMapping`을 활용
- `@RequestMapping`은 타입 레벨(클래스, 인터페이스), 메소드 레벨 사용 가능
- 스프링은 `@RequestMapping`의 정보를 결합 → 매핑정보 생성
- 타입 레벨의 `@RequestMapping` 정보를 기준
메소드 레벨의 `@RequestMapping` 정보는 타입 레벨의 매핑을 더 세분화하는데 사용

```
💡 타입 레벨이 메소드 레벨의 공통 정보
```

## @RequestMapping 어노테이션

---

- `String[] value()` : URL 패턴
    - `@RequestMapping(”/hello”)`
    - `@RequestMapping(”/hello/{userId}”)`
    - `@RequestMapping( { ”/hello”, ”/hi” } )`
    
    “/hello” 라고 정의 시 “/hello.do”, “/hello.html” 도 자동 매핑됨
    
- `RequestMethod[] method()` : HTTP 요청 메소드
    
    RequestMethod : HTTP 메소드를 정의한 enum
    
    GET, HEAD, POST, PUT, DELETE, OPTIONS, TRACE
    
    - `@RequestMapping(value=”/user/add”, method=RequestMethod.GET)`
    - `@RequestMapping(value=”/user/add”, method=RequestMethod.POST)`
- `String[] params()` : 요청 파라미터
    
    요청의 파라미터와 그 값을 비교 후 매핑
    
    같은 URL을 사용해도 HTTP 요청 파라미터에 따른 별도 작업 가능
    
    - /user/edit?type=admin ⇒
    `RequestMapping(value=”/user/edit”, params=”type=admin”)`
    - /user/edit?type=member ⇒
    `RequestMapping(value=”/user/edit”, params=”type=member”)`
    - type 파라미터가 아예 존재하지 않는 경우에만 매핑 ⇒
    `RequestMapping(value=”/user/edit”, params=”!type”)`
    
- `String[] headers()` : HTTP [헤더](/Spring/Header.md)
    
    HTTP 헤더 정보에 따른 매핑
    
    - `RequestMapping(value=”/user/edit”, headers=”content-type=text/*”)`

## 타입 레벨 매핑과 레벨 매핑의 결합

---

타입(클래스, 인터페이스) 레벨에 붙는 `@RequestMapping`은 타입 내의 모든 매핑용 메소드의 공통 조건 지정 시 사용

```java
@RequestMapping("/user") //타입 레벨 매핑
public class UserController {}
```

## 타입 레벨 단독 매핑

---

핸들러 매핑 = 핸들러 오브젝트를 결정하는 전략

```java
@RequestMapping("/user/*")
public class UserController {
	@RequestMapping
	public String add(...) { ...}
	@RequestMapping
	public String edit(...) { ...}
}
```

⇒ /user/add → public String add

⇒ /user/edit → public String edit

# 타입 상속과 매핑


`@RequestMapping`이 적용된 클래스를 상속해서 컨트롤러로 사용하는 경우

1. `@RequestMapping` 정보는 상속됨
`@RequestMapping` 재정의시 슈퍼 클래스의 정보는 무시
2. 인터페이스의 `@RequestMapping`은 인터페이스를 구현한 클래스의 매핑정보로 사용

## 매핑정보 상속의 종류

---

### 상위 타입과 메소드의 @RequestMapping 상속

---

```java
@RequestMapping("/user")
public class Super {
	@RequestMapping
	public String list() { ... }
}

public class Sub extends Super { } 
```

Sub클래스를 컨트롤러로 등록하면 /user/list URL은 `list()` 메소드로 매핑됨

```java
public class Sub extends Super {
	@Override
	public String list() { ... }
}
```

[오버라이드](/Java/Overriding.md)한 경우 Sub의 `list()`로 매핑됨

### 상위 타입의 @RequestMapping과 하위 타입 메소드의 @RequestMapping 결합

---

슈퍼클래스에만 `@RequestMapping`이 선언되어 있고,

서브클래스 타입 레벨에는 아무 매핑정보가 없고 메소드에만 `@RequestMapping`이 있는 경우 

```java
@RequestMapping("/user") public class Super { }

public class Sub extends Super {
	@RequestMapping("/list")
	public String list() { ... }
}
```

/user/list URL은 Sub클래스의 `list()`로 매핑됨

### 상위 타입 메소드의 @RequestMapping과 하위 타입의 @RequestMapping 결합

---

```java
public class Super {
	@RequestMapping("/list")
	public String list() { ... }
}

@RequestMapping("/user")
public class Sub extends Super { }
```

/user/list URL은 Sub가 상속받은 `list()`에 매핑

### 하위 타입과 메소드의 @RequestMapping 재정의

---

서브 클래스에서 재정의 시 서브 클래스를 따라감

### 서브클래스 메소드의 URL 패턴 없는 @RequestMapping 재정의

---

패턴 없이 재정의하면 슈퍼 클래스의 `@RequestMapping`를 따라감

## 제네릭스와 매핑정보 상속을 이용한 컨트롤러 작성

---

### 전형적인 CRUD와 검색 기능 컨트롤러

---

```java
public class UserController {
	UserService s;

	public void add(User user) {...}
	public void update(User user) {...}
	public User view(Integer id) {...}
	public void delete(Integer id) {...}
	public List<User> list() {...}
}
```

### [제네릭](/Java/Generic.md) [추상 클래스](/Java/Abstract%20Class.md)

---

```java
public abstract class GenericController<T,K,S> {
	S service;
	@RequestMapping("/add")
	public void add(T entity) { ... }
	@RequestMapping("/update")
	public void update(T entity) {...}
	@RequestMapping("/view")
	public T view(K id) {...}
	@RequestMapping("/delete")
	public void delete(K id) {...}
	@RequestMapping("/list")
	public List<T> list() {...} 
}
```

모든 CRUD 컨트롤러에서 상속받을 수 있게 만들어진 제네릭 추상클래스

### 제네릭 컨트롤러를 상속해서 만든 UserController

---

```java
public class UserController 
extends GenericController<User, Integer, UserService> {}
```

모델 타입 T, 기본키 타입 K, 서비스 S

### 추가 컨트롤러 메소드를 가진 UserController

---

```java
@RequestMapping("/user")
public class UserController
extends GenericController<User, Integer, UserService> {
	@RequestMapping("/login")
	public String login(String userId, String pw) { ... }
}
```

상속에 따른 `@RequestMapping` 활용