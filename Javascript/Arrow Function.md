## 객체 리턴 시

---

```jsx
// 객체 리턴 시
var a = function(x) {
    return {
        x:x
    }
}

//객체 리턴시 괄호로 알려줘야 함
var a = x => ({x});
```

## 클로저

---

```jsx
// 클로저
var f = a => b => a + b;

var y = f(1);
var x = y(2);

console.log(x);
```

## 줄여쓰기

---

```jsx
var a = () => 10;
// 이렇게 사용가능
var a = _ => 10;
// 권장되지는 않음 
```

## 명시적 this 바인딩

---

```jsx
const obj = {
    a () {
        console.log(this); // a();
        const b = () => {
            console.log(this); // a();
        }
        const c = function() {
            console.log(this); // window객체
        }
        b();
        c();
    }
}

obj.a();
```

---

메소드로 사용이 안됨

상위 객체를 따라가는데 메소드의 상위는 window

---

```jsx
const a = () => {
  console.log(this)
}
a.call({a: 1})
```

화살표 함수 사용시 call 같은 함수로 this를 변경해줄 수 없음

---

생성자 함수로 사용할 수 없다.

1. prototype 프로퍼티 X → 생성자 함수로 X
2. arguments, callee → hidden. invoke 해야만 값을 얻을수 있다.

method는 메소드로만.

arrow는 함수로만.