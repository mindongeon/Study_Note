# Collections
```kotlin
fun main(args: Array<String>) {
    // To Array
    val words = "A long time ago in a galaxy far away".split(" ")
    // make mutable list
    val shortWords = mutableListOf<String>()
    // List<String>.getShortWordsTo()
    words.getShortWordsTo(shortWords, 3)
    println(shortWords)
}

fun List<String>.getShortWordsTo(shortWords: MutableList<String>, maxLength: Int) {

    // this = words
    this.filterTo(shortWords) { it.length <= maxLength }
    val articles = setOf("a", "A", "an", "An")
    // implicitly return ???
    shortWords -= articles
}
```

## List

### Immutable List
```kotlin
fun main() {
    val il = listOf<String>("a","b")
    println(il.size)
    println(il.get(1))
    println(il[0])
    println(il.indexOf("b"))
}
```

### Mutable List
```kotlin
fun main() {
    val ml = mutableListOf("a","b") 
    ml.remove("b")
    ml.add("f")
    println(ml)
    ml.addAll(listOf("g","h"))
    ml.removeAt(2)
}
```

## Set

### Immutable Set
```kotlin
val is1 = setOf<Int>(33,22,11)
println(is1)
println(is1.size)
println(is1.contains(11))
println(is1.isEmpty())
```

