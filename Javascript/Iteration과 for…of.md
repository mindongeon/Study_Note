## 1. Iteration Protocol

---

데이터 컬렉션을 순회하기 위한 프로토콜 ( 미리 약속된 규칙 ) 이다.
이터레이션 프로토콜을 준수한 객체는 for…of 문으로 순회가 가능하고,
Spread 문법의 피연산자가 될 수 있다.

이터레이션 프로토콜에는 이터러블 프로토콜(iterable protocol)과 이터레이터 프로토콜(iterator protocol)이 있다.

### 1.1 Iterable

---

이터러블 프로토콜을 준수한 객체를 이터러블이라고 한다.
이터러블은 Symbol.iterator 메소드를 구현하거나 프로토타입 체인에 의해 상속한 객체를 말한다. Symbol.iterator 메소드는 이터레이터를 반환한다.
이터러블은 for…of문에서 순회할 수 있으며 Spread문법의 대상으로 사용할 수 있다.

배열은 Symbol.iterator 메소드를 소유한다. 따라서 배열은 이터러블 프로토콜을 준수한 이터러블이다.

```jsx
const array = [1,2,3];

// 배열은 Symbol.iterator 메소드를 소유한다.
// 따라서 배열은 이터러블 프로토콜을 준수한 이터러블이다.
console.log(Symbol.iterator in array); // true

// 이터러블 프로토콜을 준수한 배열은 for...of 문에서 순회 가능하다.
for(const item of array) {
  console.log(item) // 1,2,3
}
```

일반 객체는 Symbol.iterator 메소드를 소유하지 않는다. 
따라서 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아니다.

```jsx
const obj = {a:1, b:2}

console.log(Symbol.iterator in obj); // false
```

일반 객체도 이터러블 프로토콜을 준수하도록 구현하면 이터러블이 된다.

### 1.2 Iterator

---

이터레이터 프로토콜은 next 메소드를 소유하며 next 메소드를 호출하면
이터러블을 순회하며 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 것이다. 이 규약을 준수한 객체가 이터레이터이다.

이터러블 프로토콜을 준수한 이터러블은 Symbol.iterator 메소드를 소유한다.
이 메소드를 호출하면 이터레이터를 반환한다. 이터레이터 프로토콜을 준수한 이터레이터는 next 메소드를 갖는다.

```jsx
const arr = [1,2,3];

const iterator = arr[Symbol.iterator]();

// 이터레이터 프로토콜을 준수한 이터레이터는 next 메소드를 갖는다.
console.log('next' in iterator); // true
```

이터레이터의 next 메소드를 호출하면 value, done 프로퍼티를 갖는 **이터레이터 리절트( iterator result ) 객체를 반환**한다.

```jsx
const arr = [1,2,3];

const iterator = arr[Symbol.iterator]();

let iteratorResult = iterator.next();
console.log(iteratorResult); // { value: 1, done: false }
```

이터레이터의 next 메소드는 이터러블의 각 요소를 순회하기 위한 포인터의 역할을 한다. next 메소드를 호출하면 이터러블을 순차적으로 한 단계씩 순회하며 이터레이터 리절트 객체를 반환한다.

이터레이터의 next 메소드가 반환하는 이터레이터 리절트 객체의 value 프로퍼티는 현재 순회 중인 이터러블의 값을 반환하고 done 프로퍼티는 이터러블의 순회 완료 여부를 반환한다.

### 1.3 빌트인 이터러블

---

ES6에서 제공하는 빌트인 이터러블

> Array, String, Map, Set, TypedArray(Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Int32Array, Uint32Array, Float32Array, Float64Array), DOM data structure(NodeList, HTMLCollection), Arguments
> 

### 1.4 이터레이션 프로토콜의 필요성

---

데이터 소비자(Data consumer)인 for…of 문, Spread문법 등은 빌트인 이터러블을 사용한다. 즉, 이터러블은 데이터 공급자(Data provider)의 역할을 한다.

만약 이처럼 다양한 데이터 소스가 각자의 순회 방식을 갖는다면 데이터 소비자는 다양한 데이터 소스의 순회 방식을 모두 지원해야 한다. 이는 효율적이지 않다. 하지만 **다양한 데이터 소스가 이터레이션 프로토콜을 준수하도록 규정하면 데이터 소비자는 이터레이션 프로토콜만을 지원하도록 구현**하면 된다.

즉, 이터레이션 프로토콜은 다양한 데이터 소스가 하나의 순회 방식을 갖도록 규정하여 데이터 소비자가 효율적으로 다양한 데이터 소스를 사용할 수 있도록 **데이터 소비자와 데이터 소스를 연결하는 인터페이스의 역할**을 한다.

이터러블을 지원하는 데이터 소비자는 내부에서 Symbol.iterator 메소드를 호출해 이터레이터를 생성하고 이터레이터의 next 메소드를 호출하여 이터러블을 순회한다. 그리고 next 메소드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값을 취득한다.

## 2. for…of

---

