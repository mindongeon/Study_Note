# Reflection 관련 클래스들

자바 API에는 reflection이라는 패키지가 있다. 이 패키지에 있는 클래스들을 사용해 JVM에 로딩되어 있는 클래스와 메소드 정보를 읽어올 수 있다.

## [Class 클래스](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Class.html)
---
Class 클래스는 클래스에 대한 정보를 얻을 수 있다. Class 클래스는 생성자가 따로 없다. ClassLoader 클래스의 `defineClass()` 메소드를 이용해 클래스 객체를 만들 수 있지만, 좋은 방법은 아니다. Object 클래스에 있는 `getClass()` 메소드를 이용하는 것이 일반적이다.

## [Method 클래스](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/reflect/Method.html)
---
Method 클래스를 이용하여 메소드에 대한 정보를 얻을 수 있다. Method 클래스에는 생성자가 없어 Method 클래스의 정보를 얻기 위해서는 Class 클래스의 `getMethod()`메소드를 사용하거나 `getDeclaredMethod()`메소드를 사용해야 한다.

## [Filed 클래스](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/reflect/Field.html)
---
Field 클래스는 클래스에 있는 변수들의 정보를 제공하기 위해서 사용한다. Method 클래스와 마찬가지로 생성자가 존재하지 않아 Class 클래스의 `getField()`메소드나 `getDeclaredFileds()`메소드를 사용해야 한다.

## 출처
---
[클래스 정보와 reflection](https://12bme.tistory.com/129)