# 데이터들 간의 연관관계 표현
매일 배치를 돌며 하나의 테이블(origin)에 있는 내용을 2개의 테이블(table1, table2)에 등록하는 기능이 있다.

origin 테이블의 값은 "Y", "N"이고,   
table1 은 "1" / "0",   
table2 는 true / false 형태이다.

```java
public class Ex1 {
    public String toTable1Value(String originValue) {
        if("Y".equals(originValue)) {
            return "1";
        } else {
            return "0";
        }
    }

    public boolean toTable2Value(String originValue) {
        if("Y".equals(originValue)) {
            return true;
        } else {
            return false;
        }
    }
}
```

기능상의 문제는 없지만, 몇가지 문제가 있다.

- "Y", "1", true는 **모두 같은 의미**라는 것을 알 수 없다.
  - Y란 값은 "1"이 될 수도 있고, true가 될 수 있다는 것을 확인하려면 항상 위에서 선언된 클래스와 메소드를 찾아야만 합니다.
- 불필요한 코드량이 많다.
  - Y, N 외에 R, S 등의 추가 값이 필요한 경우 **if문을 포함한 메소드 단위**로 코드가 증가한다.
  - 동일한 타입의 값이 추가되는 것에 비해 너무 많은 **반복성 코드가 발생**하게 된다.

공통 부분을 Enum으로 추출하면
```java
public enum TableStatus {
    Y("1", true),
    N("0", false);

    private String table1Value;
    private boolean table2Value;

    TableStatus(String table1Value, boolean table2Value) {
        this.table1Value = table1Value;
        this.table2Value = table2Value;
    }

    public String getTable1Value() {
        return table1Value;
    }

    public boolean isTable2Value() {
        return table2Value;
    }
}
```

"Y", "1", true가 한 묶음으로, "N", "0", false가 한 묶음이 된 것을 확인할 수 있다. 또한 추가 타입이 필요한 경우에도 Enum 상수와 get메소드만 추가하면 된다. (만약 lombokdml `@Getter`를 사용한다면 더욱 깔끔해진다.)

사용하는 곳에서 역시 깔끔하게 표현이 가능하다.
```java
@Test
public void origin테이블에서_조회한_데이터를_다른_2테이블에_등록한다() throws Exception {
    TableStatus origin = selectFromOriginTable();

    String table1Value = origin.getTable1Value();
    boolean table2Value = origin.isTable2Value();
}
```

TableStatus라는 Enum 타입을 전달받기만 하면, table1, table2의 값을 바로 얻을 수 있다.

# 상태와 행위를 한곳에서 관리
서로 다른 계산식을 적용해야할 때가 있다.   
예를 들어 DB에 저장된 code의 값이 "CALC_A"일 경우엔 값 그대로, "CALC_B"인 경우 *10 한 값, "CALC_C"인 경우 *3 한 값을 전달한다.

static 메소드를 작성해 필요한 곳에서 호출하는 방법이 있다.
```java
public class Calc {
    public static Long calculate(String code, long originValue) {
        
        if("CAL_A".equals(code)) {
            return originValue;
        } else if("CAL_B".equals(code)) {
            return originValue * 10;
        } else if("CAL_C".equals(code)) {
            return originValue * 3;
        } else {
            return 0;
        }
    }
}
```
이렇게 메소드로 분리하고 실제 사용시, **코드는 코드대로 조회**하고 **계산은 별도의 클래스와 메소드를 통해** 진행해야 한다.

```java
@Test
public void test2() throws Exception {
    // 코드는 코드대로 조회
    String code = selectCode();

    long originValue = 10000L;

    // 계산은 별도의 메소드를 통해 진행
    long result = Calc.calculate(code, originValue);
}
```

Calc의 메소드와 code는 **서로 관계가 있음을 코드로 표현**할 수 없다.

뽑아낸 **코드에 따라 지정된 메소드에서만** 계산되길 원하지만, 현재 상태로는 **강제할 수 있는 수단**이 없다.

- 똑같은 기능을 하는 메소드를 중복 생성할 수 있다.
  - 계산 메소드가 있다는 것을 몰라 중복 생성할 수 있다.
  - 기존 메소드의 로직이 변경될 경우, 2개의 메소드 로직을 전부 변경해야 하는지, 해당 화면만 변경해야하는지 알 수 없다.
  - 관리 포인트가 증가할 확률이 높다.
- 계산 메소드를 누락할 수 있다.
  - 문자열과 메소드로 분리되어 있기 때문에 이 계산 메소드를 써야함을 알 수 없어 새로운 기능 생성시 계산 메소드 호출이 누락될 수 있다.

