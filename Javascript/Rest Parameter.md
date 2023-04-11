```jsx
function f (x, y) {
  var rest = Array.prototype.slice.call(arguments, 2)
  console.log(rest)
}
f(1, 2, true, null, undefined, 10)
// [ true, null, undefined, 10 ]
===============================================================
const f = function (x, y, ...rest) {
  console.log(rest)
}
f(1, 2, true, null, undefined, 10)
// [ true, null, undefined, 10 ]
```

---

### Getter / Setter

```jsx
const obj = {
    _a : 'a',
    get a () {return this._a;},
    set a (v) {return this._a = v; }
}
```