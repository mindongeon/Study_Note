> **DB와 연결하는 부분**
> 

```
💡 Model Layer = Business Layer + Persistence Layer
```

## DAO ( Data Access Object )

---

- 실제로 DB에 접근하는 객체
- Service와 DB를 연결하는 역할

## DTO ( Data Transfer Object )

---

- 계층간 데이터 교환을 위한 객체
    - DB에서 데이터를 얻어 Service나 Controller 등으로 보낼 때 사용
    - 로직을 갖지 않는 순수 데이터 객체
- Request, Response용 DTO는 View를 위한 클래스

## VO ( Value Object )

---

- 특정한 비즈니스 값을 담는 객체
- VO와 DTO는 동일한 개념이지만 VO는 read only

## Entity

---

- 실제 DB의 테이블과 매칭될 클래스
- 최대한 외부에서 Entity의 getter를 사용하지 않도록
해당 클래스 안에서 필요한 로직 구현

### Entity와 DTO 분리 이유

---

- View Layer 와 DB Layer의 역할 분리
- 테이블과 매핑되는 Entity 변경 시 여러 클래스에 영향
- View와 통신하는 DTO는 변경이 잦아 분리해야함