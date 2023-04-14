# Nested Class

자바는 class 안에 class 사용이 가능하다. 이런 클래스를 중첩 클래스 (Nested Class) 라고 한다.   
Outer Class의 멤버이며, `private`,`public`,`protected`,`package private`로만 선언 가능하다.   
(외부 클래스는 `public`,`package-private`로만 선언 가능하다.)


## 예시
---
```java
class Outer {
    ...
    class Nested {
        ...
    }
}
```
## 종류
---
1. non-static = [Inner Class](/Java/Inner%20Class.md)
2. static = Static Nested Class

### Static Nested Class

```java
class Outer {
    ...
    class Inner {
        ...
    }
    static class StaticNestedClass {
        ...
    }
}
```
Outer Class에 있는 다른 멤버에 접근할 수 없다.

## 사용하는 이유
---
1. 한 곳에서만 사용되는 클래스를 논리적으로 그룹화할 수 있다. 만약 다른 클래스에서 유용하게 사용될 경우, 해당 클래스를 그 클래스 내에 포함시켜 두 개를 함께 관리할 수 있다. 
2. 캡슐화를 높일 수 있다. A,B라는 두 개의 최상위 클래스가 있을 때, B가 A의 private 멤버에 접근해야 할 경우가 있다. 이 경우 A의 내부에 B를 선언하면 B클래스의 내부에서 A의 멤버에 접근이 가능하다. 또한 외부에서는 B를 알지 못하는 효과도 있다.
3. 가독성이 좋아지며, 유지 보수가 쉬워진다.

## Static Nested Class
---
클래스 메소드와 변수처럼 `Static Nested Class`는 외부 클래스와 관련있다. static class method처럼 외부클래스에 정의된 인스턴스 변수나 메소드를 직접 사용할 수 없다. 이러한 변수나 메소드를 사용하려면 객체 참조를 통해서만 가능하다.

다른 최상위 클래스와 마찬가지로 외부 클래스의 인스턴스 멤버와 상호작용한다. 실제로, 정적 중첩 클래스는 편의성을 위해 다른 최상위 클래스 내부에 중첩된 일반적인 최상위 클래스이다.

최상위 클래스와 마찬가지로 같은 방법으로 인스턴스화할 수 있다.

## IDE가 static을 붙이라고 하는 이유
---
- 외부 참조가 유지된다 = 메모리에 대한 참조가 유지됨.   
  => GC가 메모리를 회수할 수 없다 = 메모리 누수
- 항상 외부 인스턴스의 참조를 통해야 하므로 성능 상 비효율적이다.

```
외부 인스턴스에 대한 참조가 필요하지 않다면 static nested class로 만드는 것이 낫다.
```

## 출처
---
[Java Tutorials](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)

[왜 Inner Class에 Static을 붙이는거지?](https://velog.io/@agugu95/%EC%99%9C-Inner-class%EC%97%90-Static%EC%9D%84-%EB%B6%99%EC%9D%B4%EB%8A%94%EA%B1%B0%EC%A7%80)

