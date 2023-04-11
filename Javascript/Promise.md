# 1. 목차

- [1. 목차](#1-목차)
- [2. 소개](#2-소개)
  - [2.1. Callback Hell](#21-callback-hell)
- [3. 상세](#3-상세)
  - [3.1. Promise Status](#31-promise-status)
  - [3.2. 문법](#32-문법)
  - [3.3. 확장 Promise 만들기](#33-확장-promise-만들기)
  - [3.4. Promise Chaning (then, catch에서 return)](#34-promise-chaning-then-catch에서-return)
  - [3.5. Error Handling](#35-error-handling)
  - [3.6. Multi Handling](#36-multi-handling)


# 2. 소개

## 2.1. Callback Hell

---

```jsx
step1(function(value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        step5(value4, function(value5) {
            // value5를 사용하는 처리
        });
      });
    });
  });
});
```

가독성을 위해 Promise를 사용하게 됨.

# 3. 상세

---

## 3.1. Promise Status

---

- unsettled (미확정) 상태 : pending. thenable하지 않다.
⇒ 모든 브라우저에서 unsettled는 알려줌
- settled (확정) 상태 : resolved. thenable한 상태.
    - fulfilled (성공)
    - rejected (실패)

```jsx
const promiseTest = param => new Promise((resolve, reject) => {
	setTimeout(() => {
		if (param) {
			resolve("해결 완료")
		} else {
			reject(Error("실패!!"))
		}
	}, 1000)
})

const testRun = param => promiseTest(param)
  .then(text => { console.log(text) })
  .catch(error => { console.error(error) })

const a = testRun(true)
const b = testRun(false)
```

## 3.2. 문법

---

- `new Promise(function)`
- `.then()`, `.catch()`는 언제나 promise를 반환.
- 예시
    
    ```jsx
    const executer = (resolve, reject) => { ... }
    const prom = new Promise(executer)
    
    const onResolve = res => { ... }
    const onReject = err => { ... }
    
    // (1)
    prom.then(onResolve, onReject)
    
    // (2)
    prom.then(onResolve).catch(onReject)
    ```
    
    ```jsx
    new Promise((resolve, reject) => { ... })
    .then(res => { ... })
    .catch(err => { ... })
    ```
    
    ```jsx
    const simplePromiseBuilder = value => {
      return new Promise((resolve, reject) => {
        if(value) { resolve(value) }
        else { reject(value) }
      })
    }
    
    simplePromiseBuilder(1)
      .then(res => { console.log(res) })
      .catch(err => { console.error(err) })
    
    simplePromiseBuilder(0)
      .then(res => { console.log(res) })
      .catch(err => { console.error(err) })
    ```
    
    ```jsx
    const simplePromiseBuilder2 = value => {
      return new Promise((resolve, reject) => {
        if(value) { resolve(value) }
        else { reject(value) }
      })
      .then(res => { console.log(res) })
      .catch(err => { console.error(err) })
    }
    
    simplePromiseBuilder2(1)
    simplePromiseBuilder2(0)
    ```
    
    ```jsx
    const prom = new Promise((resolve, reject) => {
      resolve()
      reject()
      console.log('Promise')
    })
    prom.then(() => {
      console.log('then')
    })
    
    prom.catch(() => {
      console.log('catch')
    })
    
    console.log('Hi!')
    ```
    
    ```jsx
    const prom = new Promise((resolve, reject) => {
      reject()
      resolve()
      console.log('Promise')
    })
    prom.then(() => {
      console.log('then')
    })
    
    prom.catch(() => {
      console.log('catch')
    })
    
    console.log('Hi!')
    ```
    

## 3.3. 확장 Promise 만들기

---

1. `Promise.resolve`, `Promise.reject`

```jsx
Promise.resolve(42)
.then(res => { console.log(res) })
.catch(err => { console.error(err) })

Promise.reject(12)
.then(res => { console.log(res) })
.catch(err => { console.error(err) })
```

1. thenable 객체

```jsx
const thenable = {
  then (resolve, reject) {
    resolve(33)
  }
}
const prom = Promise.resolve(thenable)
prom.then(res => { console.log(res) })
```

```jsx
const thenable = {
  then (resolve, reject) {
    reject(33)
  }
}
const prom = Promise.resolve(thenable)
prom.catch(err => { console.log(err) })
```

## 3.4. Promise Chaning (then, catch에서 return)

---

```jsx
new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('첫번째 프라미스')
  }, 1000)
}).then(res => {
  console.log(res)
  return '두번째 프라미스'
}).then(res => {
  console.log(res)
  return new Promise((resolve, reject) => { // 새로운 조건 추가 확인.
    setTimeout(() => {
          resolve('세번째 프라미스')
    }, 1000)
  })
}).then(res => {
  console.log(res)
  return new Promise((resolve, reject) => { // 새로운 조건 추가 확인.
    setTimeout(() => {
          reject('네번째 프라미스')
    }, 1000)
  })
}).then(res => {
  console.log(res)
}).catch(err => {
  console.error(err)
  return new Error('이 에러는 then에 잡힙니다.')
}).then(res => {
  console.log(res)
  throw new Error('이 에러는 catch에 잡힙니다.')
}).then(res => {
  console.log('출력 안됨')
}).catch(err => {
  console.error(err)
})
```

1. return promise 인스턴스 : promise 인스턴스가 리턴된 것.
2. return 일반값 : promise 객체에 resolved 상태로 반환됨. 그 안에 값이 담김
3. return 안하면 : return undefined ( JS 특 )
4. `Promise.resolve()` or `Promise.reject()` : return 해주지 않는 이상 의미없음
별개의 Promise객체가 생설될 뿐임

## 3.5. Error Handling

---

```jsx
asyncThing1()
.then(asyncThing2)
.then(asyncThing3)
.catch(asyncRecovery1)
.then(asyncThing4, asyncRecovery2)
.catch(err => { console.log("Don't worry about it") })
.then(() => { console.log("All done!") })
```

![promise_chaining.png](/img/promise_chaining.png)

## 3.6. Multi Handling

---

1. `Promise.all()`
    - 일반값은 그냥 resolved된 값으로 간주.
    - iterable의 모든 요소가 fulfilled되는 경우 :
    전체 결과값들을 배열 형태로 then에 전달.
    - iterable의 요소 중 일부가 rejected되는 경우 :
    가장 먼저 rejected 되는 요소 ‘하나’의 결과를 catch에 전달.

```jsx
const arr = [
	1,
	new Promise((resolve, reject) => {
		setTimeout(()=> {
			resolve('resolved after 1000ms')
		}, 1000)
	}),
	'abc',
	() => 'not called function',
	(() => 'IIFE')()
]

Promise.all(arr)
.then(res => { console.log(res) })
.catch(err => { console.error(err) })
```

```jsx
const arr = [
	1,
	new Promise((resolve, reject) => {
		setTimeout(()=> {
			reject('rejected after 1000ms')
		}, 1000)
	}),
	'abc',
	()=> 'not called function',
	(()=> 'IIFE')()
]

Promise.all(arr)
.then(res => { console.log(res) })
.catch(err => { console.error(err) })
```

1. `Promise.race()`
    - iterable의 요소 중 가장 먼저 fulfilled / rejected 되는 요소의 결과를 then / catch에 전달.

```jsx
const arr = [
	new Promise(resolve => {
		setTimeout(()=> { resolve('1번요소, 1000ms') }, 1000)
	}),
	new Promise(resolve => {
		setTimeout(()=> { resolve('2번요소, 500ms') }, 500)
	}),
	new Promise(resolve => {
		setTimeout(()=> { resolve('3번요소, 750ms') }, 750)
	})
]
Promise.race(arr)
.then(res => { console.log(res) })
.catch(err => { console.error(err) })
```

```jsx
const arr = [
	new Promise(resolve => {
		setTimeout(()=> { resolve('1번요소, 0ms') }, 0)
	}),
	'no queue'
]
Promise.race(arr)
.then(res => { console.log(res) })
.catch(err => { console.error(err) })
```