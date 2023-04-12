# Spring AOP와 AspectJ
---
## 성능과 목적
---
[Spring AOP](/Spring/AOP.md)는 개발자가 겪을 공통적인 문제를 해결하고자 Spring [IoC](/Spring/IoC.md)를 통한 간단한 AOP 구현이 목적이다.   
완전한 AOP를 의도한 것이 아닌 [Spring Container](/Spring/Spring%20Container.md)에 의해 관리되는 Bean에만 적용이 가능하다.

`AspectJ`는 완전한 AOP를 제공하는 것이 목적인 기술이다. Spring AOP보다 강력하지만 복잡하다.   
모든 객체에 적용이 가능하다.

## Weaving
---
AspectJ와 Spring AOP 모두 각기 다른 방식의 Weaving을 사용한다.

AspectJ는 세가지 방식의 Weaving을 사용한다.
- 컴파일 시점 Weaving : AspectJ 컴파일러가 Aspect 코드와 어플리케이션의 소스 모두 입력받아 Weaving된 class파일을 생성한다.
- 컴파일 후 Weaving : 바이너리 Weaving으로 알려져 있다. 이미 존재하는 class파일과 jar파일을 Weaving하기 위해 사용된다.
- 로드 시점 Weaving : 바이너리 Weaving과 유사하나 Weaving 시점이 class 파일이 JVM에 로드될 때까지 연기된다.

AspectJ는 컴파일 시점과 로드 시점 Weaving을 사용하고,   
Spring AOP는 런타임 Weaving을 사용한다.
런타임 Weaving은 Aspect가 대상 객체의 [Proxy](/Spring/AOP%20Proxy.md)(JDK Dynamic Proxy나 CGLIB Proxy)를 사용하는 어플리케이션의 실행 시에 Weaving된다.

## 내부 구조와 어플리케이션
---
Spring AOP는 Proxy기반 AOP 프레임 워크이다. 즉, 대상 객체에 Aspect를 적용하기 위해서 대상 객체의 Proxy를 생성한다는 것이다.
두 가지의 방식을 사용한다.
- JDK Dynamic Proxy : Spring AOP에서 선호되는 방식이다. 언제든지 대상 객체가 한 개의 인터페이스를 구현하면 JDK Dynamic Proxy를 사용할 수 있다.
- CGLIB Proxy : 대상 객체가 인터페이스를 구현하지 않는 경우 CGLIB Proxy를 사용할 수 있다.

AspectJ의 경우 클래스들이 Aspect와 함께 바로 컴파일되기 때문에 런타임시 아무것도 하지 않는다. 또한 Spring AOP와 달리 어떠한 디자인 패턴도 요구하지 않는다. Aspect를 코드에 Weaving하기 위해, AspectJ compiler를 도입한다. 이 컴파일러를 통해 프로그램을 컴파일하고 미니 런타임 라이브러리를 추가해 동작시킨다.

## Joinpoint
---
흩어진 관심사와 Aspect는 [final클래스](/Java/Final.md)에 적용할 수 없다. final클래스는 [오버라이드](/Java/Overriding.md)될 수 없고, RuntimeException을 발생시킬 수 있기 때문이다.
static과 final메소드도 동일하다. Spring Aspect는 그 메소드들이 오버라이드될 수 없기 때문에 적용될 수 없다. 이러한 한계 때문에 Spring AOP는 메소드 실행시에만 Joinpoint를 지원한다.

AspectJ의 경우 런타임 이전에 실제 코드에 흩어진 관심사(Cross-Cutting Concerns)를 Weaving한다. Spring AOP와 달리, 클래스 세분화를 필요로 하지 않아서 다른 많은 Joinpoint를 지원한다.

| Joinpoint | Spring AOP | AspectJ |
| --- | --- | --- |
| 메소드 호출 | X | O |
| 메소드 실행 | O | O |
| 생성자 호출 | X | O |
| 생성자 실행 | X | O |
| static 초기화 실행 | X | O |
| 객체 초기화 | X | O |
| 필드 참조 | X | O |
| 필드 값 변경 | X | O |
| 핸들러 실행 | X | O |
| Advice 실행 | X | O |

Spring AOP는 동일 클래스 내 메소드 호출을 대상으로 Aspect 적용을 지원하지 않는다. 동일 클래스 내 다른 메소드를 호출할 때 Spring AOP에서 제공하는 Proxy메소드가 호출되지 않기 때문이다. 사용하고 싶다면, 메소드를 각기 다른 Bean으로 분리해야 한다.

## 간편성
---
Spring AOP는 구현시에 다른 외장 컴파일러를 필요로하지 않기 때문에 간편하다. 런타임 Weaving을 사용하기 때문에 일반적인 구현에 완벽하게 부합한다.

AspectJ는 AspectJ Compiler를 도입해야하며, 모든 라이브러리들을 다시 패키징해야한다.

## 성능
---
성능면에서, 컴파일 시점 Weaving은 런타임 Weaving에 비해 훨씬 빠르다.

Spring AOP는 어플리케이션의 시작시에 생성된 Proxy들을 기반으로한 프레임워크고, 적은 수의 메소드만을 지원한다.

AspectJ는 Aspect를 어플리케이션에 실행되기 전에 Weaving하기 때문에 Spring AOP와 달리 런타임시 과부하가 없다.

벤치마킹을 해보면 AspectJ가 Spring AOP에 비해 8~35배 빠른 것을 확인할 수 있다.

## 요약
---
| Spring AOP | AspectJ |
| --- | --- |
| 순수 Java로만 구현됨 | 추가 도구(자바 프로그램)을 통해 구현됨 |
| 복잡한 과정 필요 없음 | 로드 시점 Weaving을 사용하더라도 AspectJ Compiler 필요 |
| 런타임 Weaving만 가능 | 런타임 Weaving 불가. 컴파일 시점/ 컴파일 전/로드 시점 Weaving 지원 |
| 메소드 레벨의 Weaving만 지원 | 필드, 메소드, 생성, final클래스/메소드 등 다양하게 지원 |
| Spring Container에 의해 관리되는 Bean들만 적용 가능 | 모든 객체를 대상으로 적용 가능 |
| 메소드 실행 Point Cut만 지원 | 모든 Point Cut 지원 |
| 대상 객체의 Proxy가 생성되고 Aspect는 이러한 Proxy를 대상으로 적용됨 | Aspect는 어플리케이션이 실행되기 전에 코드에 바로 Weaving됨. (런타임 이전) |
| AspectJ에 비해 훨씬 느림 | Spring AOP에 비해 빠름 |
| 배우고 적용하기 쉬움 | Spring AOP에 비해 어려움 |

## 출처
---
[spring aop vs aspectj](https://logical-code.tistory.com/118)