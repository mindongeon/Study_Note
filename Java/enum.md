Java의 정석

# 1. 목차

- [1. 목차](#1-목차)
- [2. 열거형 ( enum )](#2-열거형--enum-)
  - [2.1. 일반적인 상수 선언 방법](#21-일반적인-상수-선언-방법)
  - [2.2. 열거형(enum)을 사용한 상수 선언](#22-열거형enum을-사용한-상수-선언)
- [3. 열거형의 정의와 사용](#3-열거형의-정의와-사용)
  - [3.1. 정의](#31-정의)
  - [3.2. 사용](#32-사용)
- [4. 열거형의 조상 - java.lang.Enum](#4-열거형의-조상---javalangenum)
- [5. 열거형에 멤버 추가하기](#5-열거형에-멤버-추가하기)



# 2. 열거형 ( enum )


```
💡 여러 상수를 선언해야 할 때, 편리하게 선언할 수 있는 방법.
```

## 2.1. 일반적인 상수 선언 방법

---

```java
class Card {
    static final int CLOVER = 0;
    static final int HEART = 1;
    static final int DIAMOND = 2;
    static final int SPADE = 3;
    
    static final int TWO = 0;
    static final int THREE = 1;
    static final int FOUR = 2;
    
    final int kind;
    final int num;
}
```

## 2.2. 열거형(enum)을 사용한 상수 선언

---

```java
	class Card{
    //           0,      1,       2,     3
    enum Kind {CLOVER, HEART, DIAMOND, SPADE}
    enum Value {TWO, THREE, FOUR}
    
    final Kind kind;
    final Value value;
}
```

위와 달리 따로 값을 지정해주지 않아도 자동적으로 0부터 시작하는 정수값이 할당된다.

열거형을 이용해서 상수를 정의한 경우는 값을 비교하기 전에 타입을 먼저 비교하므로 값이 같더라도 타입이 다르면 컴파일 에러가 발생한다.

# 3. 열거형의 정의와 사용


## 3.1. 정의

---

```java
enum 열거형이름 { 상수명1, 상수명2, ... }
// ex //
enum Direction { EAST, SOUTH, WEST, NORTH }
```

## 3.2. 사용

---

‘`열거형이름.상수명`’

```java
class Unit {
	int x, y;      // 유닛의 위치
	Direction dir; // 열거형 인스턴스 변수 선언

	void init() {
		dir = Direction.EAST; // 유닛의 방향을 EAST로 초기화
	}
}
```

열거형 상수간의 비교에는 `==` 을 사용할 수 있다. 
`equals()`가 아닌 `==`로 비교가 가능하다는 뜻은 빠른 성능을 제공한다는 뜻이다.

`<`,`>`과 같은 비교연산자는 사용할 수 없고, `compareTo()`를 사용한다.
```
💡 `CompareTo()` 는 두 비교대상이 같으면 0, 왼쪽이 크면 양수, 오른쪽이 크면 음수를 리턴한다.
```

# 4. 열거형의 조상 - java.lang.Enum


모든 열거형의 조상은 `java.lang.Enum`이다.

| Method | Description |
| --- | --- |
| Class<E\> getDeclaringClass() | 열거형의 Class객체를 반환한다. |
| String name() | 열거형 상수의 이름을 문자열로 반환한다. |
| int ordinal() | 열거형 상수가 정의된 순서를 반환한다. ( 0부터 시작 ) |
| T valueOf(Class<T\> enumType, String name) | 지정된 열거형에서 name과 일치하는 열거형 상수를 반환한다. |

`ordinal()`은 모든 열거형의 조상인 `java.lang.Enum`클래스에 정의된 것으로, 정의된 순서를 정수로 반환한다.

`values()`는 열거형에 정의된 모든 상수를 출력하는데 사용된다.

```java
Direction[] dArr = Direction.values()
for(Direction d : dArr)
	System.out.println(d.name() + " " + d.ordinal());
```

`valueOf(String name)`는 열거형 상수의 이름으로 문자열 상수에 대한 참조를 얻을 수 있게 해준다.

```java
Direction d = Direction.valueOf("WEST");

System.out.println(d); // WEST
System.out.println(Direction.WEST==Direction.valueOf("WEST")) // true
```

# 5. 열거형에 멤버 추가하기


`java.lang.Enum`클래스에 정의된 `ordianl()`이 열거형 상수가 정의된 순서로 반환하지만, 내부적인 용도로만 사용하는게 좋다.

열거형 상수의 값이 불규칙적인 경우, 열거형 상수의 이름 옆에 원하는 값을 괄호와 함께 적어주면 된다.

```java
enum Direction { EAST(1), SOUTH(5), WEST(-1), NORTH(10) }
```

그리고 지정된 값을 저장할 수 있는 인스턴스 변수와 생성자를 새로 추가해 주어야 한다.
**먼저 열거형 상수를 모두 정의한 다음에 다른 멤버들을 추가**해야 한다.
열거형 상수의 마지막은 ‘`;`’를 명시한다.

```java
enum Direction {
    EAST(1),SOUTH(5),WEST(-1),NORTH(10); // 끝에 ';' 추가
    
    private final int value; // 정수를 저장할 필드(인스턴스 변수) 추가
    Direction(int value) { this.value = value; } // 생성자 추가
    
    public int getValue() {return value;}
}
```

열거형의 인스턴스 변수가 항상 final이어야 한다는 제약은 없지만, `value`는 열거형 상수의 값을 저장하기 위한 것이므로 final을 붙였다.
그리고 외부에서 해당 값을 얻을 수 있도록 `getValue()`를 추가하였다.

```java
Direction d = new Direction(1); // Error. 열거형의 생성자는 외부에서 호출이 불가하다.
```

열거형의 생성자는 묵시적으로 `private`이다.