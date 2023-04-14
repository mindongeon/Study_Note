# Class의 로딩과 초기화

## 클래스 로딩
---
### 정의
ClassLoader가 .class파일을 찾고 JVM의 메모리에 올려놓는 것을 의미한다.

### 왜 올려 놓는가
JVM는 실행될 때 모든 클래스를 메모리에 올려놓지 않는다. 필요할 때마다 클래스를 메모리에 올려 효율적으로 관리하기 위해서이다.

## 언제 클래스를 로딩할까.
---
### 가이드
- 클래스의 인스턴스가 생성될 때
- 클래스의 정적 변수가 사용될 때 (final로 선언된 상수는 제외)
- 클래스의 정적 메소드가 호출될 때

### -verbose:class
`-verbose:class` 옵션을 사용하면 클래스 로딩을 디버그할 수 있다.

### 확인 방법
- 테스트 케이스 하나씩 호출
- javac 컴파일
- `-verbose:class`옵션으로 JVM 실행
- 클래스가 로드되는지 확인

## 클래스 로딩 정리
---
### 클래스 로딩 시점
- 클래스의 인스턴스 생성
- 클래스의 정적 변수 사용 (final로 선언된 상수는 제외)
- 클래스의 정적 메소드 호출

### 내부 클래스 로드 여부
외부 클래스가 로딩 - 내부의 클래스 로딩 X
내부 클래스만 로딩 - 외부의 클래스 로딩 X

## 초기화 
---
### 정의
```
클래스 초기화는 static 블럭과 static 멤버 변수의 값을 할당하는 것
```

내부의 클래스는 초기화 대상이 아니다.

### 언제 초기화 될까.
- 클래스의 인스턴스 생성
- 클래스의 정적 메소드 호출
- 클래스의 정적 변수 할당
- 클래스의 정적 변수 사용 (final로 선언된 상수 제외)
```
클래스 로드 시점과 같다. 클래스가 로드되면 초기화도 바로 진행된다.
```

### 초기화 진행 순서
1. 정적 블럭
2. 정적 변수
3. 생성자

### 오직 한번만 클래스가 로딩됨을 보장.
```
멀티 쓰레드 환경에서 쓰레드 세이프하다.
```
JVM에 클래스가 로딩되고 초기화될 때는 순차적으로 동작함을 보장한다. 멀티 쓰레드 환경에서 여러 개의 쓰레드가 클래스를 동시에 로딩하려고 오직 한개의 클래스만 로딩된다.

## [싱글톤](/Design%20Pattern/Singleton%20Pattern.md)
---
```
클래스 로딩 및 초기화 과정이 쓰레드 세이프함을 이용해 싱글톤 인스턴스를 만들 수 있다.
```

### 멀티 쓰레드 환경
멀티 쓰레드 환경에서는 여러 쓰레드가 동시에 인스턴스 생성을 시도할 수 있고, 싱글톤 인스턴스가 여러 개 생성될 수 있다.

### 클래스 로딩 및 초기화가 딱 한번만 수행됨을 활용
클래스 로딩 및 초기화는 여러개의 쓰레드가 동시에 시도해도 단 한번만 수행된다. 이 때 수행되는 초기화 과정에서 클래스의 인스턴스를 딱 한번만 생성되도록 한다. 

[LazyHolder](/Design%20Pattern/LazyHolder.md)라는 방법으로 싱글톤 인스턴스를 생성하면,
```java
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class Main {
    public static void main(String args[]) {
        // 1. 스레드 풀 생성
        ExecutorService service = Executors.newCachedThreadPool();

        // 2. 반복문을 통해 - 10개의 스레드가 동시에 인스턴스 생성
        for (int i = 0; i < 10; i++) {
            service.submit(() -> {
                SingleTon.getInstance();
            });
        }
        // 3. 종료
        service.shutdown();
    }
}

// 싱글톤 클래스
class SingleTon {
    // private 생성자 new 를 통한 인스턴스 생성 방지
    private SingleTon() {
        System.out.println("싱글톤 인스턴스 생성");
    }

    // 정적 메서드를 통해 내부 클래스 로딩
    public static SingleTon getInstance() {
        return LazyHolder.INSTANCE;
    }

    // 내부 클래스가 로딩될때 초기화 수행 - 싱글톤 인스턴스 생성
    private static class LazyHolder {
        private static final SingleTon INSTANCE = new SingleTon();
    }
}
```

## 총 정리

### 클래스 로딩 시점

-   클래스의 인스턴스 생성
-   클래스의 정적 변수 사용 (단, 정적 변수는 final로 선언된 상수 x)
-   클래스의 정적 메소드 호출

### 외부, 내부 클래스 로딩

외부 클래스 로딩 - 내부 클래스 로딩 X  
내부 클래스 로딩 - 외부 클래스 로딩 X

### 초기화 의미

1.  static 블록 수행
2.  static 변수 메모리 할당

### 클래스 로딩 및 초기화

클래스가 로딩될때 초기화도 수행

클리스 로딩 및 초기화 과정은 스레드 세이프함, 여러 스레드가 동시에 시도해도, 오직 한번만 수행

### 싱글톤 - LazyHolder

클래스 로딩 및 초기화 과정이 스레드 세이프함을 이용하는 방법

장점
-  스레드 세이프 (오직 1개의 싱글톤 인스턴스 생성)
-  필요할때 인스턴스 생성(getInstance 메서드 호출 시점)
-  모든 자바 버전에서 사용가능

## 출처
---
[클래스는 언제 로딩되고 초기화되는가? (feat. Singleton)](https://velog.io/@skyepodium/%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-%EC%96%B8%EC%A0%9C-%EB%A1%9C%EB%94%A9%EB%90%98%EA%B3%A0-%EC%B4%88%EA%B8%B0%ED%99%94%EB%90%98%EB%8A%94%EA%B0%80)