# JPA

## JPA vs myBatis
myBatis의 Mapper = JPA의 영속성 Context

## Spring Data
webMVC = Servlet을 사용하기 쉽게 spring에서 제공
spring data = JPA를 사용하기 쉽게 spring에서 제공

## ORM
- Object-relational mapping ([객체](/java/%EA%B0%9D%EC%B2%B4%EC%99%80%20%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5.md) 관계 매핑)
- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 중간에서 매핑
- 대중적인 언어에는 대부분 ORM 기술이 존재한다.

## Application과 JDBC 사이에서 동작한다.
![jpa_db](/img/jpa_db.png)

### 저장
![jpa_1](/img/jpa_1.png)
### 조회
![jpa_2](/img/jpa_2.png)

## 과거의 방법
![jpa_3](/img/jpa_3.png)

### EJB - 엔티티 빈 (자바 표준)
- 기술이 복잡하고 어렵다.
- 성능도 좋지 않다.

### 하이버네이트 (오픈 소스)
- EJB - 엔티티 빈을 보완해서 만들었다.

### JPA (자바 표준)
- JAVA에서 하이버네이트와 유사하게 만들었다.
- 실용적이며 자바 표준이다.

#### 자바 표준 ?
- JPA는 인터페이스의 모음
- JPA 2.1 표준 명세를 구현한 3가지 구현체
- **하이버네이트**, EclipseLink, DataNucleus
![jpa_4](/img/jpa_4.png)

## 사용하는 이유
- SQL 중심적인 개발에서 객체 중심으로 개발
- 생산성
- 유지보수
- 패러다임의 불일치 해결
- 성능
- 데이터 접근 추상화와 벤더 독립성
- 표준

### 생산성
- 저장 : `jpa.persist(member)`
- 조회 : `Member member = jpa.find(memberId)`
- 수정 : `member.setName("변경할 이름")`
- 삭제 : `jpa.remove(member)`

### 유지보수
- 기존의 방법 : 필드 변경시 모든 SQL 수정
- JPA : 필드만 추가, SQL은 JPA가 처리

### 패러다임의 불일치 해결
#### JPA와 상속
![JPA_7](/IMG/JPA_7.png)
##### 저장
개발자가 할일 : `jpa.persist(album)`
나머지는 JPA가 : `INSERT INTO ITEM ...`

##### 조회
개발자가 할일 : `Album album = jpa.find(Album.class, albumId)`
나머지는 JPA가 : `SELECT I.\*, A.\* FROM ITEM I JOIN ALBUM A ON I.ITEM_ID = A.ITEM_ID`

#### JPA와 연관관계, 객체 그래프 탐색
##### 연관관계 저장
```java
member.setTeam(team);
jpa.persist(member);
```
##### 객체 그래프 탐색
```java
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam();
```

#### 신뢰할 수 있는 엔티티, 계층
```java
class MemberService {
    ...
    public void process() {
        Member member = memberDAO.find(memberId);
        member.getTeam(); // 자유로운 객체 그래프 탐색
        member.getOrder().getDelivery();
    }
}
```

#### JPA와 비교하기
```동일한 트랜잭션에서 조회한 엔티티는 같음을 보장```
```java
String memberId = "100";
Member m1 = jpa.find(Member.class, memberId);
Member m2 = jpa.find(Member.class, memberId);

m1 == m2; // 같다
```

### JPA의 성능 최적화 기능
#### 1차 캐시와 동일성(identity)보장
1. 같은 트랜잭션 안에서는 같은 엔티티를 반환 - 약간의 조회 성능 향상
2. DB Isolation Level이 Read Commit이어도 어플리케이션에서 Repeatable Read 보장
```java
String memberId = "100";
Member m1 = jpa.find(Member.class, memberId); // SQL
Member m2 = jpa.find(Member.class, memberId); // 캐시

m1 == m2; // true
```
```SQL 1번만 실행```

#### 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
1. 트랜잭션을 커밋할 때까지 INSERT SQL을 모음
2. JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송
```java
tx.begin(); // 트랜잭션 시작

em.persist(memberA);
em.persist(memberB);
em.persist(memberC);
// INSERT SQL을 DB에 보내지 않는다.

// 커밋하는 순간 DB에 INSERT SQL을 모아서 보낸다.
tx.commit(); // 트랜잭션 커밋
```

##### 트랜잭션을 지원하는 쓰기 지연 - UPDATE
1. UPDATE, DELETE로 인한 로우(ROW)락 시간 최소화
2. 트랜잭션 커밋 시 UPDATE, DELETE SQL 실행하고, 바로 커밋
```java
tx.begin();

changeMember(memberA);
deleteMember(memberB);

business_logic(); // 비즈니스 로직 수행 동안 DB ROW 락이 걸리지 않는다.

// 커밋하는 순간 데이터베이스에 UPDATE, DELETE SQL을 보낸다.
tx.commit();

```

#### 지연 로딩과 즉시 로딩
- 지연 로딩 : 객체가 실제 사용될 때 로딩
- 즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회
##### 지연 로딩
```java
// SELECT * FROM MEMBER
Member member = memberDAO.find(memberId);
Team team = member.getTeam();
// SELECT * FROM TEAM
String teamName = team.getName(); 
```
##### 즉시 로딩
```java
// SELECT M.*, T.* FROM MEMBER JOIN TEAM 
Member member = memberDAO.find(memberId);
Team team = member.getTeam();
String teamName = team.getName(); 
