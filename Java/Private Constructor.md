

# Private Constructor

- Static Factory Method를 사용하는 경우
    
    ```java
    class Animal {
        private Animal() {}
        public static Animal StaticFactoryMethod() {
            return new Animal();
        }
    }
    ```
    
- *utility* class일 경우
    
    ⇒ `static` 인 멤버들만 가지고 있음 ⇒ 객체가 생성될 일이 없음
    
- 클래스가 *Singleton*일 경우
    
    ⇒ 현재 실행되는 프로그램에 해당 클래스 변수는 하나만 존재할 경우
    

---

```
💡 생성자가 공통 코드를 가지고 있을 시 유용하게 사용할 수 있다.
```

- private 생성자는 다른 생성자가 this(…)을 사용해 호출 가능
- 초기화 코드를 private나 protected 생성자에 넣어주는 식으로 사용이 가능

```java
class Animal {
    private Animal(int x, int y) {
        System.out.println(x+y); //5
    }
    public Animal() {
        this(3,2);
    }
}
```