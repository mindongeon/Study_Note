# Entity Mapping

## 엔티티 매핑 소개

- 객체와 테이블 매핑 : `@Entity`, `@Table`
- 필드와 컬럼 매핑 : `@Column`
- 기본 키 매핑 : `@Id`
- 연관관계 매핑 : `@ManyToOne`, `@JoinColumn`

## 객체와 테이블 매핑

### `@Entity`

- `@Entity`가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
- JPA를 사용해서 테이블과 매핑할 클래스는 `@Entity` 필수
- **주의**
  - **기본 생성자 필수**(파라미터가 없는 public 또는 protected 생성자)
  - final 클래스, enum, interface, inner 클래스 사용 불가
  - 저장할 필드에 final 사용 불가

#### 속성

- 속성 : **name**
  - JPA에서 사용할 엔티티 이름을 지정한다.
  - 기본값 : 클래스 이름을 그대로 사용 (ex. Member)
  - 같은 클래스 이름이 없으면 가급적 기본값을 사용한다.

### `@Table`
- `@Table`은 엔티티와 매핑할 테이블 지정

| 속성                   | 기능                                | 기본값             |
| ---------------------- | ----------------------------------- | ------------------ |
| name                   | 매핑할 테이블 이름                  | 엔티티 이름을 사용 |
| catalog                | DB catalog 매핑                     |                    |
| schema                 | DB schema 매핑                      |                    |
| uniqueConstraints(DDL) | DDL 생성 시에 유니크 제약 조건 생성 |                    |

## DB 스키마 자동 생성
- DDL을 어플리케이션 실행 지점에 자동 생성
- 테이블 중심 -> 객체 중심
- 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성
- 이렇게 **생성된 DDL은 개발 장비에서만 사용**
- 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용

### 속성
`hibernate.hbm2ddl.auto`
| 옵션        | 설명                                         |
| ----------- | -------------------------------------------- |
| create      | 기존테이블 삭제 후 다시 생성 (DROP + CREATE) |
| create-drop | create와 같으나 종료시점에 테이블 DROP       |
| update      | 변경분만 반영 (운영DB에는 사용하면 안됨)     |
| validate    | 엔티티와 테이블이 정상 매핑되었는지만 확인   |
| none        | 사용하지 않음 |

### 주의
```운영 장비에는 절대 create, create-drop, update를 사용하면 안된다.```
- 개발 초기 단계는 create 또는 update
- 테스트 서버는 update 또는 validate
- 스테이징과 운영 서버는 validate 또는 none

### DDL 생성 기능
```DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고 JPA의 실행 로직에는 영향을 주지 않는다.```
- 제약조건 추가 : 회원 이름은 **필수**, 10자 초과X
  - `@Column(nullable=false,length=10)`
- 유니크 제약조건 추가
  - `@Table(uniqueConstraints={@UniqueConstraint(name="NAME_AGE_UNIQUE",columnNames={"NAME","AGE"})})`

## 필드와 컬럼 매핑
### 요구사항 추가
1. 회원은 일반 회원, 관리자로 구분
2. 회원 가입일과 수정일 추가
3. 회원을 설명할 수 있는 필드 추가, 길이 제한 없음

### 매핑 어노테이션 정리
`hibernate.hbm2ddl.auto`
| 어노테이션    | 설명            |
| ------------- | --------------- |
| `@Column`     | 컬럼 매핑       |
| `@Temporal`   | 날짜 타입 매핑  |
| `@Enumerated` | enum 타입 매핑  |
| `@Lob`        | BLOB, CLOB 매핑 |
| `@Transient`  | 특정 필드를 컬럼에 매핑하지 않음 (매핑 무시) |

