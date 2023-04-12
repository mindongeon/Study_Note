Name Property : 디버그 할 때 사용

---

### bind 함수

```jsx
function a () { }
const b = function () { }
const h = a.bind(b) // a의 this 값을 b로한 함수 리턴
console.log(h.name)
```

---

```jsx
function a(x,y,z) {
    console.log(this,x,y,z);
}

a.call({},1,2,3);

const b = a.bind({},1,2);
b(3);
```

---

### new.target

new.target = new Person(1) 에서의 Person(1)

```jsx
function Person (name) {
  if (this instanceof Person) {
    this.name = name
  } else {
    throw new Error('new 연산자를 사용하세요.')
  }
}
```

new 사용을 강제하지만 꼼수로 통과될 수 있음

---

```jsx
function Person (name) {
  console.dir(new.target)
  if (new.target !== undefined) {
    this.name = name
  } else {
    throw new Error('new 연산자를 사용하세요.')
  }
}
```

new.taget = new 다음의 녀석

---

‘strict mode’ 가 아닌 경우…

‘sloppy mode’ 에서는 : 브라우저마다 다른 동작. 예상안됨

‘strict mode’ : 함수선언문도 블락스코프에 갇힘

---

보통 [arrow function](/Javascript/Arrow%20Function.md) 사용

객체 : 메소드 축약형 사용

function만 있는 키워드를 최대한 사용하지 않기.

call

apply

bind

b가 값을 가지고 있을 때

(…b) spread

b가 값을 가지고 있지 않을 때

(…b) rest