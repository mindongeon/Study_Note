# 1. 목차

- [1. 목차](#1-목차)
- [2. Stream](#2-stream)
- [3. Stream 이란](#3-stream-이란)
- [4. 생성](#4-생성)
  - [4.1. 배열 / 컬렉션 / 빈 스트림](#41-배열--컬렉션--빈-스트림)
  - [4.2. Stream.builder() / Stream.generate() / Stream.iterate()](#42-streambuilder--streamgenerate--streamiterate)
  - [4.3. 기본 타입형 / String / 파일 스트림](#43-기본-타입형--string--파일-스트림)
  - [4.4. 병렬 스트림 / 스트림 연결하기](#44-병렬-스트림--스트림-연결하기)
- [5. 중간 연산](#5-중간-연산)
  - [5.1. Filtering](#51-filtering)
  - [5.2. Mapping](#52-mapping)
  - [5.3. Sorting](#53-sorting)
  - [5.4. Iterating](#54-iterating)
- [6. 최종 연산](#6-최종-연산)
  - [6.1. Calcurating](#61-calcurating)
  - [6.2. Reduction](#62-reduction)
  - [6.3. Collecting](#63-collecting)
  - [6.4. Matching](#64-matching)
  - [6.5. Iterating](#65-iterating)


# 2. Stream

```
💡 **데이터를 추상화하여 다루므로, 다양한 방식으로 저장된 데이터를 읽고 쓰기 위한 공통된 방법을 제공**
```

# 3. Stream 이란


1. 자바 8에서 추가됨
2. 데이터의 흐름
3. 배열과 컬렉션을 함수형으로 처리 가능
4. 병렬처리 가능

# 4. 생성


## 4.1. 배열 / 컬렉션 / 빈 스트림

---

```java
// 배열
String[] arr = new String[]{"a","b","c"};
Stream<String> stream = Arrays.stream(arr);
Stream<String> streamArr = Arrays.stream(arr,1,3);
::: 1~2 요소 [b, c]

//컬렉션
public interface Collection<E> extends Iterable<E> {
	default Stream<E> stream() {
		return StreamSupport.stream(spliterator(), false);
	}
	// ...
}

List<String> list = Arrays.asList("a","b","c");
Stream<String> stream = list.stream();
// 병렬 스트림
Stream<String> parallelStream = list.parallelStream();

// 빈 스트림 :: Null 대신 사용할 수 있음
public Stream<String> streamOf(List<String> list) {
	return list == null || list.isEmpty()
		? Stream.empty()
		: list.stream();
}

```

## 4.2. Stream.builder() / Stream.generate() / Stream.iterate()

---

```java
//Stream.builder()
Stream<String> builderStream = 
	Stream.<String>builder()
	.add("a").add("b").add("c").build();

::: [a, b, c]

//Stream.generate()
//Supplier<T> 에 해당하는 람다로 값을 넣을 수 있음
public static<T> stream<T> generate(Supplier<T> s) {
	...
}

Stream<String> generatedStream = 
	Stream.generate(() -> "gen").limit(5);

::: [el, el, el, el, el]

//Stream.iterate()
Stream<Integer> iteratedStream = 
	Stream.iterate(30, n -> n+2).limit(5);

::: [30, 32, 34, 36, 38]
```

## 4.3. 기본 타입형 / String / 파일 스트림

---

```java
// 기본 타입형
IntStream intStream = IntStream.range(1,5);
::: [1,2,3,4]
LongStream longStream = LongStream.rangeClosed(1,5);
::: [1,2,3,4,5]

//문자열
IntStream charStream = "Stream".chars();
::: [83, 116, 114, 101, 97, 109]
Stream<String> stringStream = 
	Pattern.compile(", ").splitAsStream("aaa, bbb, ccc");
::: [aaa, bbb, ccc]

//파일
Stream<String> lineStream =
	Files.lines(Paths.get("file.txt"),
		Charset.forName("UTF-8"));
```

## 4.4. 병렬 스트림 / 스트림 연결하기

---

```java
// 병렬 스트림 Parallel Stream
Stream<Product> paralleStream = 
	productList.parallelStream();
// 병렬 여부 확인
boolean isParallel = parallelStream.isParallel();
// 병렬 처리 예시
boolean isMany = 
	parallelStream.map(product -> product.getAmount()*10)
	.anyMatch(amount -> amount > 200);

//배열을 이용해 생성하기
Arrays.stream(arr).parallel();

//컬렉션, 배열 외의 경우
IntStream intStream = IntStream.range(1, 150).parallel();
boolean isParallel = intStream.isParallel();

----------------------------
// 연결하기
// Stream.concat 을 이용
Stream<String> stream1 = Stream.of("a","b","c");
Stream<String> stream2 = Stream.of("d","e","f");
Stream<String> concat = Stream.concat(stream1, stream2);
::: [a,b,c,d,e,f]
```

# 5. 중간 연산


```java
List<String> names = Arrays.asList("a","b","c");
```

## 5.1. Filtering

---

```java
Stream<T> filter(Predicate<? super T> predicate);

Steam<String> stream = 
	names.stream().filter(name -> name.contains("a");

::: [a]
```

## 5.2. Mapping

---

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);

Stream<String> stream = names.stream().map(String::toUppderCase);

::: [A,B,C]
```

## 5.3. Sorting

---

```java
Stream<T> sorted();
Stream<T> sorted(Comparator<? super T> comparator);

IntStream.of(14,11,20,39,23).sorted().boxed.collect(Collectors.toList());
::: [11,14,20,23,39]
```

## 5.4. Iterating

---

- peek : 스트림 내 요소들을 각각을 대상으로 특정 연산을 수행하는 메소드
Counsumer 를 인자로 받음

```java
Stream<T> peek(Consumer<? super T> action);

int sum = IntStream.of(1,3,5,7,9).peek(System.out::println).sum();
```

# 6. 최종 연산

## 6.1. Calcurating

---

```java
long count = IntStream.of(1,3,5,7,9).count();
long sum = LongStream.of(1,3,5,7,9).sum();

// 스트림이 비어있는 경우 count와 sum 은 0을 출력
// 평균, 최소, 최대의 경우 표현 불가 -> Optional 사용
OptionalInt min = IntStream.of(1,3,5,7,9).min();
OptionalInt max = IntStream.of(1,3,5,7,9).max();

// 스트림에서 바로 ifPresent 메소드를 이용해 Optional 처리 가능
DoubleStream.of(1.1.,2.2,3.3,4.4,.5.5).average().ifPresent(System.out::println);
```

## 6.2. Reduction

---

스트림에 있는 여러 요소의 총 합을 나타낼 수 있음
3가지 파라미터를 가지고 있음

1. accuulator : 각 요소를 처리하는 계산 로직. 
각 요소가 올 때마다 중간 결과를 생성
2. idenity : 계산을 위한 초기값으로 스트림이 비어서 계산할 내용이 없어도 이 값은 리턴함
3. combiner : 병렬 스트림에서 나눠 계산한 결과를 하나로 합치는 동작을 하는 로직

```java
// 1개 ( accumulator )
Optional<T> reduce(BinaryOperator<T> accumulator);

// 2개 ( idenity )
T reduce(T identity, BinaryOperator<T> accumulator);

// 3개 ( combiner )
<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator,
	BinaryOperator<U> combiner);

// BinaryOperator<T> 는 같은 타입의 인자 두 개를 받아 같은 타입의 결과를 반환
// 인자 1개
OptionalInt reduced = IntStream.range(1,4).reduce(Integer::sum);
// 인자 2개
int reduceTwoParams = 
	IntStream.range(1,4).reduce(10, Integer::sum);
// 인자 3개
// Combiner 는 병렬에서만 실행됨.
Integer reducedParams = 
	Stream.of(1,2,3).reduce(10, Integer::sum,(a,b)-> {
		System.out.println("combiner");
		return a + b;
	}
// 병렬 처리된 Combiner
Integer reduceParallel = Arrays.asList(1,2,3).parallelStream()
	.reduce(10, Integer::sum, (a,b)->{
		System.out.println("combiner");
		return a+b;
	}
```

## 6.3. Collecting

---

- Collector 타입의 인자를 받아 처리

```java
List<Product> productList =
                Arrays.asList(new Product(23, "a"),
                        new Product(14, "b"),
                        new Product(13, "c"),
                        new Product(23, "d"),
                        new Product(13, "e"));

// Collectors.toList()
// .collect() : Stream의 데이터를 변형 등의 처리를 하고 원하는 자료형으로 변환
// Collectors.toList() : list 형태
List<String> collectorCollection = 
	productList.stream().map(Product::getName).collect(Collectors.toList());
::: [a,b,c,d,e]

// Collectors.joining()
// 스트림에서 작업한 결과를 하나의 스트링으로 이어 붙이기
String listToString = productList.stream().map(Product::getName).collect(Collectors.joining());
//3개의 인자를 받을 수 있음
/*
	1. delimiter : 각 요소 중간에 들어가 요소를 구분시켜주는 구분자
	2. prefix : 결과 맨 앞에 붙는 문자
	3. suffix : 결과 맨 뒤에 붙는 문자
*/
String listToString = 
	productList.stream().map(Product::getName)
	.collect(Collectors.joining(", ","<",">"));
::: <a, b, c, d, e>

// Collectors.averageingInt()
// 숫자 값의 평균
Double averageAmount = 
	productList.stream().collect(Collectors.averagingInt(Product::getAmount));

// Collectors.summingInt()
// 숫자의 합
Integer summingAmount =
	productList.stream().collect(Collectors.summingInt(Product::getAmount));

// IntStream 으로 바꿔주는 mapToInt 메소드 사용
Integer summingAmount = productList.stream().mapToInt(Product::getAmount).sum();

// Collecotrs.summarizingInt()
// 합계, 평균 한번에
IntSummaryStatistics statistics = 
	productList.stream().collect(Collectors.summarizingInt(Product::getAmount));
:::IntSummaryStatistics{count=5, sum=86, min=13, average=17.200000, max=23}

// Collectors.groupingBy()
// 함수형 인터페이스 Funcation 을 인자로 받음
// 특정 조건으로 요소들을 그룹지을 수 있음
Map<Integer, List<Product>> collectorMapOfLists = 
	productList.stream()
	.collect(Collectors.groupingBy(Product::getAmount));
// 결과는 Map 으로 나옴

// Collectors.partitioningBy()
// Predicate 를 인자로 받음
// boolean 리턴
Map<Boolean, List<Product>> mapParritioned =
	productList.stream()
	.collect(Collectors.partitioningBy(el -> el.getAmount > 15));
::: true 와 false 두 가지로 나눔

// Collectors.collectingAndThen()
// 특정 타입으로 결과를 collect 한 이후에 추가 작업을 할 경우 사용
// finish : collect 한 후 실행할 작업
public static<T,A,R,RR> Collector<T,A,RR> collectingAndthen(
	Collector<T,A,R> downstream,
	Function<R, RR> fininsher) { ... }

// 결과 -> Set -> 수정 불가 Set
Set<Product> unmodifiableSet =
	productList.stream()
	.collect(Collectors.collectingAndThen(Collectors.toSet(),
																				Collections::unmodifiableSet));

// Collector.of
// 직접 collector 만들기
public static<T, R> Collector<T, R, R> of (
	Supplier<R> supplier, // new collector 새엉
	BiConsumer<R, T> accumulator, // 두 값을 가지고 계산
	BinaryOperator<R> combiner, // 계산한 결과를 수집하는 함수
	Characteristics... characteristics) {...}

// collector 를 생성하는 supplier 에 LinkedList 생성자 넘김
// accumulator에는 리스트에 추가하는 add 메소드 넘김
// 스트림의 각 요소에 대해서 LinkedList를 만들고 요소 추가
// combiner를 이용해 결과를 조합, 생성된 리스트들을 하나의 리스트로 합침
Collector<Prodcuct, ?, LinkedList<Product>> toLinkedList =
	Collector.of(LinkedList::new,
								LinkedList::add,
								(first, second) -> {
									first.addAll(second);
									return first;
								}

```

## 6.4. Matching

---

```java
// Predicate 를 받아 해당 조건을 만족하는 요소가 있는지 체크한 결과 리턴
/*
	- 하나라도 조건을 만족하는 요소가 있는지 ( anyMatch )
	- 모두 조건을 만족하는지 ( allMatch )
	- 모두 조건을 만족하지않는지 ( noneMatch )
*/

boolean anyMatch(Predicate<? super T> predicate);
boolean allMatch(Predicate<? super T> predicate);
boolean noneMatch(predicate<? super T> predicate);

////// ex ///////
List<String> names = Arrays.asList("a","b","c");

boolean anyMatch = names.stream().anyMatch(name -> name.contain("a"));
::: true
boolean allMatch = names.stream().allMatch(name -> name.length() < 2));
::: true
boolean noneMatch = nemes.stream().noneMatch(name -> name.endsWith("s"));
::: true
```

## 6.5. Iterating

---

```java
// foreach : 요소를 돌면서 실행되는 최종 작업.
// 보통 System.out.println 메소드를 넘겨 결과 출력으로 사용
names.stream().forEach(System.out::println);
```