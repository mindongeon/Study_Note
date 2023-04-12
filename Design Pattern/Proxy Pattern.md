# Proxy Pattern
---
Client는 Interface “SubJect”를 의존하고 있고,

해당 Interface를 Proxy와 RealSubject가 구현

![Proxy_pattern](/img/Proxy_pattern.svg.png)
## 특징
---
- RealSubject 와 같은 이름의 메서드를 구현
- RealSubject 에 대한 참조 변수 가짐
- RealSubject 의 메소드를 호출, 그 결과를 클라이언트에게 반환
- RealSubject 메서드의 호출 전, 후에 별도의 로직 수행 가능