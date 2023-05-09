# 영속성 관리

## JPA에서 가장 중요한 2가지
- 객체와 관계형 데이터베이스 매핑하기 (Object Relational Mapping)
- 영속성 컨텍스트

## Entity Manager Factory, Entity Manager
![jpa_8](/img/jpa_8.png)

## 영속성 컨텍스트 (Persistence Context)
```엔티티를 영구 저장하는 환경```
- EntityManager.persist(entity);
  => entity를 영속성 컨텍스트에 저장한다.

## Entity Manager, Persistence Context
- 영속성 컨텍스트는 논리적인 개념
- 눈에 보이지 않는다.
- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근

![jpa_9](/img/jpa_9.png)

## Entity의 생명주기
- 비영속 (new/transient)
  - 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
- 영속 (managed)
  - 영속성 컨텍스트에 관리되는 상태
- 준영속 (detached)
  - 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제 (removed)
  - 삭제된 상태

![jpa_10](/img/jpa_10.png)

### 비영속
![jpa_11](/img/jpa_11.png)
```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");
```

### 영속
![jpa_12](/img/jpa_12.png)

```java
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

// 객체를 저장한 상태 (영속)
em.persist(member);
```

### 준영속, 삭제
```java
// 회원 엔티티를 영속성 컨텍스트에서 분리, 준영속 상태
em.detach(member);

//객체를 삭제한 상태 (삭제)
em.remove(member);
```

## 영속성 컨텍스트의 이점
- 1차 캐시
- 동일성(identity) 보장
- 트랜잭션을 지원하는 쓰기 지연 (transactional write-behind)
- 변경 감지 (Dirty Checking)
- 지연 로딩 (Lazy Loading)

## 엔티티 조회, 1차 캐시
![jpa_13](/img/jpa_13.png)
```java
// 엔티티를 생성한 상태 (비영속)
Member member = new Member();
member.setId("member1");
member.setName("회원1");

// 엔티티를 영속
em.persist(member);
```

### 1차 캐시에서 조회
```java
Member member = new Member();
member.setId("member1");
member.setName("회원1");

// 1차 캐시에 저장됨
em.persist(member);

// 1차 캐시에서 조회
Member findMember = em.find(Member.class, "member1");
```

### DB에서 조회
```java
Member findMember2 = em.find(Member.class, "member2");
```
![jpa_14](/img/jpa_14.png)

## 영속 엔티티의 동일성 보장
```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member2");

System.out.println(a == b); // 동일성 비교 true
```
1차 캐시로 반복 가능한 읽기 (REPEATABLE READ) 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 어플리케이션 차원에서 제공

## 엔티티 등록
### 트랜잭션을 지원하는 쓰기 지연
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

### Batch Insert
```xml
<property name="hibernate.jdbc.batch_size" value="10"/>
```
같은 옵션을 사용해 쓰기 지연에 쌓인 SQL을 한번에 보내줄 수 있다.

## 엔티티 수정
### 변경 감지 (Dirty Checking)
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

## 엔티티 삭제
```java
// 삭제 대상 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

em.remove(memberA); // 엔티티 삭제
```

## 플러시
```영속성 컨텍스트의 변경내용을 데이터베이스에 반영```

### 플러시 발생
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

### 영속성 컨텍스트를 플러시하는 방법
- `em.flush()` : 직접 호출
- 트랜잭션 커밋 : 플러시 자동 호출
- JPQL 쿼리 실행 : 플러시 자동 호출

### JPQL 쿼리 실행시 플러시가 자동으로 호출되는 이유
```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

// 중간에 JPQL 실행
query = em.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();
```

### 플러시 모드 옵션
`em.setFlushMode(FlushModeType.COMMIT);`

- FlushModeType.AUTO
  - 커밋이나 쿼리를 실행할 때 플러시 (기본값)
- FlushModeType.COMMIT
  - 커밋할 때만 플러시

### 플러시의 특징
- 영속성 컨텍스트를 비우지 않음
- 영속성 컨텍스트의 변경내용을 데이터베이스에 동기화
- 트랜잭션이라는 작업 단위가 중요 -> 커밋 직전에만 동기화하면 됨

## 준영속 상태
- 영속 -> 준영속
- 영속 상태의 엔티티가 영속성 컨텍스트에서 분리 (detached)
- 영속성 컨텍스트가 제공하는 기능을 사용 못함

### 준영속 상태로 만드는 방법
- `em.detach(entity)` : 특정 엔티티만 준영속 상태로 전환
- `em.clear()` : 영속성 컨텍스트를 완전히 초기화
- `em.close()` : 영속성 컨텍스트를 종료