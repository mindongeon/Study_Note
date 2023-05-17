- [1. JPA에서 가장 중요한 2가지](#1-jpa에서-가장-중요한-2가지)
- [2. Entity Manager Factory, Entity Manager](#2-entity-manager-factory-entity-manager)
- [3. 영속성 컨텍스트 (Persistence Context)](#3-영속성-컨텍스트-persistence-context)
- [4. Entity Manager, Persistence Context](#4-entity-manager-persistence-context)
- [5. Entity의 생명주기](#5-entity의-생명주기)
  - [5.1. 비영속](#51-비영속)
  - [5.2. 영속](#52-영속)
  - [5.3. 준영속, 삭제](#53-준영속-삭제)
- [6. 영속성 컨텍스트와 식별자 값](#6-영속성-컨텍스트와-식별자-값)
- [7. 영속성 컨텍스트와 DB 저장](#7-영속성-컨텍스트와-db-저장)
- [8. 영속성 컨텍스트의 이점](#8-영속성-컨텍스트의-이점)
- [9. 엔티티 조회, 1차 캐시](#9-엔티티-조회-1차-캐시)
  - [9.1. 1차 캐시](#91-1차-캐시)
  - [9.2. 엔티티 조회](#92-엔티티-조회)
  - [9.3. 1차 캐시에서 조회](#93-1차-캐시에서-조회)
  - [9.4. DB에서 조회](#94-db에서-조회)
- [10. 영속 엔티티의 동일성 보장](#10-영속-엔티티의-동일성-보장)
- [11. 엔티티 등록](#11-엔티티-등록)
  - [11.1. 트랜잭션을 지원하는 쓰기 지연](#111-트랜잭션을-지원하는-쓰기-지연)
  - [11.2. Batch Insert](#112-batch-insert)
- [12. 엔티티 수정](#12-엔티티-수정)
  - [12.1. SQL 수정 쿼리의 문제점](#121-sql-수정-쿼리의-문제점)
  - [12.2. 변경 감지 (Dirty Checking)](#122-변경-감지-dirty-checking)
  - [12.3. DynamicUpdate](#123-dynamicupdate)
- [13. 엔티티 삭제](#13-엔티티-삭제)
- [14. 플러시](#14-플러시)
  - [14.1. 플러시 발생](#141-플러시-발생)
  - [14.2. 영속성 컨텍스트를 플러시하는 방법](#142-영속성-컨텍스트를-플러시하는-방법)
  - [14.3. JPQL 쿼리 실행시 플러시가 자동으로 호출되는 이유](#143-jpql-쿼리-실행시-플러시가-자동으로-호출되는-이유)
  - [14.4. 플러시 모드 옵션](#144-플러시-모드-옵션)
  - [14.5. 플러시의 특징](#145-플러시의-특징)
- [15. 준영속 상태](#15-준영속-상태)
  - [15.1. 준영속 상태로 만드는 방법](#151-준영속-상태로-만드는-방법)
  - [15.2. 준영속 상태의 특징](#152-준영속-상태의-특징)
- [16. 병합 `merge()`](#16-병합-merge)
  - [16.1. 준영속 병합](#161-준영속-병합)
  - [16.2. 비영속 병합](#162-비영속-병합)
- [17. 정리](#17-정리)
- [18. 출처](#18-출처)


# 1. JPA에서 가장 중요한 2가지
- 객체와 관계형 데이터베이스 매핑하기 (Object Relational Mapping)
- 영속성 컨텍스트

# 2. Entity Manager Factory, Entity Manager
![jpa_8](/img/jpa_8.png)

# 3. 영속성 컨텍스트 (Persistence Context)
```엔티티를 영구 저장하는 환경```
- EntityManager.persist(entity);
  => entity를 영속성 컨텍스트에 저장한다.

# 4. Entity Manager, Persistence Context
- 영속성 컨텍스트는 논리적인 개념
- 눈에 보이지 않는다.
- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근

![jpa_9](/img/jpa_9.png)

# 5. Entity의 생명주기
- 비영속 (new/transient)
  - 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
- 영속 (managed)
  - 영속성 컨텍스트에 관리되는 상태
- 준영속 (detached)
  - 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제 (removed)
  - 삭제된 상태

![jpa_10](/img/jpa_10.png)

## 5.1. 비영속
![jpa_11](/img/jpa_11.png)
```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");
```

## 5.2. 영속
![jpa_12](/img/jpa_12.png)

```java
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

// 객체를 저장한 상태 (영속)
em.persist(member);
```

## 5.3. 준영속, 삭제
```java
// 회원 엔티티를 영속성 컨텍스트에서 분리, 준영속 상태
em.detach(member);

//객체를 삭제한 상태 (삭제)
em.remove(member);
```
# 6. 영속성 컨텍스트와 식별자 값
- 영속성 컨텍스트는 엔티티를 식별자 값(`@Id`로 테이블의 기본 키와 매핑한 값)으로 구분한다.
- **영속 상태는 식별자 값이 반드시 있어야 한다.**

# 7. 영속성 컨텍스트와 DB 저장
- JPA는 보통 트랜잭션을 커밋하는 순간 영속성 컨텍스트에 새로 저장된 엔티티를 DB에 반영한다.


# 8. 영속성 컨텍스트의 이점
- 1차 캐시
- 동일성(identity) 보장
- 트랜잭션을 지원하는 쓰기 지연 (transactional write-behind)
- 변경 감지 (Dirty Checking)
- 지연 로딩 (Lazy Loading)

# 9. 엔티티 조회, 1차 캐시

## 9.1. 1차 캐시
```영속성 컨텍스트 내부의 캐시```
- 영속 상태의 엔티티는 모두 저장된다.
- ex) 영속성 컨텍스트 내부에 `Map`이 하나 있고, 키는 `@Id`로 매핑한 식별자, 값은 엔티티 인스턴스.

![jpa_13](/img/jpa_13.png)
```java
// 엔티티를 생성한 상태 (비영속)
Member member = new Member();
member.setId("member1");
member.setName("회원1");

// 엔티티를 영속
em.persist(member);
```
이 코드를 실행하면 1차 캐시에 회원 엔티티를 저장한다.   
**회원 엔티티는 아직 DB에 저장되지 않았다.**

- 1차 캐시의 키는 식별자 값이다.
- 식별자 값은 DB의 기본키와 매핑되어 있다.


```=> 영속성 컨텍스트에 데이터를 저장하고 조회하는 모든 기준은 DB의 기본키 값이다.```

## 9.2. 엔티티 조회

1. `em.find()`를 호출하면 1차 캐시에서 식별자 값으로 엔티티를 찾는다.
2. 찾는 엔티티가 있으면 DB를 조회하지 않고 메모리에 있는 1차 캐시에서 엔티티를 조회한다.
3. 찾는 엔티티가 없다면 엔티티 매니저는 DB를 조회해 엔티티를 생성한다. 
4. 엔티티를 1차 캐시에 저장한 후 영속 상태의 엔티티를 반환한다.

## 9.3. 1차 캐시에서 조회

```java
Member member = new Member();
member.setId("member1");
member.setName("회원1");

// 1차 캐시에 저장됨
em.persist(member);

// 1차 캐시에서 조회
Member findMember = em.find(Member.class, "member1");
```

## 9.4. DB에서 조회
```java
Member findMember2 = em.find(Member.class, "member2");
```
![jpa_14](/img/jpa_14.png)

# 10. 영속 엔티티의 동일성 보장
```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member2");

System.out.println(a == b); // 동일성 비교 true
```
1차 캐시로 반복 가능한 읽기 (REPEATABLE READ) 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 어플리케이션 차원에서 제공

# 11. 엔티티 등록
## 11.1. 트랜잭션을 지원하는 쓰기 지연
- 엔티티 매니저는 트랜잭션을 커밋하기 직전까지 DB에 엔티티를 저장하지 않는다.
- 내부 쿼리 저장소에 INSERT SQL를 모아둔다.
- 트랜잭션이 커밋될 때 모아둔 쿼리를 DB에 보낸다.
- 트랜잭션을 커밋하면 엔티티 매니저는 우선 영속성 컨텍스트를 플러시한다.
- 플러시 : 영속성 컨텍스트의 변경 내용을 DB에 동기화하는 작업
- 이 때 등록, 수정, 삭제한 엔티티를 DB에 반영한다.

```영속성 컨텍스트의 변경 내용을 DB에 동기화한 후에 실제 DB에 트랜잭션을 커밋한다.```



```java
EntityManager em = emf.createEntityManager();
EntityTransaction tx = em.getTransaction();
tx.begin();

em.persist(memberA);
em.persist(memberB);
// 여기까지 INSERT SQL을 DB에 보내지 않는다.

// 커밋하는 순간 INSERT SQL을 보낸다.
tx.commit();
```
![jpa_15](/img/jpa_15.png)
![jpa_16](/img/jpa_16.png)


## 11.2. Batch Insert
```xml
<property name="hibernate.jdbc.batch_size" value="10"/>
```
같은 옵션을 사용해 쓰기 지연에 쌓인 SQL을 한번에 보내줄 수 있다.

# 12. 엔티티 수정

## 12.1. SQL 수정 쿼리의 문제점
- 수정 쿼리가 많아지며, 비즈니스 로직을 분석하기 위해 SQL을 계속 확인해야 한다.
- 결국 비즈니스 로직이 SQL에 의존하게 된다.


## 12.2. 변경 감지 (Dirty Checking)

1. 트랜잭션을 커밋하면 엔티티 매니저 내부에서 먼저 `flsuh()`가 호출된다.
2. 엔티티와 스냅샷을 비교, 변경된 엔티티를 찾는다.
3. 변경된 엔티티가 있으면 수정 쿼리를 생성, 쓰기 지연 SQL에 보낸다.
4. 쓰기 지연 저장소의 SQL을 DB에 보낸다.
5. DB 트랜잭션을 커밋한다.

```변경 감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티에만 적용된다.```

```java
EntityManager em = emf.createEntityManager();
EntityTransaction tx = em.getTransaction();
tx.begin();

// 영속성 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

// 영속성 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

// em.update(member) 같은 코드가 없다.

em.persist(memberA);

tx.commit();
```

![jpa_17](/img/jpa_17.png)


## 12.3. DynamicUpdate
- 필드가 많거나 저장되는 내용이 너무 크면 동적으로 UPDATE SQL을 생성하는 전략이 있다. 
- 하이버네이트의 확장 기능이다.

```java
@Entity
@org.hibernate.annotations.DynamicUpdate
@Table(name = "member")
public class Member {...}
```
`@org.hibernate.annotations.DynamicUpdate` 어노테이션을 사용하면 수정된 데이터만 사용해서 동적으로 UPDATE SQL을 생성한다.

```text
상황에 따라 다르지만 컬럼이 대략 30개 이상되면 @DynamicUpdate를 사용한 동적 수정 쿼리가 빠르다고 한다. 최적화가 필요하다고 느껴지면 그 때 수정해도 된다.
```

# 13. 엔티티 삭제
- 엔티티를 삭제하기 위해서는 먼저 삭제 대상 엔티티를 조회해야 한다.
- `em.remove()`를 사용해 삭제한다.
- 즉시 삭제하는 것이 아닌 삭제 쿼리를 쓰기 지연 SQL 저장소에 등록한다.
- 트랜잭션을 커밋해 플러시를 호출하면 실제 DB에 삭제 쿼리를 전달한다.
- 영속성 컨텍스트에서는 `em.remove()`를 호출하는 순간 제거된다.
- 삭제된 엔티티는 재사용하지 말고 자연스럽게 가비지 컬렉션의 대상이 되도록 두는 것이 좋다.


```java
// 삭제 대상 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

em.remove(memberA); // 엔티티 삭제
```

# 14. 플러시
```영속성 컨텍스트의 변경내용을 데이터베이스에 반영```

## 14.1. 플러시 발생
- 변경 감지
- 수정된 엔티티 쓰기 지연 SQL 저장소에 등록
- 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송 (등록, 수정, 삭제 쿼리)
```
1차 캐시는 지우지 않음.
실제 DB에 데이터가 들어가지는 않음.
commit이나 rollback에서만 데이터가 들어감
```

`em.flush` : 1차 캐시를 비우지 않고, DB에 SQL 전송   
`em.clear` : 1차 캐시를 비움.

## 14.2. 영속성 컨텍스트를 플러시하는 방법
- `em.flush()` : 직접 호출
- 트랜잭션 커밋 : 플러시 자동 호출
- JPQL 쿼리 실행 : 플러시 자동 호출

## 14.3. JPQL 쿼리 실행시 플러시가 자동으로 호출되는 이유
```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

// 중간에 JPQL 실행
query = em.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();
```

## 14.4. 플러시 모드 옵션
`em.setFlushMode(FlushModeType.COMMIT);`

- FlushModeType.AUTO
  - 커밋이나 쿼리를 실행할 때 플러시 (기본값)
- FlushModeType.COMMIT
  - 커밋할 때만 플러시

## 14.5. 플러시의 특징
- 영속성 컨텍스트를 비우지 않음
- 영속성 컨텍스트의 변경내용을 데이터베이스에 동기화
- 트랜잭션이라는 작업 단위가 중요 -> 커밋 직전에만 동기화하면 됨

# 15. 준영속 상태
- 영속 -> 준영속
- 영속 상태의 엔티티가 영속성 컨텍스트에서 분리 (detached)
- 영속성 컨텍스트가 제공하는 기능을 사용 못함

## 15.1. 준영속 상태로 만드는 방법
- `em.detach(entity)` : 특정 엔티티만 준영속 상태로 전환
- `em.clear()` : 영속성 컨텍스트를 완전히 초기화
- `em.close()` : 영속성 컨텍스트를 종료

## 15.2. 준영속 상태의 특징

```거의 비영속 상태에 가깝다```
영속성 컨텍스트가 관리하지 않으므로 1차 캐시, 쓰기 지연, 변경 감지, 지연 로딩을 포함한 영속성 컨텍스트가 제공하는 어떠한 기능도 동작하지 않는다.

```식별자 값을 가지고 있다```
비영속 상태는 식별자 값이 없을 수도 있지만 준영속 상태는 이미 한 번 영속 상태였으므로 반드시 식별자 값을 가지고 있다.

```지연 로딩을 할 수 없다```
지연 로딩은 실제 객체 대신 프록시 객체를 로딩해두고 해당 객체를 실제 사용할 때 영속성 컨텍스트를 통해 데이터를 불러오는 방법이다.   
하지만 준영속 상태는 영속성 컨텍스트가 관리하지 않으므로 지연 로딩시 문제가 발생한다.

# 16. 병합 `merge()`
- 준영속 상태의 엔티티를 다시 영속 상태로 변경하려면 병합을 사용한다.
- `merge()` 메소드는 준영속 상태의 엔티티를 받아 그 정보로 **새로운 영속 상태의 엔티티를 반환**한다.

```병합은 준영속, 비영속을 신경 쓰지 않는다. 식별자 값으로 엔티티를 조회할 수 있다면 불러서 병합하고 조회할 수 없다면 새로 생성해 병합한다. 따라서 병합은 save or update 기능을 수행한다.```

```java
Member mergeMember = em.merge(member);
```
## 16.1. 준영속 병합
1. `merge()`를 실행한다.
2. 파라미터로 넘어온 준영속 엔티티의 식별자 값으로 1차 캐시에서 엔티티를 조회한다.
   1. 만약 1차 캐시에 엔티티가 없다면 DB에서 엔티티를 조회, 1차 캐시에 저장한다.
3. 조회한 영속 엔티티에 `merge()`의 엔티티의 값을 채워 넣는다.
4. 새로운 엔티티를 반환한다.

`merge()`는 파라미터로 넘어온 준영속 엔티티를 사용해 새롭게 병합된 영속 상태의 엔티티를 반환한다.   
파라미터로 넘어온 엔티티는 병합 후에도 준영속 상태로 남아 있다.

## 16.2. 비영속 병합
- 비영속 엔티티도 영속 상태로 만들 수 있다.

병합은 파라미터로 넘어온 엔티티의 식별자 값으로 영속성 컨텍스트를 조회하고 찾는 엔티티가 없으면 DB에서 조회한다. 만약 DB에서도 없다면 새로운 엔티티를 생성, 병합한다.

# 17. 정리
1. 엔티티 매니저는 엔티티 매니저 팩토리에서 생성한다. J2SE 환경에서는 엔티티 매니저 생성 시 내부에 영속성 컨텍스트도 함께 생성된다. 영속성 컨텍스트는 엔티티 매니저를 통해 접근 가능하다.
2. 영속성 컨텍스트는 어플리케이션과 DB 사이에서 객체를 보관하는 가상의 DB 같은 역할을 한다. 영속성 컨텍스트 덕분에 1차 캐시, 동일성 보장, 트랜잭션을 지원하는 쓰기 지연, 변경 감지, 지연 로딩 기능을 사용할 수 있다.
3. 영속성 컨텍스트에 저장한 엔티티는 플러시 시점에서 DB에 반영되는데 일반적으로 트랜잭션을 커밋할 때 영속성 컨텍스트가 플러시된다.
4. 영속성 컨텍스트가 관리하는 엔티티를 영속 상태의 엔티티라 하는데, 영속성 컨텍스트가 해당 엔티티를 더 이상 관리하지 못하면 그 엔티티는 준영속 상태의 엔티티라 한다. 준영속 상태의 엔티티는 더는 영속성 컨텍스트의 관리를 받지 못하므로 영속성 컨텍스트가 제공하는 1차 캐시, 동일성 보장, 트랜잭셔을 지원하는 쓰기 지연, 변경 감지, 지연 로딩 같은 기능들을 사용할 수 없다.

# 18. 출처
---
자바 ORM 표준 JPA 프로그래밍