for…of 문은 내부적으로 이터레이터의 next 메소드를 호출하여 이터러블을 순회하며 next 메소드가 반환한 이터레이터 리절트 객체의 value 프로터피 값을 for…of 문의 변수에 할당한다. 그리고 이터레이터 리절트 객체의 done 프로퍼티 값이 false이면 이터러블의 순회를 계속하고 true이면 이터러블의 순회를 중단한다.

## 3. Custom Iterable

---

### 3.1 구현

---

일반 객체는 이터러블 프로토콜을 준수하지 않으므로 for…of문을 순회할 수 없다.

하지만 일반 객체가 이터레이션 프로토콜을 준수하도록 구현하면 이터러블이 된다.

```jsx
const fibonacci = {
  [Symbol.iterator]() {
    let [pre, cur] = [0,1];
    // 최대값
    const max = 10;

    // Symbol.iterator 메소드를 next 메소드를 리턴.
    // next 메소드는 iterator result를 리턴.
    return {
      // fibonacci 객체를 순회할 때마다 next 메소드 호출.
      next() {
        [pre,cur] = [cur, pre+cur];
        return {
          value : cur,
          done : cur >= max
        }
      }
    }
  }
}

for(const num of fibonacci) {
  console.log(num); // 1 2 3 5 8
}
```

```jsx
const arr = [...fibonacci];
console.log(arr); // [ 1, 2, 3, 5, 8 ]

const [first, second, ...rest] = fibonacci;
console.log(first, second, rest); // 1 2 [ 3, 5, 8 ]
```

### 3.2 이터러블을 생성하는 함수

---

위 finbonacci 이터러블 외부에서 값을 전달할 방법이 없다는 아쉬운 점이 있다.

최대값을 외부에서 전달할 수 있도록 수정하면

```jsx
const fibonacci = function (max) {
  let [pre,cur] = [0,1];
  return {
    [Symbol.iterator]() {
      let [pre, cur] = [0,1];
      return {
        next() {
          [pre,cur] = [cur, pre+cur];
          return {
            value : cur,
            done : cur >= max
          }
        }
      }
    }
  }
}
```

### 3.3 이터러블이면서 이터레이터인 객체를 생성하는 함수

---

이터레이터를 생성하려면 이터러블의 Symbol.iterator 메소드를 호출해야 한다. 이터러블이면서 이터레이터인 객체를 생성하면 Symbol.iterator 메소드를 호출하지 않아도 된다.

```jsx
// iter는 이터러블이면서 이터레이터이다.
let iter = fibonacciFunc(10);
```

iter는 이터러블이면서 이터레이터인 객체이다.

Symbol.iterator 메소드는 this를 반환하므로 next 메소드를 갖는 이터레이터를 반환한다.

```jsx
const fibonacci = function (max) {
  let [pre,cur] = [0,1];
  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre,cur] = [cur, pre+cur];
      return {
        value : cur,
        done : cur >= max
      }
    }
  }
}
```

### 3.4 무한 이터러블과 Lazy evaluation( 지연 평가 )

무한 이터러블(infinite sequence)을 생성하는 함수를 정의해
무한 수열(infinite sequence)을 간단히 표현할 수 있다.

```jsx
const fiboFunc = function() {
  let [pre, cur] = [0,1];
  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre+cur];
      return { value : cur };
    }
  }
}

for(const num of fiboFunc()) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8 ...
}
```

이터러블은 데이터 공급자(Data provider)의 역할을 한다.
빌트인 이터러블은 데이터를 모두 메모리에 확보한 다음 동작한다.
하지만 이터러블은 **Lazy evaluation(지연 평가)**를 통해 값을 생성한다.
Lazy evaluation은 평가 결과가 필요할 때까지 평가를 늦추는 기법이다.

위의 finboFunc 함수는 무한 이터러블을 생성한다.
하지만 데이터를 공급하는 메커니즘을 구현한 것이므로 데이터 소비자 (for…of나 분해 할당)가 실행하기 전까지는 데이터를 생성하지 않는다.

```
💡 데이터가 필요할 때까지 데이터의 생성을 지연, 데이터가 필요한 순간 데이터를 생성한다.
```

## Generator

---

function* = generator

yeild* = 펼쳐서 yeild 1, yeild 2, yeild 3 같이 펼침

```jsx
const arr = [1, 2, 3]
const map = new Map([['a', 1], ['b', 2], ['c', 3]])
const set = new Set([1, 2, 3])
const str = '이런것도 된다.'

const makeGenerator = iterable => function* () {
  yield* iterable
}
const arrGen = makeGenerator(arr)()
const mapGen = makeGenerator(map)()
const setGen = makeGenerator(set)()
const strGen = makeGenerator(str)()

console.log(arrGen.next())
console.log(mapGen.next())
console.log(...setGen)
console.log(...strGen)
```

## Iterable한 개체를 인자로 받을 수 있는 개체

---

```jsx
new Map()
new Set()
new WeakMap()
new WeakSet()
Promise.all()
Promise.race()
Array.from()
```