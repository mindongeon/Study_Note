# 스프링 컨테이너란


- 코드 대신 오브젝트에 대한 제어권을 가짐 : IoC
- 스프링에서 자바 객체들을 관리하는 공간
- 자바 객체 = 빈(bean)
- 빈의 생성부터 소멸까지 관리해줌

# 종류


## BeanFactory

---

- 오브젝트의 생성, 오브젝트 사이의 런타임 관계를 설정하는 DI 관점으로 볼 경우

## ApplicationContext

---

- BeanFactory의 기능을 포괄하면서 추가적인 기능 사용

⇒ 대부분의 경우에 사용

# 만들기


1. ApplicationContext interface를 사용해 만듬
2. 빈 설정
    1. XML
        1. 생성자 방식
        2. Setter 방식
    2. XML + Componet → @Component,@Service …
    3. JAVA config → @Configuration
3. bean scope
    1. singleton : 같은 객체 주소값으로 나감
    2. prototype : 같은 객체지만 주소값 다르게  나감