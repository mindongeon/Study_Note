# JPA 시작

## H2 데이터베이스
- In Memory DB (ex. 교통카드), File DB 둘다 된다.
- 최고의 실습용 DB
- 가볍다.
- 웹용 쿼리툴 제공
- MySQL, Oracle DB 시뮬레이션 기능
- 시퀀스, AUTO INCREMENT 기능 지원

## 메이븐
- 자바 라이브러리, 빌드 관리
- 라이브러리 자동 다운로드 및 의존성 관리
- 최근에는 그래들(Gradle)이 점점 유명

## 데이터베이스 방언
```
SQL표준을 지키지 않는 특정 데이터베이스만의 고유한 기능
```
- JPA는 특정 데이터베이스에 종속되지 않는다.
- 각각의 데이터베이스가 제공하는 SQL문법과 함수는 조금씩 다르다.
  - 가변문자
    - MySQL : VARCHAR
    - Oracle : VARCHAR2
  - 문자열 자르는 함수
    - SQL표준 : SUBSTRING()
    - Oracle : SUBSTR()
  - 페이징
    - MySQL : LIMIT
    - Oracle : ROWNUM

![jpa_5](/img/jpa_5.png)

- **hibernate.dialect** 속성에 지정
  - H2 : org.hibernate.dialect.H2Dialect
  - Oracle 10g : org.hibernate.dialect.Oracle10gDialect
  - MySQL : org.hibernate.dialect.MySQL5InnoDBDialect 
- 하이버네이트는 40가지 이상의 데이터베이스 방언을 지원한다.

### persistence.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <property name="hibernate.hbm2ddl.auto" value="create" />
        </properties>
    </persistence-unit>
</persistence>
```
## JPA 구동 방식
![jpa_6](/img/jpa_6.png)

1. 설정 정보 조회, EntityManagerFactory 생성
```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

// code ...

emf.close();
```

2. EntityManager 생성
- EntityManager의 수명 : 트랜잭션의 시작과 끝
```java
EntityManager em = emf.createEntityManager();

// code ...

em.close();
```

3. Transaction 생성
```java
EntityTransaction tx = em.getTransaction();

tx.begin();

// code ...

tx.commit();
```

### CRUD 예시
```java
public class JpaMain {
     static void insert1(EntityManager em) {
        Member m1 = new Member();
        m1.setId(1L);
        m1.setName("HelloA");

        Member m2 = new Member();
        m2.setId(2L);
        m2.setName("HelloB");

        em.persist(m1);
        em.persist(m2);
    }

    static void find1(EntityManager em) {
         Member findMember = em.find(Member.class, 1L);
         System.out.println(findMember);
    }

    static void remove1(EntityManager em) {
         em.remove(em.find(Member.class, 1L));
    }

    static void update1(EntityManager em) {
         em.find(Member.class, 2L).setName("HelloJPA");
    }

    public static void main(String[] args) {

        // 엔티티 매니저 구하기
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        // 앤티티 매니저의 수명 : 하나의 트랜잭션 시작부터 끝
        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try {
//            insert1(em);
//            find1(em);
//            remove1(em);
            update1(em);

            tx.commit();
        } catch (Exception e) {
            tx.rollback();
        } finally {
            em.close();
        }
        emf.close();
    }
}
```
## 주의
- **엔티티 매니저 팩토리**는 하나만 생성해서 어플리케이션 전체에서 공유
- **엔티티 매니저**는 쓰레드간에 공유X (사용하고 버려야 한다.)
- ```JPA의 모든 데이터 변경은 트랜잭션 안에서 실행```

## JPQL 소개
- 가장 단순한 조회 방법
  - `EntityManager.find()`
  - 객체 그래프 탐색 (`a.getB().getC()`)
- 나이가 18살 이상인 회원을 모두 검색하고 싶다면...? => JPQL 사용

### JPQL 예시
```java
    static void jpqlEx1(EntityManager em) {

        List<Member> rslt = em.createQuery("select m from Member as m", Member.class).setFirstResult(5).setMaxResults(8).getResultList();

        for (Member member : rslt) {
            System.out.println(member);
        }
    }
```
## JPQL
```
객체 지향 SQL   
테이블이 아닌 객체를 대상으로 검색하는 객체 지향 쿼리   
```
- JPA를 사용하면 엔티티 객체를 중심으로 개발
- 문제는 검색 쿼리
- 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능
- 어플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검색 조건이 포함된 SQL이 필요
- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공
- SQL과 문법 유사, SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
- **JPQL은 엔티티 객체**를 대상으로 쿼리
- **SQL은 DB 테이블**을 대상으로 쿼리
- SQL을 추상화해서 특정 DB SQL에 의존하지 않는다.