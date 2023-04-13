# RequestContextHolder

## 개요
---
`RequestContextHolder`는 Spring에서 전역으로 Request에 대한 정보를 가져오고자 할 때 사용하는 유틸성 클래스이다.

주로 [Controller](/Spring/Controller.md)외의 Business Layer 등에서 Request 객체를 참고하려할 때 사용한다. 매번 method의 call param으로 넘기기 애매할 때 주로 사용한다.

## 사용
---
`RequestContxtHolder.getRequestAttributes()`를 사용하면 Attribute만 가져와 아래와 같이 Wrapping해 사용한다.
```java
HttpServletRequest servletRequest = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();
```

## 생성
---
`RequestContextHolder`가 초기화되는 것은 `Servlet`이 생성될 때 이다. `Http Request`가 오는 시점에 생성 및 초기화가 되고, `Business Layer`를 거친 뒤 `Servlet`이 destory될 때 clean된다.

`static`으로 선언되어 클래스 생성과 동시에 만들어지고, `Servlet`이 요청/종료 될 때마다 일일히 값을 채워넣고, 없애는 작업을 한다.

1. Http 요청이 오면 `FrameworkServlet`클래스의 `processRequest` 메소드가 호출된다.
2. 이전 `attribute`가 남아있는지 확인 후 새로운 `requestAttributes`를 만든다.
3. `ContextHolder`를 초기화시킨다.
4. `RequestContextHolder`에 `attribute`를 넣어준다.

이렇게 되면 Request Thread. 즉, Tomcat [Thread](Process와%20Thread.md)에서는 어디서는 static한 값을 꺼내 쓸 수 있다.

Tomcat의 경우, `nio-8080-exec-1`과 같은 쓰레드가 생성, `Servlet`을 서빙한다. `@Component`, `@Service`, `@Repository` 등은 Business Layer로 Spring Container에 등록되어 같은 쓰레드에서 동작하므로 잘 동작이 된다.

## 동작 원리
---
static한 ThreadLocal에 값을 Write/Read 하는 방식이다. 그래서 같은 쓰레드에서는 값을 꺼내쓸 수 있다.

다른 쓰레드(new Thread, executor를 사용한 ThreadPool에서의 참조 등) 에서는 `ReqeustContextHolder`의 `Request`값을 꺼내 쓸 수 없다. 새로운 쓰레드를 생성하는 순간 DispatcherServlet의 범위에서 벗어나 새로운 쓰레드가 생성되기 때문이다.

## 출처
---
[Spring RequestContextHolder](https://gompangs.tistory.com/entry/Spring-RequestContextHolder)