#### `@Column`
| 속성                  | 설명                                                                                                                                                                                                                                  | 기본값                                |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| name                  | 필드와 매핑할 테이블의 컬럼 이름                                                                                                                                                                                                      | 객체의 필드 이름                      |
| insertable, updatable | 등록, 변경 가능 여부                                                                                                                                                                                                                  | TRUE                                  |
| nullable(DDL)         | null 값의 허용 여부를 설정한다. false로 설정하면 DDL 생성 시에 not null 제약 조건이 붙는다.                                                                                                                                           |                                       |
| unique(DDL)           | `@Table`의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용한다.                                                                                                                                              |                                       |
| columnDefinition(DDL) | 데이터베이스 컬럼 정보를 직접 줄 수 있다.   ex)varchar(100) default 'EMPTY'                                                                                                                                                           | 필드의 자바 타입과 방언 정보를 사용해 |
| length(DDL)           | 문자 길이 제약조건, String 타입에만 사용한다.                                                                                                                                                                                         | 255                                   |
| precision, scale(DDL) | BigDecimal 타입에서 사용한다(BigInteger도 사용할 수 있다). precision은 소수점을 포함한 전체 자릿수를, scale은 소수의 자릿수다. 참고로 double, float 타입에는 적용되지 않는다. 아주 큰 숫자나 정밀한 소수를 다루어야 할 때만 사용한다. | precision=19, scale=2 |


#### `@Enumerated`
```ORDINAL 사용 X```
자바 enum 타입을 매핑할 때 사용
| 속성  | 설명                                                                                   | 기본값 |
| ----- | -------------------------------------------------------------------------------------- | ------ |
| value | - EnumType.ORDINAL : enum 순서를 DB에 저장 <br> - EnumType.STRING : enum 이름을 DB에 저장 | EnumType.ORDINAL |

#### `@Temporal`
날짜 타입 (`java.util.Date`, `java.util.Calendar`)을 매핑할 때 사용

참고 : LocalDate, LocalDateTime을 사용할 때는 생략 가능 (최신 하이버네이트 지원)

| 속성  | 설명 | 기본값 |
| ----- | ---- | ------ |
| value | - **TemporalType.DATE** : 날짜, DB date타입과 매핑 (예: 2013-10-11) <br> - **TemporalType.TIME** : 시간, DB time타입과 매핑 (예: 11:11:11) <br> - **TemporalType.TIMESTAMP** : 날짜와 시간, DB timestamp타입과 매핑 (예: 2013-10-11 11:11:11) |        |

#### `@Lob`
LOB : Large Of Binary, 매우 큰 데이터, SUBSTR같은 함수 사용불가.
DB BLOB, CLOB 타입과 매핑
- `@Lob`에는 지정할 수 있는 속성이 없다.
- 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑
  - CLOB(문자) : String, char[], java.sql.CLOB
  - BLOB(파일) : byte[], java.sql.BLOB

#### `@Transient`
- 필드 매핑X
- DB에 저장X, 조회X
- 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용
```java
@Transient
private Integer temp;
```

## 기본키 매핑
- `@Id`
- `@GeneratedValue`
```java
@Id @GeneratedValue(strategy=GenerationType.AUTO)
private Long id
```

### 방법
- 직접 할당 : `@Id`만 사용
- 자동 생성(`@GeneratedValue`)
  - IDENTITY : DB에 위임, MYSQL
  - SEQUENCE : DB 시퀀스 오브젝트 사용, ORACLE
    - `@SequenceGenerator` 필요
  - TABLE : 키 생성용 테이블 사용, 모든 DB에서 사용
    - `@TableGenerator` 필요
  - AUTO : 방언에 따라 자동 지정, 기본값

#### IDENTITY 전략 - 특징
- 기본 키 생성을 DB에 위임
- 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용   (예: MySQL의 AUTO_INCREMENT)
- JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행
- AUTO_INCREMENT는 DB에 INSERT SQL을 실행한 이후에 ID값을 알 수 있음
- IDENTITY 전략은 `em.persist()` 시점에 즉시 INSERT SQL 실행하고 DB에서 식별자를 조회

#### IDENTITY 전략 - 매핑
```java
@Entity
public class Member {
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;
}
```

