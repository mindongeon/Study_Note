Scope : 범위. 유효공간. 

**함수 스코프 : var**

함수에 의해서 생기는 범위. 변수의 유효 범위

**블럭 스코프  : let, const**

Block에 의해 생기는 유효 범위

{ } 에 의해서 변수의 유효범위가 결정됨

if문. for문. while문. switch-case문.  = ‘문’
문 자체가 하나의 

식 (expression) : 값이 될 수 있는 경우. ( return 값이 있는 것 )

식 = 값.

호이스팅 : 유효 범위의 가장 위로 올라감

TDZ : let, const의 경우 호이스팅되고 초기화 전에 값을 사용할 수 없음

Temporal Dead Zone : 임시사각지대.

기존 var

변수명만 위로 끌어올리고, undefined 할당

```jsx
if(true) {
    let a = 10;
    if (true) {
        console.log(a); //Cannot access 'a' before initalization
        let a = 20;
        console.log(a); // 20
    }
    console.log(a); //10
}
console.log(a); // a is not defined
```

---

---

```jsx
{ let a = 10 }
console.log(a) ::: undefined

{ var a = 10 }
console.log(a) ::: 10
```

- 호이스팅 : 스코프 안에 있는 선언들을 모두 스코프의 최상위로 끌어올리는 것
1. var
    1. 함수 스코프
    2. 키워드 생략 가능
    3. 중복 선언 가능
    4. 호이스팅 당함
        
        ```jsx
        console.log(name) //undefined
        var name = 'hello'
        
        var name;
        console.log(name) // undefined
        name = 'hello'
        
        와 같음
        ```
        
    5. 선언과 동시에 초기화 ( 선언과 동시에 undefined 할당됨)
2. let
    1. block 스코프
    2. 키워드 생략 불가
    3. 중복 선언 가능
3. const
    1. block 스코프
    2. 초기화와 동시에 선언되어야 함
    3. 값 자체를 불변으로 만드는게 아님
    4. 상수를 선언할 때 사용