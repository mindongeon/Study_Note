Toby Spring

```
💡 Java Enterprise Application 개발에 사용되는 Application Framework.
Application Framework는 Application 개발을 빠르고 효율적으로 할 수 있도록 Application의 바탕이 되는 틀과 공통 프로그래밍 모델, 기술 API를 제공.
```

# Application의 기본 틀 - Spring Container


1. Spring은 Spring Container 또는 Application Context라고 불리는 Spring Runtime Engine을 제공한다.
2. Spring Container는 설정정보를 참고, Application을 구성하는 Object를 생성, 관리한다.
3. Spring Container는 독립적으로 동작 가능하지만, 보통 웹 모듈에서 동작하는 서비스나 서블릿을 등록해 사용한다.
4. Spring을 사용하려면 먼저 Spring Container를 다루는 방법, Spring Container가 Application Object를 이용할 수 있도록 설정정보를 작성하는 방법을 알아야한다.

# 공통 프로그래밍 모델 - IoC/DI, 서비스 추상화, AOP


## 프로그래밍 모델이란,

---

- Framework에서 제공하는 Application을 구성하는 Object의 생성, 동작 방식에 대한 틀이다.
- Application Code가 어떻게 작성돼야 하는지에 대한 기준도 제시한다.
- Spring은 세 가지 핵심 프로그래밍 모델을 지원한다.

### 1. IoC/DI

---

- Object의 생명주기, 의존관계에 대한 프로그래밍 모델이다.
- 유연하고 확장성이 뛰어난 코드를 만들 수 있게 도와주는 객체 지향 설계 원칙과 디자인 패턴의 핵심 원리를 담고 있다.
- Spring이 직접 제공하는 모든 기술과 API, 컨테이너는 IoC/DI 방식으로 작성되어 있다.

### 2. 서비스 추상화

---

- 구체적인 기술과 환경에 종속되지 않도록 유연한 추상 계층을 두는 방법이다.
- Spring은 서비스 추상화를 통해 환경, 서버, 특정 기술에 종속되지 않고 이식성이 뛰어나며 유연한 Application을 만든다.

### 3. AOP

---

- Application Code에 산재해서 나타나는 부가적인 기능을 독립적으로 모듈화하는 프로그래밍 모델이다.
- Spring은 AOP를 이용해, 다양한 엔터프라이즈 서비스를 적용하고도 깔끔한 코드를 유지할 수 있게 해준다.

## 기술 API

---

- UI 작성, Web Presentation Layer, Business Service Layer, Service Layer, Domain Layer, Data Access Layer 등에서 필요한 주요 기술을 지원한다.