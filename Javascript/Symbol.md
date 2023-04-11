## Symbol이란

---

- primitive value => 유일무이하고 고유한 존재.
- 비공개 멤버에 대한 needs에서 탄생.
- 기본적인 열거대상에서 제외.
- 암묵적 형변환 불가.
- 주로 중복되지 않는 키 값을 만들 때 사용됨

```
💡 ES6 에서 새롭게 추가된 변경 불가능한 원시타입.
주로 이름의 충돌 위험이 없는 유일한 객체 프로퍼티 키 생성을 위해 사용한다.
```

## Symbol의 생성

---

Sysbol() 함수로 생성한다.

호출할 때마다 Symbol값을 생성한다.

이 때 생성된 Symbol값은 객체가 아닌 변경 불가능한 원시타입의 값.

```jsx
let mySymbol = Symbol(); // mySymbol은 충돌위험이 없는 유일한 프로퍼티 키

console.log(mySymbol); // Symbol()
console.log(typeof mySymbol); //symbol
```

String, Number, Boolean 같은 생성자 함수를 통해 생성하는 것과 다르게 new 연산자를 사용하지 않는다.

```jsx
new Symbol(); //Symbol is not a constructor
```

Symbol 함수에는 문자열을 인자로 전달 가능하다.

Symbol에 대한 설명 정도로 디버깅시에 사용된다.

## Sysbol의 사용

---

객체의 프로퍼티 키는 빈 문자열을 포함하는 모든 문자열로 만들 수 있다.

```jsx
const obj = {};

obj.prop = 'myProp';
obj[123] = 123;
//obj.123 = 123; // SyntaxError: Unexpected token
obj['prop' + 123] = false;

console.log(obj); // { 123: 123, prop: 'myProp', prop123: false }
```

Symbol 값도 객체의 프로퍼티 키로 사용 가능하다.

Symbol 값은 유일한 값이므로 

> Symbol 값을 키로 갖는 프로퍼티는 다른 어떠한 프로퍼티와도 충돌하지 않는다.
> 

```jsx
const obj = {};

const mySymbol = Symbol('mySymbol');
obj[mySymbol] = 123;

console.log(obj); // { [Symbol(mySymbol)]: 123 }
console.log(obj[mySymbol]) //123
```

## Symbol 객체

---

Symbol 객체는 프로퍼티와 메소드를 가지고 있다.

Symbol 객체의 프로퍼티 중에 length와 prototype을 제외한 프로퍼티를 `Well-Known Symbol` 이라고 부른다.

### Symbol.iterator

---

`Well-Known Symbol` 은 자바스크립트 엔진에 상수로 존재한다.

자바스크립트 엔진은 `Well-Known Symbol`을 참조하여 일정한 처리를 한다.

예를 들어, 어떤 객체가 Symbol.iterator를 프로퍼티 key로 사용한 메소드를 가지고 있으면 자바스크립트 엔진은 이 객체가 이터레이션 프로토콜을 따르는 것으로 간주하고 이터레이터로 동작하도록 한다.

Symbol.iterator를 프로퍼티 key로 사용하여 메소드를 구현하고 있는 빌트인 객체(빌트인 이터러블)는 아래와 같다. 아래의 객체들은 이터레이션 프로토콜을 준수하고 있으며 이터레이터를 반환한다.

```
Array.prototype[Symbol.iterator]
String.prototype[Symbol.iterator]
Map.prototype[Symbol.iterator]
Set.prototype[Symbol.iterator]
arguments[Symbol.iterator]

**DOM data structures**
NodeList.prototype[Symbol.iterator]
HTMLCollection.prototype[Symbol.iterator]
```

```jsx
// 이터러블
// Symbol.iterator를 프로퍼티 key로 사용한 메소드를 구현해야 한다.
// 배열에는 Array.prototype[Symbol.iterator] 메소드가 구현되어 있다.
const iterable = ['a','b','c']

// 이터레이터
// 이터러블의 Symbol.iterator를 프로퍼티 key로 사용한 메소드는 이터레이터를 반환한다.
const iterator = iterable[Symbol.iterator]();

// 이터레이터는 순회 가능한 자료 구조인 이터러블의 요소를 탐색하기 위한 포인터로서
// value, done 프로퍼티를 갖는 객체를 반환하는 next() 함수를 메소드로 갖는 객체이다.
// 이터레이터의 next() 메소드를 통해 이터러블 객체를 순회할 수 있다.
console.log(iterator.next()); // { value: 'a', done: false }
console.log(iterator.next()); // { value: 'b', done: false }
console.log(iterator.next()); // { value: 'c', done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

## Symbol.for

---

인자로 전달받은 문자열을 키로 사용하여 Symbol 값들이 저장되어 있는 전역 Symbol 레지스트리에서 해당 키와 일치하는 저장된 Symbol 값을 검색한다.

이 때 검색에 성공하면 검색된 Symbol 값들을 반환하고, 실패한다면 새로운 Symbol값을 생성하여 해당 키로 전역 Symbol 레지스트리에 저장한 후, Symbol 값을 반환한다.

```jsx
// 전역 Symbol 레지스트리에 foo라는 키로 저장된 Symbol이 없으면 새로운 Symbol 생성
const s1 = Symbol.for('foo');
// 전역 Symbol 레지스트리에 foo라는 키로 저장된 Symbol이 있으면 해당 Symbol 반환
const s2 = Symbol.for('foo');

console.log(s1 === s2); // true
```

Symbol 함수는 매번 다른 Symbol값을 생성하는 것에 반해
Symbol.for 메소드는 하나의 Symbol을 생성하여 여러 모듈이 키를 통해 같은 Symbol을 공유할 수 있다.

Symbol.for 메소드를 통해 생성된 Symbol값은 반드시 키를 갖는다.
이에 반해 Symbol 함수를 통해 생성된 Symbol값은 키가 없다.

```jsx
const shareSymbol = Symbol.for('myKey');
const key1 = Symbol.keyFor(shareSymbol);
console.log(key1); //myKey

const unsharedSymbol = Symbol('myKey');
const key2 = Symbol.keyFor(unsharedSymbol);
console.log(key2); //undefined
```