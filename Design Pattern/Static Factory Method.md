# 정의

```
객체 생성의 역할을 하는 클래스 메소드
```
## 예시 1
---
직접적으로 생성자를 통해 객체를 생성하는 것이 아닌 
아래의 `of`메소드처럼 메소드를 통해서 객체를 생성하는 것을 **정적 팩토리 메소드**라고 한다.
```java
// LocalTime.class
...
public static LocalTime of(int hour, int minute) {
  ChronoField.HOUR_OF_DAY.checkValidValue((long)hour);
  if (minute == 0) {
    return HOURS[hour];
  } else {
    ChronoField.MINUTE_OF_HOUR.checkValidValue((long)minute);
    return new LocalTime(hour, minute, 0, 0);
  }
}
...

// hour, minutes을 인자로 받아서 9시 30분을 의미하는 LocalTime 객체를 반환한다.
LocalTime openTime = LocalTime.of(9, 30);
```


## 예시 2
---
enum의 요소를 조회할 때 사용하는 `valueOf`도 정적 팩토리 메소드의 일종이라고 할 수 있다. 미리 생성된 객체를 "조회"하는 메소드이기 때문에 팩토리의 역할을 한다고 볼수는 없지만, 외부에서 원하는 객체를 반환해주고 있으므로 정적 팩토리 메소드로 간주해도 좋다.

```java
public enum Color {
  RED,
  BLUE;
}
...
Color redColor = Color.valueOf("RED");
Color blueColor = Color.valueOf("BLUE");
```

# 생성자와의 차이점
## 이름을 가질 수 있다.
---
```
메소드의 이름을 지정함으로써 동작 방식을 파악할 수 있다.
=> 가독성이 좋아진다.
```
객체는 생성 목적과 과정에 따라 생성자를 구별해서 사용할 필요가 있다. `new`라는 키워드를 통해 객체를 생성하는 생성자는 내부 구조를 잘 알고 있어야 목적에 맞게 객체를 생성할 수 있다.     
하지만 정적 팩토리 메소드를 사용하면 메소드 이름에 객체의 생성 목적을 명시할 수 있다.

### 예시
---
```java
public class LottoFactory() {
  private static final int LOTTO_SIZE = 6;

  private static List<LottoNumber> allLottoNumbers = ...; // 1~45까지의 로또 넘버

  public static Lotto createAutoLotto() {
    Collections.shuffle(allLottoNumbers);
    return new Lotto(allLottoNumbers.stream()
            .limit(LOTTO_SIZE)
            .collect(Collectors.toList()));
  }

  public static Lotto createManualLotto(List<LottoNumber> lottoNumbers) {
    return new Lotto(lottoNumbers);
  }
  ...
}
```
`createAutoLotto`와 `createMenualLotto`처럼 메소드의 이름으로 구조와 동작 방식을 파악할 수 있다.

## 호출할 때마다 새로운 객체를 생성할 필요가 없다.
---
```
생성자를 private로 설정해 객체 생성을 정적 팩토리 메소드로만 가능하게 한다.
```
enum같은 자주 사용되는 요소이 개수가 정해져있다면 해당 개수만큼 미리 생성해놓고 조회(캐싱)할 수 있는 구조로 만들 수 있다.
정적 팩토리 메소드와 캐싱구조를 함께 사용하면 매번 새로운 객체를 생성할 필요가 없어진다.

### 예시
---
```java
public class LottoNumber {
  private static final int MIN_LOTTO_NUMBER = 1;
  private static final int MAX_LOTTO_NUMBER = 45;

  private static Map<Integer, LottoNumber> lottoNumberCache = new HashMap<>();

  static {
    IntStream.range(MIN_LOTTO_NUMBER, MAX_LOTTO_NUMBER)
                .forEach(i -> lottoNumberCache.put(i, new LottoNumber(i)));
  }

  private int number;

  private LottoNumber(int number) {
    this.number = number;
  }

  public LottoNumber of(int number) {  // LottoNumber를 반환하는 정적 팩토리 메서드
    return lottoNumberCache.get(number);
  }

  ...
}
```
미리 생성된 로또 번호 객체의 캐싱을 통해서 새로운 객체 생성의 부담을 덜 수 있다는 장점도 있지만, 생성자의 접근 제한자를 `private`로 설정함으로써 객체 생성을 정적 팩토리 메소드로만 가능하도록 제한할 수 있다.
이를 통해 정해진 범위를 벗어나는 로또 번호의 생성을 막을 수 있다는 장점을 확보할 수 있다.

## 하위 자료형 객체를 반환할 수 있다.
---
생성자의 역할을 하는 정적 팩토리 메소드가 반환값을 가지고 있기 때문에 가능하다.

### 예시 
---

> `Basic`, `Intermediate`, `Advanced`는 `Level`이라는 클래스를 상속 받고 있다.

```java
public class Level {
  ...
  public static Level of(int score) {
    if (score < 50) {
      return new Basic();
    } else if (score < 80) {
      return new Intermediate();
    } else {
      return new Advanced();
    }
  }
  ...
}
```
시험 점수에 따라 결정되는 하위 등급 타입을 반환하는 정적 팩토리 메소드를 만들면, 분기처리를 통해 하위 타입의 객체를 반환할 수 있다.

## 객체 생성을 [캡슐화](/Java/%EC%BA%A1%EC%8A%90%ED%99%94%2C%20%EC%83%81%EC%86%8D%2C%20%EB%8B%A4%ED%98%95%EC%84%B1%2C%20%EC%B6%94%EC%83%81%ED%99%94.md) 할 수 있다.
---
정적 팩토리 메소드는 객체 생성을 캡슐화하는 방법이기도 하다.