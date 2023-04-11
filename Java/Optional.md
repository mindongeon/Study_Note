# Optional

```
💡 Null Point Exception 방지
```

1. `static <T> Optional <T> empty()`
    
    ```java
    Optional<String> optional = Optional.empty();
    
    System.out.println(optional); // Optional.empty
    
    ```
    
2. `T get()`
    
    ```java
    // 연산이 끝난 후 객체 꺼냄
    // 비어있는 객체였다면 예외 발생
    
    public T get();
    
    Optional.of("test").get(); // test
    Optional.ofNullable(null).get(); // NoSuchElementException
    ```
    
3. `boolean isPresent()`
    
    ```java
    /*
    	boolean 타입
    	Optional 객체가 값을 가지고 있으면 true, 없으면 false
    */
    
    System.out.println(optional.isPresent()); false
    ```
    
4. `static <T> Optional<T> of(T value)`
    
    ```java
    /*
    	만약 어떤 데이터가 절대 null이 아니라면 Optional.of()로 생성 가능
    	만약 Optional.of()로 Null을 저장하려고 하면 NPE 발생
    */
    
    // Optional의 value가 절대 Null이 아닐경우
    Optional<String> optional = Optional.of("Hello");
    ```
    
5. `static <T> Optional<T> ofNullable(T value)`
    
    ```java
    /*
    	값이 Null 일수도, 아닐수도 있는 경우
    */
    
    Optional<String> optional = Optional.ofNullable(getName());
    // 값이 없다면 anonymous 를 리턴
    String name = optional.orElse("anonymous");
    
    ```
    
6. `T orElse(T other)`
    
    ```java
    private String getUserEmail() {
        System.out.println("getUserEmail() Called");
        return "abc@abc.com";
    }
    
    // 연산을 끝낸 후에도 optional 객체가 비어있으면 기본값으로 제공할 객체 지정
    // 파라미터로 값을 받음
    // 값이 미리 존재하는 경우에 사용
    public void findEmail() {
    	String userEmail = "Empty";
    	String result = Optional.ofNullable(userEmail).orElse(getUserEmail());
    
    	System.out.println(result);
    	::: getUserEmail() Called
    			Empty
    }
    ```
    
7. `T orElseGet(Supplier<? extends T> other)`
    
    ```java
    private String getUserEmail() {
        System.out.println("getUserEmail() Called");
        return "abc@abc.com";
    }
    // 연산을 끝낸 후에도 optional 객체가 비어 있으면 기본값으로 제공할
    // 공급자 함수를 지정 ( Supplier )
    // 파라미터로 함수형 인터페이스를 받음
    // 값이 미리 존재하지 않는 거의 대부분의 경우에 사용
    public void findEmail() {
    	String userEmail = "Empty";
    	String result = Optional.ofNullable(userEmail).orElseGet(this::getUserEmail());
    
    	System.out.println(result);
    	::: Empty
    }
    ```
    
8. `<X extends Throwable> T`
9. `orElseThrow(Supplier<? extends X> exceptionSupplier)`
    
    ```java
    // 연산을 끝낸 후에도 optional 객체가 비어있으면 예외 공급자 함수를 통해 예외 발생시킴
    public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier throws X;
    
    Optional.ofNullable("input").filter("test"::equals).orElseThrow(NoSuchElementException::new);
    
    // JAVA 8
    Optional.ofNullable(something.orElseThrow(NoSuchElementException::new);
    
    // JAVA 10
    public T orElseThrow();
    Optional.ofNullable(something).orElseThrow();
    ```