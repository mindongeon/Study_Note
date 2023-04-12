```jsx
var x = 10
var y = 20
var obj = {
  x: x,
  y: y
}
////////////////////////////////////
const x = 10
const y = 20
const obj = {
  x,
  y
}
```

---

destructuring assignment

```jsx
const {name,age} = {
  name: '재남',
  age: 30
}
console.log(name, age) // 재남 30
```

---

concise methods ( 간결한 메소드 )

```jsx
var obj = {
  name: 'foo',
  getName: function () { return this.name }
}
///////////////////////////////////////
const obj = {
  name: 'foo',
  getName () { return this.name }
}
```

function 생략시 생성자가 만들어지지 않음

객체 생성 가능 여부

```jsx
const a = new obj.getName();
```

생성자 함수 : prototype, prototype 을 가지고 있음

나머지 : prototype 만 가지고 있음

__proto__ = prototype