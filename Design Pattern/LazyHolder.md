# LazyHolder

## 개요
---
Java에서의 생성자는 접근 제어로 통제할 수 있다. 객체 생성에 생성자를 쓰고 싶지 않다면 private를 사용하면 된다.

생성자에 private를 사용하면 상속을 방어하는 효과도 있다.

## LazyHolder 기법
---
`Android의 Context`같은 경우 [Singleton](/Design%20Pattern/Singleton%20Pattern.md)의 초기화 과정에서 Context라는 의존성이 끼어들 수 있다.

`LazyHolder`는 그에 대한 대안으로 나온 방법이다. JVM에게 객체의 초기화를 떠넘기는 방식으로, 멀티쓰레드 환경에서도 객체의 단일성을 보장할 수 있다.

### 예시
```java
public class OnlyOne {  
      
    private OnlyOne() {}  
  
    public static OnlyOne getInstance() {  
        return LazyHolder.IT;  
    }  
  
    private static class LazyHolder {  
        private static final OnlyOne IT = new OnlyOne();    
    }  
}
```

객체 생성을 담당할 내부클래스를 하나 정의하는데, 이것이 `LazyHolder`이다.
OnlyOne클래스는 초기에는 아무런 상태가 없기에 LazyHolder클래스를 초기화하지 않지만, `getInstance`메소드가 호출될 때 LazyHolder가 로딩되며 초기화가 진행된다.

[클래스의 내부 클래스는 외부의 클래스가 초기화될 때 초기화되지 않고, 클래스의 static변수는 클래스를 로딩할 때 초기화되는 것](/Java/Class%EC%9D%98%20%EB%A1%9C%EB%94%A9%EA%B3%BC%20%EC%B4%88%EA%B8%B0%ED%99%94.md)을 이용한 기법이다.

Class를 로딩하고 초기화하는 것은 JVM의 영역이고 `Thread Safe`를 보장한다.

## 출처
---
[LazyHolder](https://blog.javarouka.me/2018/11/20/no-instance/)