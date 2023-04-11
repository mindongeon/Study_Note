# let

---

```jsx
var funcs = []
for (var i = 0; i < 10; i++) {
  funcs.push(function () {
    console.log(i) // 10 10 10 10 ...
  })
}
funcs.forEach(function (f) {
  f()
})
```

### ES5의 해결법

---

```jsx
var funcs = []
for (var i = 0; i < 10; i++) {
  funcs.push((function (v) {
    return function () {
      console.log(v) // 0 1 2 3 4 ...
    }
  })(i))
}
funcs.forEach(function (f) {
  f()
})
```

### ES6 에서

---

```jsx
let funcs = []
for (let i = 0; i < 10; i++) {
  funcs.push(function () {
	  console.log(i) // 0 1 2 3 4 ...
  })
}
funcs.forEach(function (f) {
  f()
})
```

# const

---

선언과 동시에 초기화를 해야함 

```jsx
const OBJ = {
    prop1 : 1,
    prop2 : 2
  }
  OBJ.prop1 = 3
  console.log(OBJ.prop1)
```

```jsx
const OBJ = {}
Object.defineProperty(OBJ, 'prop1', {
  value : 1,
  writable: false,
  configurable: false
})

const OBJ2 = {
  prop1 : 1
	prop2 : [1,2,3,4]
}
Object.freeze(OBJ2) // 참조된 값도 변경시키고 싶지 않을 시

```

얕은 복사 : 객체의 프로퍼티들을 복사 ( depth 1단계까지만 )

깊은 복사 : 객체의 프로퍼티들을 복사 ( 모든 depth에 대해서 )

> 깊은 복사를 해야만 immutable하다.
> 

immutable : 변경이 불가능하다.

1. 생성자로 초기화
2. Setter가 없음

```jsx
var a = {
	a: 1,
	b: [1,2,3],
	c: {d: 1, e: 2}
}

var b = Object.assign({}, a); // 얕은 복사 
// b = { a: 새로운 주소, b: 같은 주소값, c: 같은 주소값 } 복사됨
b.b[1] = 20;
// -> a.b[1]도 20이 됨
// 이렇게 바뀌는 걸 Side Effect 라고 함
```

> var로 전역변수에서 선언하면
> 
> 
> var 전역변수임과 동시에 전역객체의 프로퍼티
> 

delete : 객체에 있는 프로퍼티를 삭제한다.

## For in 문에서는 const 사용 가능

---

```jsx
var obj = {
  prop1: 1,
  prop2: 2,
  prop3: 3
}
// 가능
for (const prop in obj) {
  console.log(prop)
}

// 불가능
for (const i = 0; i < 5; i++) {
  console.log(i)
}
```