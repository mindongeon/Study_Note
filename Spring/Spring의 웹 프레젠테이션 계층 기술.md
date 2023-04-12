Toby Spring

# 1. 목차

- [1. 목차](#1-목차)
- [2. **Servlet Web Application Context**](#2-servlet-web-application-context)
- [3. **Spring에서 사용되는 웹 프레임워크의 종류**](#3-spring에서-사용되는-웹-프레임워크의-종류)
- [4. **Spring MVC와 DispatcherServlet 전략**](#4-spring-mvc와-dispatcherservlet-전략)
  - [4.1. DispatcherServlet](#41-dispatcherservlet)
  - [4.2. DispatcherServlet과 MVC 아키텍처](#42-dispatcherservlet과-mvc-아키텍처)
  - [4.3. **Front Controller 패턴**](#43-front-controller-패턴)
  - [4.4. **Front Controller 작동 순서**](#44-front-controller-작동-순서)
  - [4.5. **DispatcherServlet**](#45-dispatcherservlet)
    - [4.5.1. **동작 과정**](#451-동작-과정)
  - [4.6. DispatcherServlet의 DI 가능한 전략](#46-dispatcherservlet의-di-가능한-전략)
    - [4.6.1. HandlerMapping](#461-handlermapping)
    - [4.6.2. HandleAdapter](#462-handleadapter)
    - [4.6.3. HandlerExceptionResolver](#463-handlerexceptionresolver)
    - [4.6.4. ViewResolver](#464-viewresolver)
    - [4.6.5. LocaleResolver](#465-localeresolver)
    - [4.6.6. ThemeResolver](#466-themeresolver)
    - [4.6.7. RequestToViewNameTranslator](#467-requesttoviewnametranslator)


# 2. **Servlet Web Application Context**

---

스프링은 두 가지로 분리해 놓음

→ 통째로 다른 기술로 대체할 수 있게

- Root Application Context
    
    비즈니스 서비스 계층과 데이터 액세스 계층
    
- Servlet Application Context
    
    스프링 웹 기술을 기반으로 동작하는 웹 관련 빈 소유
    

# 3. **Spring에서 사용되는 웹 프레임워크의 종류**

---

1. **스프링 웹 프레임워크**
    1. Spring Servlet / [Spring MVC](/Spring/Spring%20MVC.md)
        - 스프링이 직접 제공하는 서블릿 기반의 MVC 프레임워크
        - Front Controller 의 역할을 하는
        DispatcherServlet을 핵심으로 사용
        - 다양한 종류의 컨트롤러를 동시에 사용할 수 있게 설계
        - MVC 프레임워크의 많은 기능을 자유롭게 확장 가능
        - 어노테이션 설정, 유연한 핸들러 메소드를 지원하는
        스프링 @MVC 가 대표적인 기술
        - 모든 컴포넌트는 서블릿 어플리케이션 컨텍스트의 빈으로 등록되어 동작
        ⇒ Root Context 에 존재하는 서비스 계층의 빈 사용 가능
    2. Spring Portlet
        - Portlet 은 자바 표준 기술
        - 서블릿과 유사한 Portlet Container에서 동작
        - ‘Portlet’이라는 자유로운 조합이 가능한 작은 단위의 프레젠테이션 컴포넌트를 Portlet을 지원하는 포탈 서버에 배치해 사용
        - Spring Servlet MVC 와 거의 동일한 기능 제공
2. **스프링 포트폴리오 웹 프레임워크**
    1. Spring Web Flow
        1. Spring Servlet 을 기반으로 상태유지 스타일의 웹 어플리케이션을 작성하게 해주는 프레임워크
        2. 컨트롤러를 직접 코드를 작성하지 않아도, DSL을 이용해 쉽게 개발 가능
    2. Spring JavaScript
        1. Dojo를 추상화한 것
        2. 손쉽게 Ajax 기능을 구축 가능
    3. Spring Faces
    4. Spring Web Service
        1. Spring MVC 와 유사한 방식
        2. SOAP 기반의 웹 서비스 개발 가능
        3. Object Mapping, XML 마샬링 기능 제공
        4. **마샬링 : 자바객체 → XML 변환
        5. Spring-Security 등 다양한 스프링 기능 활용 가능

# 4. **Spring MVC와 DispatcherServlet 전략**

---

## 4.1. DispatcherServlet

---

- 스프링 웹 기술의 핵심
- 스프링을 구성하는 다양한 전략을 DI로 구성, 확장하도록 만들어진 Spring Servlet/MVC 와 같은 역할
- Servlet Container가 생성하고 관리하는 오브젝트

## 4.2. DispatcherServlet과 MVC 아키텍처

---

- M : 프레젠테이션 계층의 구성요소를 정보로 담은 모델
- V : 화면 출력 로직을 담은 뷰
- C : 제어 로직을 담은 컨트롤러

## 4.3. **Front Controller 패턴**

---

- 중앙집중형 컨트롤러를 프레젠테이션 계층의 제일 앞에 둠
- 서버로 들어오는 모든 요청을 먼저 받아 처리

## 4.4. **Front Controller 작동 순서**

---

1. 클라이언트가 보낸 요청 받음
2. 공통적인 작업 먼저 수행
3. 적절한 세부 컨트롤러로 작업 위임
4. 클라이언트에게 보낼 뷰 선택
5. 최종 결과 생성


```
💡 예외 발생 시 일관된 방식으로 처리함
```

일반적으로 MVC 세 가지를 이용해 작업 수행

## 4.5. **DispatcherServlet**

---

- DispatcherServlet = Front Controller
- MVC로 구성된 프레젠테이션 계층을 만들 수 있도록 설계
- 동작구조

![Untitled](/img/Untitled.png)

### 4.5.1. **동작 과정**

---

1. **DispatcherServlet의 HTTP 요청 접수**
    
    모든 요청에 대해 공통적으로 진행해야 하는 전처리 작업이 등록된 것이 있다면 먼저 수행
    
    Ex) 공통적으로 이용 가능한 보안, 파라미터 조작, 한글 디코딩 등
    
2. **DispatcherServlet에서 컨트롤러로 HTTP 요청 위임**
    - URL, 파라미터 정보, HTTP 명령 등을 참고 후
    - 어떤 컨트롤러에게 작업을 위임할지 결정
    
    > **스프링에서는 컨트롤러를 핸들러라고 부름**
    > 
    
    **핸들러 매핑 전략**
    
    - 사용자의 요청을 기준으로 어떤 핸들러에게 작업을 위임할지 결정해주는 것
    - 전략이라고 부르는 이유는 DI의 전략 패턴이 적용됨
    - DS의 수정 없이도 DI를 통해 확장 가능
    - 어댑터 패턴을 통해 컨트롤러의 종류에 따라 적절한 어댑터 사용
    - 웹 요청 전달시 모든 요청 정보가 담긴
    HttpServletRequest 타입의 오브젝트 전달
    - HttpServletResponse 도 함께 전달
    - 컨트롤러가 리턴값으로 돌려주는 대신 HttpServletResponse 오브젝트 안에 직접 넣는 경우 있음
3. **컨트롤러의 모델 생성과 정보 등록**
    - 컨트롤러는 어떤 식으로든 다시 DispatcherServlet에돌려줘야 할 두가지 정보
        - Model
        - View
    - 보통 Model = Map에 담긴 정보
4. **컨트롤러의 결과 리턴 : 모델과 뷰**
    - View 결정
    - View도 하나의 Object
    - **순서**
        1. Controller가 View의 논리적인 이름을 return시
        2. DispatcherServlet의 전략인 View Resolver가 이를 이용
        3. View Object 생성
    - 대표적인 뷰 : JSP / JSTL
    - Controller는 JSP View Template 와 결합될 JstlView Class로 만들어진 View Object가 사용할 JSP Template 파일 이름을 리턴
    - DispatcherServlet으로
5. **DispatcherServlet의 View 호출, 모델 참조**
    - DispatcherServlet이 Controller에게 Model, View 받음
    - View Object 에게 Model 전달
    - 클라이언트에게 돌려줄 최종 결과물 생성 요청
    - JstlView는 Controller가 돌려준 JSP View Template의 이름을 가져다 HTML 생성
    - 동적인 부분은 Model을 참고해 채워줌
    - 최종 결과물은 HttpServletResponse Object에 담김
6. **HTTP 응답 돌려주기**
    - 등록된 후처리기 확인
    - 있다면 후속 작업 진행
    - HttpServletResponse에 담긴 최종 결과 Servlet Container에게 돌려줌
    - Servlet Container는 HttpServletResponse에 담긴 정보를 HTTP 응답으로 만듬
    - 사용자의 브라우저나 클라이언트에게 전송
    - 작업 종료

## 4.6. DispatcherServlet의 DI 가능한 전략

---

### 4.6.1. HandlerMapping

---

- URL과 요청 정보를 기준으로 어떤 컨트롤러를 사용할지 결정하는 로직 담당
- 인터페이스 구현해서 사용
- DispatcherServlet은 하나 이상의 핸들러 매핑을 가질 수 있음

### 4.6.2. HandleAdapter

---

- HandlerMapping으로 선택한 컨트롤러를 DispatcherServlet이 호출할 때 사용
- 컨트롤러 타입에 따라 호출 방법이 달라짐

### 4.6.3. HandlerExceptionResolver

---

- 예외 발생시 처리하는 로직을 가진 전략

### 4.6.4. ViewResolver

---

- 컨트롤러가 리턴한 View 이름을 참고해서 적절한 View Object를 찾아주는 로직을 가진 전략

### 4.6.5. LocaleResolver

---

- 지역 정보를 결정해주는 전략
- HTTP 헤더의 정보를 보고 지역정보를 설정

### 4.6.6. ThemeResolver

---

- 테마 정보를 결정해주는 전략

### 4.6.7. RequestToViewNameTranslator

---

- 컨트롤러에서 View의 이름이나 View Object를 제공해주지 않았을 경우 URL과 같은 요청정보를 참고해 자동으로 View의 이름을 생성해주는 전략