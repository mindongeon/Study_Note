# 1. 목차
- [1. 목차](#1-목차)
- [2. Function Interface](#2-function-interface)
  - [2.1. Predicate](#21-predicate)
  - [2.2. Consumer](#22-consumer)
  - [2.3. Supplier](#23-supplier)
  - [2.4. Function\<T,R\>](#24-functiontr)
  - [2.5. Comparator](#25-comparator)
  - [2.6. Runnable](#26-runnable)
  - [2.7. Callable](#27-callable)

# 2. Function Interface


```
💡 구현해야 할 추상 메소드가 하나만 정의된 인터페이스
```

## 2.1. Predicate
    1. T타입 인자를 받고 결과값 boolean 리턴
    2. 조건을 주고 그에 맞는 값에 따라 boolean 리턴됨
    3. test() 호출
    
    ```java
    Predicate<Integer> predicate = num -> num > 10;
    boolean result = predicate.test(100);
    System.out.println(result);
    ```
    
## 2.2. Consumer
    1. T타입 인자를 받고 void
    2. accept() 호출
    
    ```java
    Consumer<String> consumer = 
    	s -> System.out.println(s.toUpperCase());
    
    consumer.accept("hello world");
    ```
    
## 2.3. Supplier
    1. Supplier<T> 인자를 받지 않고 T 타입 객체 리턴
    2. get() 호출
    
    ```java
    Supplier<String> supplierEx = () -> "Hello";
    System.out.println(supplierEx.get()  + "aaaaaaaaaaa");
    
    :::
    
    Helloaaaaaaaaaaa
    ```
    
## 2.4. Function<T,R>
    1. T 타입 인자를 받고, R 타입 객체 리턴
    2. apply() 호출
    
    ```java
    Function<Integer, String> funcEx = (x) -> x + "ddd";
    System.out.println(funcEx.apply(3));
    ```
    
## 2.5. Comparator
    1. Collection 이나 배열 정렬
    2. 리턴 값은 음수,0,양수
    3. 음수가 리턴되면 오른쪽 인자가 아래로 내려감
    4. 익명 클래스로 생성
    
    ```java
    List<String> strings = new ArrayList<>();
    
    strings.add("1111111111111111111111111");
    strings.add("22222222222222222");
    strings.add("333333333333333333333333");
    strings.add("44444444444");
    strings.add("55555555");
    strings.add("66666666666666666666666666666666");
    
    // Sorting 하기 전 출력
    for (String str : strings) {
        System.out.println(str);
    }
    
    // 문자 길이로 sort (오름차순)
    Collections.sort(strings, new Comparator<String>() {
    
        @Override
        public int compare(String o1, String o2) {
            return o1.length() - o2.length();
        }
    });
    
    System.out.println();
    // sorting 후 출력
    for (String str : strings) {
        System.out.println(str);
    }
    ```
    
## 2.6. Runnable
    1. 인자를 받지 않고 리턴값도 없는 인터페이스
    2. run() 호출
## 2.7. Callable
    1. Callable<V>
    2. 인자를 받지 않고 특정 타입의 객체를 리턴
    3. call() 호출