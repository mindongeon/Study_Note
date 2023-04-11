```jsx
const f = function (x, y, z) {
  x = x ? x : 4
  y = y || 5
  if (!z) {
    z = 6
  }
  console.log(x, y, z)
}
f(1) //1 5 6

f(0, null) //4 5 6

////////////////////////////////////////////////
const f = function (x = 4, y = 5, z = 6) {
  console.log(x, y, z)
}

f(1) // 1 5 6

f(0, null) //0 null 6
```

---

```jsx
function a (a,b,c) {}
== var a, var b, var c

function a(a=1,b=2,c=3) {}
== let a !== undefined ? a : 1, let b, let c

```