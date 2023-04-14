# 정의

객체의 인스턴스가 오직 1개만 생성되는 패턴

# 사용하는 이유

```
크게 메모리와 데이터 공유에 대한 이점이 있다.
메모리의 경우 한번의 new로 계속 사용가능한 점.
데이터 공유의 경우 전역 인스턴스인 점.
```
인스턴스를 오직 한 개로만 가져가면 **메모리**와 **데이터 공유**에 대한 부분의 이점이 있다.

메모리는 최초 한번의 new 연산자를 통해 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리의 낭비를 방지할 수 있다. 또한 이미 생성된 인스턴스를 활용하기 때문에 속도 측면에서도 이점이 있다.

데이터 공유는 인스턴스가 전역으로 사용되는 인스턴스이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있다. 하지만 여러 클래스의 인스턴스에서 싱글톤 인스턴스의 데이터에 동시에 접근하게 되면 동시성 문제가 발생할 수 있으니 주의해서 설계해야 한다.

이 외의 도메인 관점에서 인스턴스가 한 개만 존재하는 것을 보증하고 싶은 경우 싱글톤 패턴을 사용하기도 한다.

# 문제점

```
1. 코드가 길어진다.
2. 테스트가 어려워진다.
3. SOLID원칙 중 DIP를 위반한다.
```

싱글톤 패턴을 구현하는 코드 자체가 많이 필요하다. [정적 팩토리 메소드](/Design%20Pattern/Singleton%20Pattern.md)에서 객체 생성을 확인하고 생성자를 호출하는 경우 멀티쓰레딩 환경에서 발생할 수 있는 동시성 문제 해결을 위해 `syncronized`를 사용해야 한다.

테스트하기 어렵다는 점도 있다. 싱글톤 인스턴스는 자원을 공유하기 때문에 테스트가 결정적으로 격리된 환경에서 수행되려면 매번 인스턴스의 상태를 초기화시켜주어야 한다. 그렇지 않으면 어플리케이션 전역에서 상태를 공유하기 때문에 테스트가 온전하게 수행되지 못한다.

의존 관계상 클라이언트가 구체 클래스에 의존하게 된다. new 키워드를 직접 사용하여 클래스 안에서 객체를 생성하고 있으므로, 이는 [SOLID원칙](/Java/%EA%B0%9D%EC%B2%B4%EC%99%80%20%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5.md) 중 DIP를 위반하게 되고 OCP원칙 또한 위반할 가능성이 높다.

이 외에 자식클래스 생성 불가, 내부 상태 수정이 어렵다는 점 등 여러가지 문제점이 있다.   
=> 싱글톤 패턴은 유연성이 많이 떨어지는 패턴이다.

# 만드는 방법.

## 기존
---
### 1. public static final field
```java
public class Singleton {
    public static final Singleton INSTANCE = new Singleton();
    private Singleton(){};
}
```
### 2. public static factory method
```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton(){};
    public static Singleton getInstance(){
        return INSTANCE;
    }
}
```

### 3. lazy itialization
```java
public class Singleton {
    private static Singleton INSTANCE = null;
    private Singleton(){};
    public static Singleton getInstance(){
        if(INSTANCE == null){
            synchronized (Singleton.class){
                if(INSTANCE == null)
                    INSTANCE = new Singleton();
            }
        }
        return INSTANCE;
    }
}
```
### 기존 방법의 장단점
---
- 코드 블록에서 하는 일이 없어도 private생성자를 만들어야 한다. (기본 생성자가 생성되는 것을 피하기위해) 
- 2,3번 방법은 api를 변경하지 않고 클래스를 비싱글톤으로 만들 수 있다는 장점이 있다.
```java
public static Singleton getInstance() {
    return new Singleton ();
}
```
- static 필드는 클래스 로딩 시간에 초기화되므로 1,2번 방법은 런타임에 해당 클래스를 사용하지 않아도 싱글톤 인스턴스가 생성된다. (자원 낭비)
- 3번 방법은 이런 자원 낭비를 막고, 싱글톤 객체에 처음 액세스할 때 인스턴스가 생성된다. 또한 `synchronized` 예약어를 통해 멀티 쓰레드 환경에서 객체가 두 개 이상 생성되지 않도록 막는다.

### 추가적인 문제 - 1. 직렬화와 역직렬화 (Serialization & Deserialization)
- 클래스를 역직렬화할 때 새로운 인스턴스가 생성되어 싱글톤 속성을 위반한다.
#### 해결방법
- 싱글톤 클래스에 readResolve 메소드를 구현한다.

### 추가적인 문제 - 2. 리플렉션 (Reflection)
- 리플렉션을 이용하면 런타임에 private생성자에 접근하여 새로운 인스턴스를 생성할 수 있다.

## [enum](/Java/enum.md)을 통해 싱글톤 만들기
```java
public enum Singleton {
    INSTANCE;
}
```
- enum 타입은 기본적으로 직렬화 가능하므로 Serializable 인터페이스를 구현할 필요가 없고, [리플렉션](/Java/Reflection.md) 문제도 발생하지 않는다.
- 인스턴스가 JVM 내에 하나만 존재한다는 것이 100% 보장되므로, Java에서 싱글톤을 만드는 가장 좋은 방법으로 권장된다.

### 사용법
```java
public enum SingletonEnum {
    INSTANCE;
    int value;
    
    public int getValue() {
        return value;
    }
    public void setValue(int value) {
        this.value = value;
    }
}
public class EnumDemo {
    
    public static void main(String[] args) {
        SingletonEnum singleton = SingletonEnum.INSTANCE;
        
        System.out.println(singleton.getValue());
        singleton.setValue(2);
        System.out.println(singleton.getValue());
    }
}
```

### 주의 사항
열거형을 직렬화할 때 필드 변수는 소실된다. 즉, 위 코드에서 value변수는 직렬화되지 않고, 소실된다.

# 결론
```
프레임워크의 도움을 받아 사용하는게 편하지만
없이 사용하려면 장단점을 잘 고려해야 한다.
```
오직 한개의 인스턴스 생성을 보증하여 효율을 찾을 수 있찌만 그에 못지않게 수반되는 문제점도 많다. 싱글톤 패턴은 안티패턴이라고 불릴 만큼 단독으로 사용한다면 객체 지향에 위배되는 사례가 많다. 스프링 컨테이너 같은 프레임워크의 도움을 받으면 싱글톤 패턴의 문제점을 보완하며 장점의 혜택을 누릴 수 있다.

프레임워크의 도움없이 싱글톤 패턴을 적용하고 싶다면, 장단점의 trade-off를 잘 고려하여 사용해야 한다.

# 출처

[싱글톤 패턴이란?](https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/)
[Enum과 싱글톤](https://scshim.tistory.com/361)