## 펼치기 연산자. 전개 연산자.

```jsx
var birds = ['eagle', 'pigeon']
var mammals = ['rabbit', 'cat']
var animals = birds.concat('whale').concat(mammals)
console.log(animals)

const animals2 = [...birds, 'whale', ...mammals]
console.log(animals2)
```

---

값을 가지고 있다 : Spread

값을 안갖고 있다 : Rest

---

```jsx
let originalArr = [2, 3]
const preArr    = [-2, -1]
const sufArr    = [6, 7]

originalArr.unshift(1)
originalArr.push(4)
originalArr = [0, ...originalArr, 5]
console.log(originalArr)

==> 기본형 Deep Copy가 자동으로 이루어짐
```

---

```jsx
let originalArray = [{
  first: 'Hello,',
  second: 'World!'
}, {
  first: 'Welcome',
  second: 'ES6!'
}]
let copiedArray = [...originalArray]
console.log(originalArray[0].first)

copiedArray[0].first = "Hi,"
console.log(originalArray[0].first)

==> 얕은 복사만 수행
```