# 1. 목차
- [1. 목차](#1-목차)
- [2. Reflection](#2-reflection)
  - [2.1. Java Reflection 정의](#21-java-reflection-정의)
  - [2.2. `Java.lang.Class` 가져오는 3가지](#22-javalangclass-가져오는-3가지)
  - [2.3. Reflection 이용](#23-reflection-이용)
    - [2.3.1. 예시 객체](#231-예시-객체)
    - [2.3.2. 인스턴스 생성](#232-인스턴스-생성)
    - [2.3.3. 메소드 실행](#233-메소드-실행)
  - [2.4. 요약](#24-요약)
  - [2.5. 선생님](#25-선생님)


# 2. Reflection

## 2.1. Java Reflection 정의

---

- Runtime에 코드를 변경할 수 있도록 Java에서 제공해줌
- Runtime에 객체를 동적으로 생성 가능

> **런타임에 클래스의 동작을 검사, 조작할 때 사용되는 프로세스**
> 

## 2.2. `Java.lang.Class` 가져오는 3가지

---

- **.class : 인스턴스가 없을 경우**
    
    ```java
    public class Human{}
    
    public static void main(String[] args) {
    	Class<Human> hClass = Human.class;
    }
    ```
    
- **Object.getClass() : 인스턴스가 존재할 경우**
    
    ```java
    public class Human{}
    
    public static void main(String[] args) {
    	Human human = new Human();
    	human.getClass();
    	Class<? extends Human> hClass = Human.getClass();
    }
    ```
    
- **Class.forName() : 패키지명이 포함된 클래스명을 통해서 가져오기**
    
    ```java
    public class Human{}
    
    public static void main(String[] args) {
    	Class<?> hClass = Class.forName("com.ex.Human");
    }
    ```
    
    class를 찾지 못하면 ClassNotFoundException 발생
    
    ⇒ 예외처리 강제
    

## 2.3. Reflection 이용

---

### 2.3.1. 예시 객체

---

```java
public class Human {
    private String name;
    private int age;

    public Human() {}

    public Human(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

### 2.3.2. 인스턴스 생성

---

```java
//NoArgConstructor
public static void main(String[] args) throws Exception {
        Class<Human> hClass = Human.class;
        Constructor<Human> hConst = hClass.getConstructor();
        Human human = hConst.newInstance();
    }
```

```java
//AllArgsConstructor
public static void main(String[] args) throws Exception {
        Class<Human> hClass = Human.class;
        Constructor<Human> hConst = hClass.getConstructor(String.class, int.class);
        Human human = hConst.newInstance("abc", 123);
        System.out.println(human); //Human{name='abc', age=123}
    }
```

### 2.3.3. 메소드 실행

---

```java
**Method sum = hClass.getMethod("sum",int.class,int.class);
int rslt1 = (int) sum.invoke(human,3,5); // instance 지정해야함
System.out.println(rslt1);**
```

```java
//static method
Method sum2 = hClass.getMethod("stSum", int.class,int.class);
int rslt2 = (int) sum2.invoke(null, 3,5); //instance 지정안해도됨
System.out.println(rslt2);
```

## 2.4. 요약

---

- Reflection을 통해 Class 객체를 이용, 해당 인스턴스를 생성, 메소드 실행 가능
- 생성자를 얻어올 시, 매개변수가 있다면 매개변수의 타입을 지정
- static 메소드가 아닌 경우 `invoke()`의 첫번째 인자는 인스턴스
- 생성자나 메소드를 찾지 못하면 NoSuchMethodException 발생

## 2.5. 선생님

---

[https://velog.io/@dev_leewoooo/Reflection이란](https://velog.io/@dev_leewoooo/Reflection%EC%9D%B4%EB%9E%80)