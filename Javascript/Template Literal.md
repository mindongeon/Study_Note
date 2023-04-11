1. backtick (`)
2. multi-line
3. string interpolation = ``${ }``

```jsx
const a = 10
const b = 20

const sum = ` a + b 
                    = ${a+b}`

console.log(sum);
```

- multi-line 시 주의
    
    ```jsx
    const a = 10;
    const b = 20;
    
    console.log(`${a} +
         ${b} = 
         ${ a+b }`);
    
    10 + 
         20 =  
         30
    ```
    
    ```jsx
    const a = 10;
    const b = 20;
    
    console.log(`${a} \n` +
         `${b} = \n` +
         `${ a+b }`);
    
    10  
    20 =  
    30
    ```
    

---

template language / template engine / template library

→ Model의 값을 가지고 html을 만듬

---

Array.prototype.forEach(callback[, thisArg]) → [ ] 안의 요소는 필수 아님

---

### Template tag function

```jsx
const tag = function (strs, arg1, arg2) {
    return {strs: strs, args: [arg1, arg2]}
  }
const res = tag `순서가 ${1}이렇게 ${2}`
console.log(res)
//{ strs: [ '순서가 ', '이렇게 ', '' ], args: [ 1, 2 ] }

const res1 = tag `123 333 ${1}5555 ${`hello`}`
console.log(res1)
//{ strs: [ '123 333 ', '5555 ', '' ], args: [ 1, 'hello' ] }
```

```jsx
const tags = function (strings, ...expressions) {
    console.log(strings, expressions)
  }
const a = 'iu', b = 'Friday'
const str1 = tags `hello, ${b} , aaaa ${a} , accccc ${123}`
// [ 'hello, ', ' , aaaa ', ' , accccc ', '' ] [ 'Friday', 'iu', 123 ]
```

1. expression의 수는 언제나 string의 수보다 하나 적음
2. 이를 이용하면 strings 또는 expressions 중 하나를 순회하여 별도의 처리가 가능