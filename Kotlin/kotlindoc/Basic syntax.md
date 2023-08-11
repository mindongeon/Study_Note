# Basic syntax

## 패키지 정의와 imports

패키지 정의는 파일의 위에 위치해야 한다.
```kotlin
package my.demo

import kotlin.text.*

// ...
```

디렉토리와 패키지를 매치시킬 필요는 없다.

파일은 파일 시스템에 임의로 배치할 수 있다.

## 시작 위치
코틀린은 `main` 함수를 통해 시작한다.
```kotlin
fun main() {
    println("Hello World")
}
```

여러 `String` 인자를 `main`에서 받을 수 있다.
```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```

## 출력하기
`print`를 통해 문자열을 출력할 수 있다.
`println`을 사용하면 줄바꿈도 포함된다.

## 함수
두개의 `Int`인자를 받고 리턴값으로 `Int`를 리턴하는 함수
```kotlin
fun sum(a: Int, b: Int): Int {
    return a+b
}
```

함수도 표현식으로 사용가능하다. 리턴타입은 추론 가능하다.
```kotlin
fun sum(a: Int, b: Int) = a + b
```

리턴값이 없을 경우 `Unit`을 사용하지만 생략 가능하다.
```kotlin
fun printSum(a: Int, b: Int) {
    println("$a 와 $b의 합은 ${a+b}")
}
```

## 변수
`val`을 사용해 읽기만 가능한 변수를 선언할 수 있다. 값은 한번만 할당이 가능하다.
```kotlin
// 즉시 할당하기
val a: Int = 1
// Int 추론
val b = 2
// 할당하지 않을 경우, 타입 지정이 필요
val c: Int
// 지연된 할당
c = 3
```

재할당 가능한 변수는 `var`로 선언한다.
```kotlin
var x = 5 // Int 추론
x += 1
```

## class와 instance
`class`를 사용해 선언한다.
```kotlin
class Shape
```

class의 속성은 body 내부나 선언시 가능하다.
```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2
}
```

class 선언시에 매개변수가 있다면 기본 생성자는 자동 생성된다.
```kotlin
val rectangle = Rectangle(5.0, 2.0)
println("넓이는 ${rectangle.perimeter}")
```

class 간의 상속은 `:`으로 가능하다. class는 `final`이 기본이며 상속이 필요하다면 `open` 키워드를 사용한다.
```kotlin
open class Shape

class Rectangle(var height: Double, var length: Double): Shape() {
    var premimeter = (height + length ) * 2
}
```

## 주석
다른 언어와 같다.
```kotlin
// 한줄 주석

/*
* 여러줄 주석
* */
```

## 문자열 표현식
```kotlin
var a = 1
var s1 = "a is $a" // a is 1

a = 2
val s2 = "${s1.replace("is", "was")}, but now is $a"
// a was 1, but now is 2
```

## 조건식
```kotlin
fun maxOf(a: Int, b: Int): Int {
    if (a>b) {
        return a
    } else {
        return b
    }
}
```

`if`문은 표현식을 사용할 수 있다.
```kotlin
fun maxOf(a: Int, b:Int) = if(a>b) a else b
```

## for문
```kotlin
val items = listOf("apple", "banana", "kiwi")
for (item in iemts) {
    println(item)
}
```
이나
```kotlin
val items = listOf("apple", "banana", "kiwifruit")
for (index in items.indices) {
    println("item at $index is ${items[index]}")
}
```

## while문
```kotlin
val items = listOf("apple","banana","kiwi")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

## when 표현식
```kotlin
fun describe(obj: Any): String =
        when (obj) {
            1 -> "one"
            "hello" -> "greeting"
            is Long -> "Long"
            !is String -> "not a string"
            else -> "Unknown"
        }
```

## 범위
숫자가 범위내에 있는지 확인하려면 `in`연산자를 사용한다.
```kotlin
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```

범위 밖의 경우
```kotlin
val list = listOf("a","b","c")
if (-1 !in 0..list.lastIndex) {
    println("list size is out of valid list indices range")
}
```

반복문에서
```kotlin
for (x in 1..5) {
    print(x)
}
```
```kotlin
for (x in 1..10 step 2) {
    print(x)
}
println()
for (x in 9 downTo 0 step 3) {
    print(x)
}
```

## Collection
```kotlin
for (item in items) {
    println(item)
}
```

배열에서 `in`연산자 사용
```kotlin
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```

람다 표현식 사용
```kotlin
val fruits = listOf("banana","avocado","apple","kiwi")
fruits.filter { it.startsWith("a") }
        .sortedBy { it }
        .map { it.uppercase() }
        .forEach { println(it) }
```

## null 값과 null 체크
null 값을 허용할 경우 `?`를 사용한다.
```kotlin
fun parseInt(str: String): Int? {
    // ...
}
```

함수를 사용해 null 값 리턴
```kotlin
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)
    
    if (x != null && y != null)
        println(x*y)
    else println("$arg1이나 $arg2는 숫자가 아님")
}
```

## 타입 확인과 형변환
`is`연산자는 타입을 확인할 수 있다.
```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        return obj.length
    }
    
    return null
}
```

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null
    
    return obj.length
}
```

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj is String && obj.length > 0) {
        return obj.length
    }
    return null
}
```