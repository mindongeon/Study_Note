# 목표
- 객체와 테이블 연관관계의 차이를 이해
- 객체의 참조와 테이블의 외래키를 매핑
- 용어 이해
  - 방향(Direction) : 단방향, 양방향
  - 다중성(Multiplicity) : 다대일(N:1), 일대다(1:N), 일대일(1:1), 다대다(N:M) 이해
  - 연관관계의 주인(Owner) : 객체 양방향 연관관계는 관리 주인이 필요

# 연관관계가 필요한 이유

```객체를 테이블에 맞추어 데이터 중심으로 모델링을 하면, 협력 관계를 만들 수 없다.```

- 테이블은 외래키로 조인을 사용해서 연관된 테이블을 찾는다.
- 객체는 참조를 사용해서 연관된 객체를 찾는다.
- 테이블과 객체 사이에는 이런 큰 간격이 있다.

# 단방향 연관관계

## Member
```java
@ManyToOne
@JoinColumn(name = "TEAM_ID")
private Team team;
```

## Main
```java
Team team = new Team();
team.setName("영업팀");
em.persist(team);

Member member = new Member();
member.setTeam(team); // 단방향 연관관계 설정, 참조 저장
member.setUserName("홍길동");
em.persist(member);

em.flush();
em.clear();

Team findTeam = em.find(Team.class, team.getId());
System.out.println(findTeam);

tx.commit();
        
```

# 양방향 연관관계

## Team
```java
@OneToMany(mappedBy = "team")
private List<Member> members = new ArrayList<>();
```

## Main
```java
Team team = new Team();
team.setName("영업팀");
em.persist(team);

Member member = new Member();
member.setTeam(team); // 단방향 연관관계 설정, 참조 저장
member.setUserName("홍길동");
em.persist(member);

Member member1 = new Member();
member1.setTeam(team); // 단방향 연관관계 설정, 참조 저장
member1.setUserName("이순신");
em.persist(member1);

em.flush();
em.clear();

Team findTeam = em.find(Team.class, team.getId());
List<Member> members = findTeam.getMembers();
for (Member m : members) {
    System.out.println(m);
}

tx.commit();

```

# 연관관계

## 객체와 테이블이 관계를 맺는 차이
- 객체 연관관계 = 2개 (단방향 2개)
  - 회원 -> 팀
  - 팀 -> 회원
- 테이블 연관관계 = 1개 (양방향 1개)
  - 회원 <-> 팀의 연관관계 1개

## 객체의 양방향 관계
- 객체에서의 양방향 관계는 서로 다른 단방향 관계 2개다.

## 테이블의 양방향 연관관게
- 테이블은 외래키 하나로 두 테이블의 연관관계를 관리한다.

# 연관관계의 주인 (Owner)

## 양방향 매핑 규칙
- 객체의 두 관계중 하나를 연관관계의 주인으로 지정
- 연관관계의 주인만이 외래키를 관리 (등록, 수정)
- 주인이 아닌쪽은 읽기만 가능
- 주인은 `mappedBy` 속성 사용 X
- 주인이 아니면 `mappedBy` 속성으로 주인 지정

## 누구를 주인으로?
- `@JoinColumn()`라고 명시하면 연관관계의 주인
- FK가 있는 테이블의 엔티티를 주인으로

## 양방향 매핑시 연관관계의 주인에 값을 입력해야 한다.

## 주의 
- 순수 객체 상태를 고려해 항상 양쪽에 값을 설정.
- 연관관계 편의 메소드를 생성.
- 양방향 매핑시 무한 루프 조심
  - ex) `toString()`,lombok, JSON 생성 라이브러리
- Entity는 entity로만, 프론트로 보낼 시 DTO, VO 사용

## 연관관계 편의 메서드

### 연관관계 편의 메서드 사용하지 않을 시
`member.setteam(team)`과 `team.getMembers().add(member)`를 각각 호출해야 한다.

### 연관관계 편의 메서드 사용 시
```java
public void setTeam(Team team) {
    this.team = team;
    team.getMembers().add(this);
}
```

```java
Team t1 = new Team("team1", "팀"1);
em.persist(t1);

Member m1 = new Member("member1", "회원1");
m1.setTeam(t1); // 양방향 설정 완료
em.persist(m1);
```

### 양방향 연관관계는 결국 양쪽 다 신경을 써야 한다.

### 주의사항
`setTeam(team)` 메소드에는 버그가 있다.
```java
m1.setTeam(teamA);
m1.setTeam(teamB);
Member findMember = teamA.getMember(); // member1이 여전히 조회된다.
```
`m1.setTeam(teamA)` 호출 직후는 아래와 같은 상태이다.
![img1](/img/association_mapping_1.PNG)

이후에 `m1.setTeam(teamB)` 호출 직후는 아래와 같은 상태이다.
![img2](/img/association_mapping_2.PNG)

teamB로 변경해도 여전히 teamA -> member1 관계는 유지되어 있다. 이 때문에 연관관계를 변경할 때는 기존 관계를 삭제하는 코드도 추가해주어야 한다.

```java
public void setTeam(Team team) {

  // 기존 관계 제거
  if (this.team != null) {
    this.team.getMembers().remove(this);
  }

  this.team = team;
  team.getMembers().add(this);
}
```

# 양방향 매핑 정리
- 단방향 매핑만으로도 이미 연관관계 매핑은 완료
- 양방향 매핑은 반대 방향으로 조회 기능이 추가된 것 뿐.
- JPQL에서 역방향으로 탐색할 일이 많다.
- 단방향 매핑을 잘 하고 양방향은 필요할 때 추가해도 된다. (테이블에 영향을 주지 않는다.)

## 연관관계의 주인을 정하는 기준
- 비즈니스 로직을 기준으로 연관관계의 주인을 선택하면 안된다.
- 연관관계의 주인은 외래키의 위치를 기준으로 정해야 한다.


## 출처
---
자바 ORM 표준 JPA 프로그래밍