#### SEQUENCE 전략 - 특징
- DB 시퀀스는 유일한 값을 순서대로 생성하는 특별한 DB 오브젝트 (예: 오라클 시퀀스)
- 오라클, PostgreSQL, DB2, H2 DB에서만 사용

#### SEQUENCE 전략 - 매핑
```java
@Entity
@SequenceGenerator(
    name = "MEMBER_SEQ_GENERATOR",
    sequenceName = "MEMBER_SEQ", //매핑할 DB 시퀀스 이름
    initalValue = 1, allocationSize = 1
)
public class Member {
    @Id
    @GeneratedValue(
        strategy=GenerationType.SEQUENCE,
        generator = "MEMBER_SEQ_GENERATOR"
    )
    private Long id;
}
```

#### SEQUENCE - `@SequenceGenerator`
- 주의 : allocationSize 기본값 = 50

| 속성            | 설명                                                                                                                                       | 기본값             |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ------------------ |
| name            | 식별자 생성기 이름                                                                                                                         | 필수               |
| sequenceName    | DB에 등록되어 있는 시퀀스 이름                                                                                                             | hibernate_sequence |
| initialValue    | DDL 생성 시에만 사용됨, 시퀀드 DDL을 생성할 때 처음 1 시작하는 수를 지정한다.                                                              | 1                  |
| allocationSize  | 시퀀스 한번 호출에 증가하는 수(성능 최적화에 사용됨 **DB 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다**) | **50**             |
| catalog, schema | DB catalog, schema 이름                                                                                                                    |                    |

#### TABLE 전략
- 키 생성 전용 테이블을 하나 만들어서 DB 시퀀스를 흉내내는 전략
- 장점 : 모든 DB에 적용 가능
- 단점 : 성능

#### TABLE 전략 - 매핑
```SQL
CREATE TABLE MY_SEQUENCES (
    SEQUENCE_NAME VARCHAR(255) NOT NULL,
    NEXT_VAL BIGINT,
    PRIMARY KEY (SEQUENCE_NAME)
)
```
```java
@Entity
@TableGenerator(
    name = "MEMBER_SEQ_GENERATOR",
    table = "MY_SEQUENCES",
    pkColumnValue="MEMBER_SEQ", allocationSize=1
)
public class Member {
    @Id
    @GeneratedValue(
        strategy=GenerationType.TABLE,
        generator = "MEMBER_SEQ_GENERATOR"
    )
    private Long id;
}
```
#### `@TableGenerator` - 속성
| 속성                   | 설명                                                   | 기본값              |
| ---------------------- | ------------------------------------------------------ | ------------------- |
| name                   | 식별자 생성기 이름                                     | 필수                |
| table                  | 키생성 테이블명                                        | hibernate_sequences |
| pkColumnName           | 시퀀스 컬럼명                                          | sequence_name       |
| valueCloumnNa          | 시퀀스 값 컬럼명                                       | next_val            |
| pkColumnValue          | 키로 사용할 값 이름                                    | 엔티티 이름         |
| initialValue           | 초기값, 마지막으로 생성된 값이 기준이다.               | 0                   |
| allocationSize         | 시퀀스 한 번 호출에 증가하는 수 (성능 최적화에 사용됨) | **50                |
| catalog, schema        | DB catalog, schema 이름                                |                     |
| uniqueConstraints(DDL) | 유니크 제약 조건을 지정할 수 있다. |                     |

### 권장하는 식별자 전략
- 기본 키 제약 조건 : null 아님, 유일, **변하면 안된다.**
- 미래까지 이 조건을 만족하는 자연키는 찾기 어렵다. 대리키(대체키)를 사용하자.
- 예를 들어 주민등록번호도 기본키로 적절하지 않다.
- **권장 : Long형 + 대체키 + 키 생성전략 사용**

## 도메인 : 업무 지식

## 데이터 중심 설계의 문제점
- 테이블 설계에 맞춘 방식
- 테이블의 외래키를 객체에 그대로 가져옴
- 객체 그래프 탐색 불가능
- 참조가 없으므로 UML도 잘못됨


