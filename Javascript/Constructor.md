```jsx
let Person = function(name) {
    this.name = name;
}

let p1 = new Person('a');
let p2 = new Person('b');

Person == p1.__proto__.constructor // true
Person == p1.__proto__ // false 
// p1.__proto__ = prototype
```