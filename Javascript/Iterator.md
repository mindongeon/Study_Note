# 소개

---

반복을 위해 설계된 특별한 인터페이스를 가진 객체.

- 객체 내부에는 `next()` 메소드가 있고,
- 이 메소드는 `value`와 `done` 프로퍼티를 가진 객체 반환.
- `done` 프로퍼티는 boolean

# 객체가 이터러블한지 확인

---

```jsx
const isIterable = target => !!target[Symbol.iterator]
```

# 정리

---

- `for-of`, `...(spread operator)`, `forEach 메소드` 등은 내부적으로
- `[Symbol.iterator]`를 실행한 결과 객체를 들고,
- 객체 내부의 `next()` 메소드를
- `done 프로퍼티`가 `true`가 나올 때까지 반복하여 호출한다.
- 즉, Symbol.iterator 메소드의 내용을 위 요구사항에 맞추어 구현하기만 하면 iterable한 객체이다.

> 내가 필요로할 때 next( )를 사용해 가져올 수 있다.
>