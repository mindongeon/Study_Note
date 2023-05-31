# Toby Spring
# 1. 목차

- [Toby Spring](#toby-spring)
- [1. 목차](#1-목차)
- [2. Controller](#2-controller)
- [3. Controller의 종류와 Handler Adapter](#3-controller의-종류와-handler-adapter)
  - [3.1. Servlet과 SimpleServletHandlerAdapter](#31-servlet과-simpleservlethandleradapter)
    - [장점](#장점)
  - [3.2. HttpRequestHandler와 HttpRequestHandlerAdapter](#32-httprequesthandler와-httprequesthandleradapter)
  - [3.3. Controller와 SimpleControllerHandlerAdapter](#33-controller와-simplecontrollerhandleradapter)
  - [3.4. AbstractController가 제공하는 추가 기능](#34-abstractcontroller가-제공하는-추가-기능)
  - [3.5. AnnotationMethodHandlerAdapter](#35-annotationmethodhandleradapter)
- [4. 핸들러 매핑](#4-핸들러-매핑)
  - [4.1. BeanNameUrlHandlerMapping](#41-beannameurlhandlermapping)
  - [4.2. ControllerBeanNameHandlerMapping](#42-controllerbeannamehandlermapping)
  - [4.3. ControllerClassNameHandlerMapping](#43-controllerclassnamehandlermapping)
  - [4.4. SimpleUrlHandlerMapping](#44-simpleurlhandlermapping)
  - [4.5. DefaultAnnotationHandlerMapping](#45-defaultannotationhandlermapping)
- [5. 핸들러 인터셉터](#5-핸들러-인터셉터)
  - [5.1. HandlerInterceptor](#51-handlerinterceptor)
  - [5.2. Handler Interceptor 적용](#52-handler-interceptor-적용)
  - [5.3. Servlet Filter VS Handle Interceptor](#53-servlet-filter-vs-handle-interceptor)
- [6. 컨트롤러 확장](#6-컨트롤러-확장)



# 2. Controller


- Servlet이 넘겨주는 HTTP 요청은 HttpServletRequest에 담겨있음
- HttpServletRequest에서 필요한 사용자 요청 정보 추출
- 요청 검증
    - 예외 시 예외처리
- HttpServletRequest 정보를 적절한 타입으로 변환 or 가공
- Mulipart로 바이너리 정보가 전달된 경우 파일로 저장
- 사용자의 요청 모두 분석 후 서비스 계층의 비즈니스 로직 담당 메소드 호출
- 결과에 따른 뷰 결정

# 3. Controller의 종류와 Handler Adapter


- Spring MVC가 지원하는 Controller의 종류는 네 가지
⇒ Handler Adapter도 네 가지

## 3.1. Servlet과 SimpleServletHandlerAdapter

---

- 표준 Servlet
- Servlet Class를 Spring MVC의 Controller로 사용 가능
- 기존에 Servlet으로 개발된 코드를 Spring Application에 가져와 사용하고 싶은 경우 Spring MVC Controller로 등록
- Model, View를 리턴하지 않음
    
    ⇒ HttpServletResponse의 정보를 확인하는 방법 사용
    

### 장점
---
- Servlet Class 코드를 그대로 유지 → Spring Bean 등록 가능

## 3.2. HttpRequestHandler와 HttpRequestHandlerAdapter

---

- 인터페이스로 정의된 Controller 타입
- 인터페이스를 구현해 만듬
- Servlet처럼 동작하는 Controller를 만들기 위해 사용
- HTTP 프로토콜을 기반으로 한 전용 서비스를 만들때 사용할 수 있음
- Model, View 개념이 없는 로우레벨 서비스 개발 시 이용할 수 있음

## 3.3. Controller와 SimpleControllerHandlerAdapter

---

- Controller 컨트롤러는 DispatcherServlet이 컨트롤러와 주고받는 정보를 그대로 메소드의 파라미터와 리턴 값으로 가지고 있음
- Spring MVC의 가장 대표적인 컨트롤러 타입
- Controller 인터페이스를 구현하기만 하면 됨
⇒ 여타 MVC 프레임워크의 컨트롤러보다 유연하게 설계 가능
- Controller 인터페이스를 직접 구현해 컨트롤러를 만드는 것은 **권장되지 않음**
- 컨트롤러로서의 필수 기능이 구현되어 있는 AbstractController를 상속해 만드는 것이 편리해서
- AbstractController는 Controller 인터페이스를 구현한 Controller 타입의 컨트롤러

## 3.4. AbstractController가 제공하는 추가 기능

---

- synchroniceOnSession
    - HTTP세션에 대한 동기화 여부 결정
    - 사용자가 자신의 HTTP세션에 동시에 접근하는 것을 막아줌
    - 가장 안전하게 HTTP세션을 조작하는 컨트롤러 생성 가능
- supportedMethods
    - 컨트롤러가 허용하는 HTTP메소드(GET, POST 등) 지정
    - 디폴트는 모든 종류 허용
- useExpiresHeader, userCacheControlHeader, userCacheControlNoStore, cacheSeconds

## 3.5. AnnotationMethodHandlerAdapter

---

- 지원하는 컨트롤러의 타입에 제한이 없음
- 컨트롤러 하나가 하나 이상의 URL 매핑 가능
- URL 매핑을 메소드 단위로 가능
- Annotation으로 지정
- DefaultAnnotationHandlerMapping 과 같이 사용해야 함
- Spring MVC 에서 가장 인기 있는 컨트롤러 작성 방법

# 4. 핸들러 매핑

---

- **HTTP 요청 정보를 이용해 처리할 컨트롤러를 찾아주는 기능**을 가진 DispatcherServlet의 전략
- **핸들러 인터셉터를 적용**해줌
- 하나의 핸들러 매핑 전략이 여러 가지 타입의 컨트롤러 선택 가능
- Spring은 기본적으로 5개의 핸들러 매핑 지원

## 4.1. BeanNameUrlHandlerMapping

---

- URL을 HTTP요청의 URL과 비교해 일치하는 빈을 찾아줌
- URL에 ‘*,**,?’ 같은 와일드카드 사용 가능
- 컨트롤러의 개수가 많아지면 전체적인 매핑구조 파악, 관리 힘듬
    
    ⇒ 복잡한 어플리케이션에서는 잘 사용하지 않음
    
- 빈 이름에 매핑정보를 넣어 관리하기 불편

## 4.2. ControllerBeanNameHandlerMapping

---

- 빈의 아이디나 빈 이름을 이용해 매핑
- XML에서 정의 시 “/” 사용 불가

## 4.3. ControllerClassNameHandlerMapping

---

- 빈 이름 대신 클래스 이름을 URL에 매핑

## 4.4. SimpleUrlHandlerMapping

---

- URL과 컨트롤러의 매핑정보를 한곳에 모아놓을 수 있음
- 매핑정보는 SimpleUrlHandlerMapping 빈의 프로퍼티에 넣어줌
- 매핑 정보가 한곳에 모여 있어 URL 관리가 편함
- 매핑할 컨트롤러 빈의 이름을 직접 적어줘야 하기 때문에 오타 등의 오류 조심

## 4.5. DefaultAnnotationHandlerMapping

---

- @RequestMapping이라는 어노테이션 사용
- 컨트롤러 클래스나 메소드에 직접 부여
- 메소드 단위 URL 매핑 가능
⇒ 컨트롤러 개수를 줄일 수 있음
- HTTP 메소드, 파라미터, HTTP헤더 정보까지 매핑에 활용 가능
- 매핑 어노테이션의 사용 정책과 작성 기준이 필요

# 5. 핸들러 인터셉터


- DispatcherServlet이 컨트롤러를 호출하기 전과 후에 요청과 응답을 참조, 가공할수 있는 일종의 필터
- Servlet Filter와 유사한 개념
- Serlvlet Filter보다 정교하고 편리하게 인터셉터 생성 가능
- Spring Bean이기 때문에 다른 Bean 활용 가능

## 5.1. HandlerInterceptor

---

- HandlerInterceptor 인터페이스를 구현해서 만듬
- 3개의 메소드가 포함되어 있음
- **boolean preHandle( …)**
    - 컨트롤러가 호출되기 전에 실행
    - handler 파라미터는 핸들러 매핑이 찾아준 컨트롤러 빈 오브젝트
    - 리턴 값이 true 다음 단계,
    false면 작업 중단, 리턴
- **void postHandle( …)**
    - 컨트롤러를 실행하고 난 후에 호출
    - ModelAndView 타입의 정보 참조, 조작 가능
- **void afterCompletion( …)**
    - 모든 뷰에서 최종 결과를 생성하는 일을 포함한 모든 작업이 다 완료된 후 실행
    - 요청 처리 중에 사용한 리소스 반환하기 적당
- 하나 이상 등록 가능
- preHandle()은 인터셉터가 등록된 순서대로 실행
- postHandle(), afterCompletion()은 preHandle()이 실행된 순서와 반대로 실행

## 5.2. Handler Interceptor 적용

---

- 핸들러 매핑 클래스를 빈으로 등록해야 함
- 핸들러 매핑 단위로 등록

## 5.3. Servlet Filter VS Handle Interceptor

---

1. 서블릿 필터
    1. web.xml에 별도로 등록
    2. 스프링의 빈이 아님
    3. 웹 어플리케이션에 들어오는 모든 요청에 적용
    4. 스프링의 빈 사용 가능
2. 핸들러 인터셉터
    1. DispatcherServlet의 특정 핸들러 매핑으로 제한
    2. 스프링의 빈으로 등록 가능
    3. 컨트롤러 오브젝트에 접근 가능
    4. 컨트롤러가 리턴하는 정보 활용 가능
    5. web.xml을 수정하지 않아도 됨

# 6. 컨트롤러 확장


- Controller를 이용해 만든 컨트롤러는 개별 컨트롤러가 특정 클래스를 상속하도록 강제함

⇒ 인터페이스를 구현하는 방법으로 컨트롤러를 생성하고 싶음

⇒ 핸들러 어댑터를 직접 구현해 새로운 컨트롤러 타입을 도입해야 함