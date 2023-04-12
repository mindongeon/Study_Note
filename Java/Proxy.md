# 1. 목차

- [1. 목차](#1-목차)
- [2. Proxy](#2-proxy)
  - [2.1. Proxy란](#21-proxy란)
  - [2.2. Proxy Pattern](/Design%20Pattern/Proxy%20Pattern.md)
  - [2.3. 예시](#23-예시)
    - [2.3.1. VO](#231-vo)
    - [2.3.2. Subject](#232-subject)
    - [2.3.3. RealSubject](#233-realsubject)
    - [2.3.4. Proxy](#234-proxy)
    - [2.3.5. Client](#235-client)
  - [2.4. 요약](#24-요약)
    - [2.4.1. 선생님](#241-선생님)



# 2. Proxy

## 2.1. Proxy란

---

- RealSubject는 자신의 기능에만 집중,
그 이외 **부가 기능을 제공, 접근 제어 역할은 Proxy 객체에 위임**
- SOLID 중 SRP를 지향하는 코드로 작성 가능

## 2.2. [Proxy Pattern](/Design%20Pattern/Proxy%20Pattern.md)

## 2.3. 예시

---

### 2.3.1. VO

---

```java
public class Book {
    private String title;
    public Book(String title) {
        this.title = title;
    }
    public String getTitle() {
        return title;
    }
}
```

### 2.3.2. Subject

---

```java
public interface BookService {
    void printTitle(Book book);
}
```

### 2.3.3. RealSubject

---

```java
public class BookServiceImpl implements BookService{
    @Override
    public void printTitle(Book book) {
        System.out.println("title = " + book.getTitle());
    }
}
```

### 2.3.4. Proxy

---

```java
public class BookServiceImplProxy implements BookService{
    BookService bs = new BookServiceImpl();
    @Override
    public void printTitle(Book book) {
        System.out.println("pre proxy");
        bs.printTitle(book);
        System.out.println("after proxy");
    }
}
```

### 2.3.5. Client

---

```java
public static void main(String[] args) {
        BookService bs = new BookServiceImplProxy();
        bs.printTitle(new Book("hello"));
    }
```

## 2.4. 요약

---

- Proxy는 RealSubject의 대리자 같은 역할
- Client에서 구현체로 RealSubject가 아닌 Proxy로 사용
- RealSubject에 부가 기능을 추가하지 않고
Proxy에 접근 제어, 부가 기능을 추가하여 사용
- 직접 Proxy를 구현, 작성하면 번거롭고 이 후 확장 시
많은 제약조건, 중복 코드 발생

⇒ 개선하고자 [Dynamic Proxy](/Java/Dynamic%20Proxy.md) 라는 기술이 도입됨

### 2.4.1. 선생님

---

[https://velog.io/@dev_leewoooo/Proxy-pattern이란-with-Java](https://velog.io/@dev_leewoooo/Proxy-pattern%EC%9D%B4%EB%9E%80-with-Java)