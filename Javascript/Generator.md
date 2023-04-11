# 1. 목차

- [1. 목차](#1-목차)
- [2. 소개](#2-소개)
  - [2.1. 선언 방식](#21-선언-방식)
  - [2.2. 이터레이터로서의 제네레이터](#22-이터레이터로서의-제네레이터)
  - [2.3. yield\* \[iterable\]](#23-yield-iterable)
  - [2.4. 인자 전달하기](#24-인자-전달하기)
  - [2.5. 비동기 작업 수행](#25-비동기-작업-수행)


# 2. 소개

---

- 중간에서 멈췄다가 이어서 실행할 수 있는 함수.
- function 키워드뒤에 `*` 을 붙여 표현하며, 함수 내부에는 `yield` 키워드를 활용.
- 함수 실행 결과에 대해 `next()` 메소드를 호출할 때마다 순차적으로 제너레이터 함수 내부의 `yield` 키워드를 만나기 전까지 실행하고, `yield` 키워드에서 일시정지한다.
- 다시 `next()` 메소드를 호출하면 그 다음 `yield` 키워드를 만날 때까지 함수 내부의 내용을 진행하는 식이다.

## 2.1. 선언 방식

---

```jsx
function* gene() { yield }
const gene = function* () { yield }
const obj = {
	gene1 : function* () { yield }
	*gene2 () { yield }
}
class A {
	*gene () { yield }
}
```

## 2.2. 이터레이터로서의 제네레이터

---

```jsx
const obj = {
  a: 1,
  b: 2,
  c: 3,
  *[Symbol.iterator] () {
    for (let prop in this) {
      yield [prop, this[prop]]
    }
  }
}
console.log(...obj)
for (let p of obj) {
  console.log(p)
}
```

## 2.3. yield* [iterable]

---

```jsx
function* gene () {
  yield* [1, 2, 3, 4, 5]
  yield
  yield* 'abcde'
}
for (const c of gene()) {
  console.log(c)
}
```

## 2.4. 인자 전달하기

---

```jsx
function* gene () {
  let first = yield 1
  let second = yield first + 2
  yield second + 3
}
const gen = gene()
console.log(gen.next().value)
console.log(gen.next().value)
console.log(gen.next().value)
```

1. `let first = yield 1`  첫번째 `next()`를 호출
2. `yield 1` 에서 중지됨
3. `let first` 상태로 대기

4-1. 다시 `next()` 를 호출하면 `first = undefined`

4-2. `next(10)` 을 호출하면 `first = 10`

## 2.5. 비동기 작업 수행

---

userId가 1000번 이후인 데이터를 가져와서
그 중에 4번째에 위치한 데이터를 보고 싶다.

```jsx
const ajaxCalls = () => {
  const res1 = fetch.get('https://api.github.com/users?since=1000')
  const res2 = fetch.get('https://api.github.com/user/1003')
}
const m = ajaxCalls()
```

`res1` 에는 request를 하자마자 바로 결과가 담김.
⇒ response된 결과를 담는게 아닌 불필요한 데이터가 담김.

**Generator를 사용한 비동기 처리**

---

```jsx
const fetchWrapper = (gen, url) => fetch(url)
  .then(res => res.json())
  .then(res => gen.next(res));

function* getNthUserInfo() {
  const [gen, from, nth] = yield;
  const req1 = yield fetchWrapper(gen, `https://api.github.com/users?since=${from || 0}`);
  const userId = req1[nth - 1 || 0].id;
  console.log(userId);
  const req2 = yield fetchWrapper(gen, `https://api.github.com/user/${userId}`);
  console.log(req2);
}
const runGenerator = (generator, ...rest) => {
  const gen = generator();
  gen.next();
  gen.next([gen, ...rest]);
}
runGenerator(getNthUserInfo, 1000, 4);
runGenerator(getNthUserInfo, 1000, 6);
```

Promise 사용 : 비동기를 동기처럼