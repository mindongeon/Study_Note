# 1. 목차

- [1. 목차](#1-목차)
- [2. Spring AOP](#2-spring-aop)
- [3. Proxy 메커니즘](#3-proxy-메커니즘)
  - [3.1. JDK Dynamic Proxy](#31-jdk-dynamic-proxy)
  - [3.2. InvocationHandler](#32-invocationhandler)
  - [3.3. CGLIB Proxying](#33-cglib-proxying)
    - [3.3.1. CGLIB Proxying이 작동하는 규칙](#331-cglib-proxying이-작동하는-규칙)
    - [3.3.2. JDK Dynamic Proxy VS CGLIB Proxy](#332-jdk-dynamic-proxy-vs-cglib-proxy)


# 2. [Spring AOP](/Spring/AOP.md)

객체 지향인 OOP를 보완하기 위해 Spring에서 제공함

OOP의 주된 모듈이 클래스이기 때문에
AOP는 유사한 목적을 갖는 클래스들을 관통할 수 있게
Aspect 단위로 모듈화함

> 이를 위해 Proxy를 사용하게 되는데 
Spring에서는 기본적으로 두가지 방식을 통해 AOP를 제공함
> 
- JDK Proxy : Java 내부에 구현되어 있는 Proxy
- CGLIB Proxy : Open Source로 구현되어 있는 Proxy

# 3. Proxy 메커니즘

## 3.1. JDK Dynamic Proxy

- [Reflection](/Java/Reflection.md)을 통해 동적으로 [Proxy](/Java/Dynamic%20Proxy.md) 객체 생성
- **interface**를 기준으로 Proxy 생성
- JDK Dynamic Proxy 의 핵심 : **InvocationHandler**

## 3.2. InvocationHandler

프록시로 선언한 method-call이 일어날 때 지정한 핸들러를 통해

메서드가 수행되도록 조작해줌

## 3.3. CGLIB Proxying

- 클래스 상속을 통해 proxy 객체 생성
- **interface, class** 기준으로 proxy 생성
- 타겟 클래스의 바이트코드를 조작하여 재정의함
⇒ final 사용 불가

---

- Spring [@Configuration](/Spring/A.Configuraion.md) 클래스는 등록된 Bean을 [싱글톤](/Design%20Pattern/Singleton%20Pattern.md)으로 등록함
- Spring이 `@Configuration` 클래스 주위에 CGLIB 프록시를 생성함
- 해당 Bean을 호출하게 되면 새로운 빈을 생성하기 전에
- 해당 Bean이 존재하는지 확인하고 존재한다면
- 존재하는 Bean이 리턴됨

```java
if(Bean != null) {
	return Bean;
} else {
	return new Bean();
}
```

위와 비슷한 로직이 실행되는 듯

- `@Bean`을 사용한 메소드가 static인 부분이 있으면
- `@Configuration`은 해당 클래스를 CGLIB Proxy로 감싸지 않음

⇒ **Singleton 이 아닌 Prototype으로 실행됨**

- `@Configuration(proxyBeanMethods = false)` 옵션으로 해제 가능

---

### 3.3.1. CGLIB Proxying이 작동하는 규칙

- `@Configuration` 클래스는 final이 되면 안됨
- `@Bean` 메서드는 final이나 private이 되면 안됨

### 3.3.2. JDK Dynamic Proxy VS CGLIB Proxy

- 차이점
    
    JDK 는 `InvocationHandler`가 proxy로 선언한 method-call이
    일어날 때 지정한 핸들러를 통해 메서드가 수행되도록 조작
    
    CGLIB는 `Enhancer`를 통해 interface가 아닌 구현체가 등록
    
    `MethodInterceptor`에 의해 method-call이 일어날 때 지정된
    
    핸들러를 통해 메서드가 수행
    
- Spring Framework
    - Spring AOP에서는 기본적으로 JDK dynamic proxy를 사용
    - 인터페이스를 구현여부에 따라 선택적으로 사용
    - proxy-target-class 설정에 따라 선택 가능
- Spring Boot
    - CGLIB proxy가 기본적으로 사용됨
    - Reflection을 사용하는 JDK dynamic proxy 보다 
    `unexpected case exception` 발생 가능성이 적어서
    

- 선생님
    
    [https://taes-k.github.io/2021/02/07/spring-aop-proxy/](https://taes-k.github.io/2021/02/07/spring-aop-proxy/)