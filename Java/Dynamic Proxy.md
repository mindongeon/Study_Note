# 1. 목차

- [1. 목차](#1-목차)
- [2. Dynamic Proxy](#2-dynamic-proxy)
  - [2.1. Dynamic Proxy란?](#21-dynamic-proxy란)
    - [2.1.1. Return 값](#211-return-값)
  - [2.2. 예제 코드](#22-예제-코드)
    - [2.2.1. VO](#221-vo)
    - [2.2.2. Subject](#222-subject)
    - [2.2.3. RealSubject](#223-realsubject)
    - [2.2.4. Client](#224-client)
  - [2.3. java.lang.reflect.Proxy를 통해](#23-javalangreflectproxy를-통해)
  - [2.4. 요약](#24-요약)


# 2. Dynamic Proxy

> Proxy 객체를 직접 생성하는 것이 아닌 Runtime에 
***Interface*를 구현하는 *Class or Instance*를 만들어 내는 것**
> 

## 2.1. Dynamic Proxy란?

---

- Proxy를 직접 생성하지 않고 Runtime에 
Interface를 구현하는 친구들을 만듦
- Java의 `java.lang.reflect.Proxy` 에서 제공해주는 API를 
이용해 사용 가능

```java
public static Object newProxyInstance(
    ClassLoader loader, // 1
    Class<?>[] interfaces, // 2
    InvocationHandler h // 3
) throws IllegalArgumentException
```

1. Proxy객체를 정의하기 위한 Class Loader 지정
( Proxy 객체가 구현할 Interface에 Class Loader를 얻어오는 것이 일반적)
2. `newProxyInstance()`를 통해 생성될 Proxy 객체가 구현할 Interface 정의
3. 메소드 호출을 디스패치하기 위한 호출 핸들러

> Dispatch
어떤 메소드를 호출할 것인가를 결정하여 그것을 실행하는 과정
> 

### 2.1.1. Return 값

Class Loader에 의해 정의된 Interface를 구현한 Proxy객체의
InvocatonHandler를 가지고 있는 Object 타입의 Proxy 인스턴스

## 2.2. 예제 코드

---

### 2.2.1. VO

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

### 2.2.2. Subject

---

```java
public interface BookService {
    void printTitle(Book book);
}
```

### 2.2.3. RealSubject

---

```java
public class BookServiceImpl implements BookService {
    @Override
    public void printTitle(Book book) {
        System.out.println("title = " + book.getTitle());
    }
}
```

### 2.2.4. Client

---

```java
public static void main(String[] args) {
    BookService bs = (BookService) Proxy.newProxyInstance(
            BookService.class.getClassLoader(),
            new Class[]{BookService.class},
            (proxy, method, args1) -> {
                BookService realSub = new BookServiceImpl();
                System.out.println("pre Proxy");
                Object invoke = method.invoke(realSub, args1);
                System.out.println("after Proxy");
                return invoke;
            }
    );

    System.out.println("getClass : " + bs.getClass());

    Book book = new Book("hello");
    bs.printTitle(book);
}
```

## 2.3. java.lang.reflect.Proxy를 통해 
Proxy 인스턴스 생성시 주의점

---

- `java.lang.reflect.Proxy` 에서 제공하는 `newProxyInstance()`를 
이용 시 **Class 기반으로는 Proxy 객체를 생성 불가**

```java
// Exception in thread "main" java.lang.IllegalArgumentException:
// com.dynamicProxy.BookServiceImpl is not an interface
BookServiceImpl bookService = (BookServiceImpl) Proxy.newProxyInstance(
    BookServiceImpl.class.getClassLoader(),
    new Class[]{BookServiceImpl.class},
    (proxy, method, arguments) -> {
        //...
    }
)
```

## 2.4. 요약

---

- Spring은 내부적으로 Proxy 기술을 많이 사용
( Spring AOP, Spring Data JPA .. )
- Spring에서 Bean 등록 시 Singleton 유지를 위해 Proxy 기법을 이용
Proxy객체를 Bean으로 등록함
- Spring Data JPA에서는 JpaRepository만 상속 받아도 해당 구현체를
만들어 Bean으로 등록하는 것도 Proxy 기법을 이용함