# 목차

- [목차](#목차)
- [1. 소개](#1-소개)
- [2. 상세](#2-상세)
  - [2-1. 선언방식](#2-1-선언방식)
  - [2-2. 기존 방식과의 차이점](#2-2-기존-방식과의-차이점)
  - [2-3. ‘문’이 아닌 ‘식’ 이다.](#2-3-문이-아닌-식-이다)
  - [2-4. 접근자](#2-4-접근자)
  - [2-5. computed property names](#2-5-computed-property-names)
  - [2-6. Generator](#2-6-generator)
  - [2-7. Symbol.iterator](#2-7-symboliterator)
  - [2-8. static method](#2-8-static-method)
- [3. 클래스 상속](#3-클래스-상속)
  - [3-1. 소개](#3-1-소개)
  - [3-2. 상세](#3-2-상세)
    - [3-2-1. class \[서브클래스명\] extends \[슈퍼클래스명\] { \[서브클래스 본문\] }](#3-2-1-class-서브클래스명-extends-슈퍼클래스명--서브클래스-본문-)
    - [3-2-2. super (내부 키워드로써, 허용된 동작 외엔 활용 불가)](#3-2-2-super-내부-키워드로써-허용된-동작-외엔-활용-불가)


# 1. 소개

---

```jsx
function Person1 (name) {
  this.name = name
}
Person1.prototype.getName = function () { // prototype method
  return this.name
}
Person1.isPerson = function (obj) { // static method
  return obj instanceof this
}
const jn1 = new Person1('재남')
console.log(jn1.getName())
console.log(Person1.isPerson(jn1))

//--------------------------------------

class Person2 {
  constructor (name) { this.name = name }
  getName () { return this.name }
  static isPerson (obj) { return obj instanceof this }
}
const jn2 = new Person2('재남2')
console.log(jn2.getName())
console.log(Person2.isPerson(jn2))
```

![Untitled](/img/class.png)

Static 함수 : 생성자 함수 내부에 생성되는 함수.

⇒ 인스턴스나 prototype에 생성되지 않아서 호출 불가

⇒ `Person.isPerson()`으로만 호출 가능

# 2. 상세

---

## 2-1. 선언방식

---

```jsx
// 클래스 리터럴
class Person1 { }
console.log(Person1.name)

// 기명 클래스 표현식
const Person2 = class Person22 { }
console.log(Person2.name)

// 익명 클래스 표현식
let Person3 = class { }
console.log(Person3.name)
```

## 2-2. 기존 방식과의 차이점

---

- let, const와 마찬가지로 TDZ가 존재, 블록스코프에 갇힘.

```jsx
if(true) {
  class A { }
  const a = new A() // ok
  if(true) {
    const b = new A() // TDZ
    class A { }
  }
}
const c = new A() // reference Error
```

- class 내부는 strict mode가 강제된다.
- 모든 메소드를 열거할 수 없다.

```jsx
class A {
  a () { }
  b () { }
  static c () { }
}

for (let p in A.prototype) {
  console.log(p)
}

A.prototype.a = function () { }
A.prototype.d = function () { }

for (let p in A.prototype) {
  console.log(p)
}
```

- constructor를 제외한 모든 메소드는 `new` 명령어로 호출 불가.

```jsx
class A {
  constructor () { }
  a () { }
  static b () { }
}
const a = new A.prototype.constructor()
const b = new A.prototype.a()
const c = new A.prototype.b()

const d = new A()
const e = new d.constructor()
```

- 생성자로서만 동작한다.

```jsx
class A { }
A()
```

- 클래스 내부에서 클래스명 수정

```jsx
let A = class {
  constructor () { A = 'A' }
}
const a = new A()
console.log(A)

const B = class {
  constructor () { B = 'B' }
}
const b = new B()

class C {
  constructor () { C = 'C' }
}
const c = new C()
```

- 클래스 외부에서 클래스명 수정

```jsx
let A = class { }
A = 10;             // ok

const B = class { }
B = 10;             // Uncaught Type Error: Assignment to constant variable

class C { }
C = 10;             // ok
```

- 외부에서 prototype을 다른 객체로 덮어씌울 수 없다 ( readonly )

```jsx
class A {
  a () { }
}
A.prototype = {
  a () { console.log(1) }
}
const a = new A()
a.a()
```

## 2-3. ‘문’이 아닌 ‘식’ 이다.

---

```jsx
const jn = new class {
  constructor (name) { this.name = name }
  sayName () { console.log(this.name) }
}('재님')
jn.sayName()
```

```jsx
const instanceGenerator = (className, ...params) => new className(...params)
class Person {
  constructor (name) { this.name = name }
  sayName () { console.log(this.name) }
}

const jn = instanceGenerator(Person, '재나')
const sh = instanceGenerator(class {
  constructor (name) { this.name = name }
  sayName () { console.log(this.name) }
}, '성후')

jn.sayName()
sh.sayName()
```

## 2-4. 접근자

---

```jsx
class CustomHTMLElement {
  constructor (element) {
    this._element = element
  }
  get html () {
    return this._element.innerHTML
  }
  set html (value) {
    this._element.innerHTML = value
  }
}
console.log(Object.entries(CustomHTMLElement.prototype))
console.log(Object.getOwnPropertyDescriptor(CustomHTMLElement.prototype, 'html'))
```

## 2-5. computed property names

---

```jsx
const method1 = 'sayName'
const fullNameGetter = 'fullname'
class Person {
  constructor (name) { this.name = name }
  [method1] () { console.log(this.name) } // 안쪽에서도 사용가능
  get [fullNameGetter] () { return this.name + ' 정' }
}
const jn = new Person('재나')
jn.sayName()
console.log(jn.fullname)
```

## 2-6. Generator

---

```jsx
class A {
  *generator () {
    yield 1
    yield 2
  }
}
const a = new A()
const iter = a.generator()
console.log(...iter)
```

## 2-7. Symbol.iterator

---

```jsx
class Products {
  constructor () {
    this.items = new Set()
  }
  addItem (name) {
    this.items.add(name)
  }
  [Symbol.iterator] () {
    let count = 0
	  const items = [...this.items]
    return {
      next () {
        return {
          done: count >= items.length,
          value: items[count++]
        }
      }
    }
  }
}
const prods = new Products()
prods.addItem('사과')
prods.addItem('배')
prods.addItem('포도')
for (let x of prods) {
  console.log(x)
}
```

```jsx
class Products {
  constructor () {
    this.items = new Set()
  }
  addItem (name) {
    this.items.add(name)
  }
  *[Symbol.iterator] () {
    yield* this.items
  }
}
const prods = new Products()
prods.addItem('사과')
prods.addItem('배')
prods.addItem('포도')
for (let x of prods) {
  console.log(x)
}
```

## 2-8. static method

---

```jsx
class Person {
  static create (name) {
    return new this(name)
  }
  constructor (name) {
    this.name = name
  }
}
const jn = Person.create('재난')
console.log(jn)
```

# 3. 클래스 상속

---

## 3-1. 소개

---

```jsx
function Square (width) {
  this.width = width
}

Square.prototype.getArea = function () {
  return this.width * (this.height || this.width)
}

function Rectangle (width, height) {
  Square.call(this, width)
  this.height = height
}

function F() { }

F.prototype = Square.prototype
Rectangle.prototype = new F()
Rectangle.prototype.constructor = Rectangle

const square = Square(3)
const rect = new Rectangle(3, 4)

console.log(rect.getArea())
console.log(rect instanceof Rectangle)
console.log(rect instanceof Square)
```

```jsx
class Square {
  constructor (width) {
    this.width = width
  }
  getArea () {
    return this.width * (this.height || this.width)
  }
}
class Rectangle extends Square {
  constructor (width, height) {
    super(width) //상위클래스의 생성자를 호출하는 함수. 오직 생성자 안에서만 호출가능.
    this.height = height
  }
}

const rect = new Rectangle(3, 4)
console.log(rect.getArea())
console.log(rect instanceof Rectangle)
console.log(rect instanceof Square)
```

## 3-2. 상세

---

### 3-2-1. class [서브클래스명] extends [슈퍼클래스명] { [서브클래스 본문] }

---

- 반드시 변수만 와야하는 것이 아니라, 클래스 식이 와도 된다.

```jsx
class Employee extends class Person {
  constructor (name) { this.name = name }
} {
  constructor (name, position) {
    super(name)
    this.position = position
  }
}
const jn = new Employee('잰남', 'worker')
```

```jsx
class Employee extends class {
  constructor (name) { this.name = name }
} {
  constructor (name, position) {
    super(name)
    this.position = position
  }
}
const jn = new Employee('잰남', 'worker')
console.log(jn.__proto__.__proto__.constructor.name)
```

- 함수도 상속 가능.

```jsx
function Person (name) { this.name = name }
class Employee extends Person {
  constructor (name, position) {
    super(name)
    this.position = position
  }
}
const jn = new Employee('잰남', 'worker')
```

```jsx
class Employee extends function (name) { this.name = name } {
  constructor (name, position) {
    super(name)
    this.position = position
  }
}
const jn = new Employee('잰남', 'worker')
```

- 내장 타입 상속 가능.

```jsx
class NewArray extends Array {
  toString () {
    return `[${super.toString()}]`
  }
}
const arr = new NewArray(1, 2, 3)
console.log(arr)
console.log(arr.toString())
```

### 3-2-2. super (내부 키워드로써, 허용된 동작 외엔 활용 불가)

---

1. constructor 내부에서
    1. 슈퍼클래스의 constructor를 호출하는 함수 개념.
    2. 서브클래스의 constructor 내부에서 `this`에 접근하려 할 때는 **가장 먼저** super 함수를 호출해야 함
    3. 서브클래스에서 constructor를 사용하지 않는다면 무관.
    ( 이 경우 상위클래스의 constructor만 실행된다. )
    내부에서 `this`에 접근하지 않는다면 무관.
2. 메소드 내부에서
    1. 슈퍼클래스의 프로토타입 객체 개념.
    2. 메소드 오버라이드 또는 상위 메소드 호출 목적으로 활용.

```jsx
class Rectangle {
  constructor (width, height) {
    this.width = width
    this.height = height
  }
  getArea () {
    return this.width * this.height
  }
}
class Square extends Rectangle {
  constructor (width) {
    console.log(super)
    super(width, width)
  }
  getArea () {
    console.log('get area of square.')
    console.dir(super)
    return super.getArea()
  }
}
const square = new Square(3)
console.log(square.getArea())
```

1. `new.target`을 활용한 abstract class 흉내

```jsx
class Shape {
  constructor () {
    if(new.target === Shape) {
      throw new Error('이 클래스는 직접 인스턴스화할 수 없는 추상클래스입니다.')
    }
  }
  getSize () {}
}
class Rectangle extends Shape {
  constructor (width, height) {
    super()
    this.width = width
    this.height = height
  }
  getSize () {
    return this.width * this.height
  }
}
const s = new Shape()
const r = new Rectangle(4, 5)
```