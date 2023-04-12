Java의 정석


# 1. 목차

- [1. 목차](#1-목차)
- [2. Annotation ?](#2-annotation-)
- [3. 표준 Annotation](#3-표준-annotation)
  - [3.1. Meta Annotation](#31-meta-annotation)
    - [3.1.1. @Target](#311-target)
    - [3.1.2. @Retention](#312-retention)
    - [3.1.3. @Repeatable](#313-repeatable)
- [4. Annotation 타입 정의하기](#4-annotation-타입-정의하기)
- [5. Annotation의 요소](#5-annotation의-요소)
- [6. 모든 Annotation의 조상](#6-모든-annotation의-조상)
- [7. 마커 Annotataion](#7-마커-annotataion)
- [8. Annotation 요소와 규칙](#8-annotation-요소와-규칙)



# 2. Annotation ?

---

```
💡 소스코드 안에 다른 프로그램을 위한 정보를 미리 약속된 형식으로 포함시킨 것
```

자바를 개발한 사람들은 소스코드에 대한 문서를 따로 만들기보다 소스코드와 문서를 하나의 파일로 관리하는 것이 낫다고 생각했다. 그래서 소스코드의 주석에 소스코드에 대한 정보를 저장하고, 소스코드의 주석으로부터 HTML문서를 생성해내는 프로그램( javadoc.exe )을 만들어서 사용했다.

# 3. 표준 Annotation

---

자바에서 기본적으로 제공하는 Annotation은 몇 개 없다.
일부 ‘meta annotation’으로 Annotation을 정의하는데 사용되는 Annotation이다.

| Annotation | Description |
| --- | --- |
| @Overide | 컴파일러에게 메소드를 오버라이딩하는 것이라고 말한다. |
| @Deprecated | 앞으로 사용하지 않을 것을 권장하는 대상에 붙인다. |
| @SuppressWarnings | 컴파일러의 특정 경고메시지가 나타나지 않게 해준다. |
| @SafeVarargs | [제네릭스](/Java/Generic.md) 타입의 가변인자에 사용한다. ( JDK 1.7 ) |
| @FunctionalInterface | [함수형 인터페이스](/Java/Functional%20Interface.md)라는 것을 알린다. ( JDK 1.8 ) |
| @Native | native메소드에서 참조되는 상수 앞에 붙인다. |

## 3.1. Meta Annotation

---

| Annotataion | Description |
| --- | --- |
| @Target* | Annotation이 적용가능한 대상을 지정하는데 사용한다. |
| @Documented* | Annotation 정보가 javadoc으로 작성된 문서에 포함되게 한다. |
| @Inherieted* | Annotation이 자손 클래스에 상속되도록 한다. |
| @Retention* | Annotation이 유지되는 범위를 지정하는데 사용한다. |
| @Repeatable* | Annotation을 반복해서 적용할 수 있게 한다. ( JDK 1.8 ) |

### 3.1.1. @Target

---

Annotation이 적용가능한 대상을 지정하는데 사용한다.
`@SuppressWarnings` 를 살펴보면 이 Annotation에 적용할 수 있는 대상을 `@Target`으로 지정하였다.

```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, MODULE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {String[] value();}
```

- `@Target`으로 지정할 수 있는 Annotation 적용대상의 종류
    
    
    | 대상 타입 | 의미 |
    | --- | --- |
    | ANNOTATION_TYPE | Annotation |
    | CONSTRUCTOR | 생성자 |
    | FIELD | 필드 ( 멤버변수, enum 상수 ) |
    | LOCAL_VARABLE | 지역변수 |
    | METHOD | 메소드 |
    | PACKAGE | 패키지 |
    | PARAMETER | 매개변수 |
    | TYPE | 타입 ( Class, Interface, enum ) |
    | TYPE_PARAMETER | 타입 매개변수 ( JDK 1.8 ) |
    | TYPE_USE | 타입이 사용되는 모든 곳 ( JDK 1.8 ) |
    
    `TYPE`은 타입을 선언할 때 Annotation을 붙일 수 있다는 뜻이고
    `TYPE_USE`는 해당 타입의 변수를 선언할 때 붙일 수 있다는 뜻이다.
    
    위 표의 값들은 java.lang.annotation.ElementType이라는 enum에 정의되어 있으며,
    static import문을 쓰면 간단히 사용할 수 있다.
    
    ```java
    import static java.lang.annotation.ElementType.*;
    
    @Target({FIELD, TYPE, TYPE_USE})
    	...
    ```
    

### 3.1.2. @Retention

---

Annotation이 유지되는 기간을 지정하는데 사용된다.

- Annotation의 유지 정책
    
    
    | 유지 정책 | 의미 |
    | --- | --- |
    | SOURCE | 소스 파일에만 존재.
    클래스 파일에는 존재하지 않음. |
    | CLASS | 클래스 파일에 존재.
    실행시에 사용불가.
    기본값. |
    | RUNTIME | 클래스 파일에 존재.
    실행시에 사용가능. |
- SOURCE
    
    `@Override`나 `@SuppressWarnings`처럼 컴파일러가 사용하는 Annotation의 유지 정책
    
- RUNTIME
    
    실행 시에 ‘[reflection](/Java/Reflection.md)’을 통해 클래스 파일에 저장된 Annotation 정보를 읽어서 처리할 수 있다. `@FunctionalInterface`는 컴파일러가 체크해주는 Annotation이지만, 실행 시에도 사용되므로 ‘RUNTIME’으로 되어 있다.
    
- CLASS
    
    컴파일러가 Annotation의 정보를 클래스 파일에 저장할 수 있게는 하지만, 클래스 파일이 JVM에 로딩될 때는 Annotation의 정보가 무시되어 실행 시에 Annotataion에 대한 정보를 얻을 수 없다.
    ⇒ 유지 정책의 기본값임에도 불구하고 잘 사용되지 않는다.
    

### 3.1.3. @Repeatable

---

보통은 하나의 대상에 한 종류의 Annotation을 붙이는데, `@Repeatable`이 붙은 Annotation은 여러 번 붙일 수 있다.

```java
@Repeatable(ToDos.class) // ToDo Annotation을 여러번 반복해서 쓸 수 있게 한다.
@interface ToDo {
    String value();
}
```

```java
@ToDo("hello")
@ToDo("bye")
class MyClass {
	...
}
```

와 같이 여러번 붙이는 것이 가능하다.

일반적인 Annotation과 달리 같은 이름의 Annotation이 하나의 대상에 여러 번 적용될 수 있기 때문에,  이 Annotation들을 하나로 묶어서 다룰 수 있는 Annotation도 추가로 정의해야 한다.

```java
@interface ToDos { // 여러 개의 ToDo Annotation을 담을 컨테이너 Anootation ToDos
    ToDo[] value(); // ToDo Annotation 배열 타입의 요소를 선언. 이름이 반드시 value여야 함.
}
@Repeatable(ToDos.class) // ToDo Annotation을 여러번 반복해서 쓸 수 있게 한다.
@interface ToDo {
    String value();
}
```

# 4. Annotation 타입 정의하기

---

직접 Annotation을 만들어 사용할 수 있다.

```java
@interface 'Annotation name' {
	타입 요소이름(); // Annotation의 요소를 선언한다.
}
```

# 5. Annotation의 요소

---

Annotation 내에 선언된 메소드를 ‘Annotation의 요소’ 라고 한다.

아래의 TestInfo Annotation은 다섯 개의 요소를 갖는다.

```java
@interface TestInfo {
	int count();
	String testedBy();
	String[] testTools();
	TestType testType(); // enum TestType { FIRST, FINAL }
	DateTime testDate(); // 자신이 아닌 다른 Annotataion을 포함할 수 있다.
}

@interface DateTime {
	String yymmdd();
	String hhmmss();
}
```

Annotation의 요소는 반환값이 있고 매개변수는 없는 추상 메소드의 형태를 가지며, 상속을 통해 구현하지 않아도 된다. 다만, Annotation을 적용할 때 이 요소들의 값을 빠짐없이 지정해주어야 한다. 요소의 이름도 같이 적어주므로 순서는 상관없다.

```java
@TestInfo(
	count=3, testedBy="Kim",
	testTools={"JUnit", "AutoTester"},
	testType=TestType.FIRST,
	testDate=@DateTime(yymmdd="160101", hhmmss="235959")
)
public class NewClass { ... }
```

Annotataion의 각 요소는 기본값을 가질 수 있으며, 기본값이 있는 요소는 Annotation을 적용할 때 값을 지정하지 않으면 기본값이 사용된다.

```java
@interface TestInfo {
	int count() default 1;
}

@TestInfo // @TestInfo(count=1) 과 동일
public class NewClass { ... }
```

Annotation 요소가 오직 하나뿐이고 이름이 value인 경우, Annotation을 적용할 때 요소의 이름을 생략하고 값만 적어도 된다.

```java
@interface TestInfo {
	String value();
}

@TestInfo("passed") // @TestInfo(value="passed")와 동일
class NewClass { ... }
```

요소의 타입이 배열인 경우, 괄호 { } 를 사용해서 여러 개의 값을 지정할 수 있다.

```java
@interface TestInfo {
	String[] testTools();
}

@Test(testTools={"JUnit", "AutoTester"})
@Test(testTools="JUnit")
@Test(testTools={})
```

기본값을 지정할 때도 마찬가지로 괄호 { }를 사용할 수 있다.

```java
@interface TestInfo {
	String[] info() default {"aaa", "bbb"}; // 기본값이 여러 개인 경우, { } 사용
	String[] info2() default "ccc"; // 기본값이 하나인 경우. 괄호 생략 가능
}

@TestInfo // @TestInfo(info={"aaa","bbb"}, info2="ccc")와 동일
@TestInfo(info2={}) // @TestInfo(info={"aaa","bbb"},info2={})와 동일
class NewClass { ... }
```

요소의 타입이 배열일 때도 요소의 이름이 value이면, 요소의 이름을 생략할 수 있다.

# 6. 모든 Annotation의 조상


모든 Annotation의 조상은 Annotataion이다.

Annotation은 일반적인 인터페이스로 정의되어 있다.

모든 Annotation 객체는 `equals()`, `hashCode()`, `toString()`과 같은 메소드를 호출하는 것이 가능하다.

# 7. 마커 Annotataion
```
💡 Serializable이나 Coneable인터페이스처럼, 요소가 하나도 정의되지 않은 Annotation
```

값을 지정할 필요가 없는 경우, Annotation의 요소를 하나도 정의하지 않을 수 있다.

# 8. Annotation 요소와 규칙

---

- 요소의 타입은 기본형, String, [enum](/Java/enum.md), Annotation, Class만 허용된다.
- `()` 안에 매개변수를 선언할 수 없다.
- 예외를 선언할 수 없다.
- 요소를 타입 매개변수로 정의할 수 없다.

```java
@interface AnnoTest {
	int id = 100; // 상수 선언. static final int id = 100;
	String major(int i, int j); // Error. 매개변수를 선언할 수 없음
	String minor() throws Exception; // Error. 예외를 선언할 수 없음
	ArrayList<T> list(); // Error. 요소의 타입에 타입 매개변수 사용 불가
}
```

