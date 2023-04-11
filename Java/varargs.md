# varargs

```
💡 가능한 가변인자를 사용한 메소드를 오버로딩하지 말자.
```


## 가변인자 사용법

---
키워드 "`…`" 을 사용한다.

```java
void hello(String ...str) {
	for(a:str) {
		System.out.println(a);
	}
}
```

가변인자는 내부적으로 배열을 생성해서 사용한다.

가변인자 외에도 다른 변수가 있다면 가변인자는 마지막에 사용한다.

```java
class Main {
    public static void main(String[] args) throws Exception {
        hello("-", "1", "2", "3");
    }

    static void hello(String s, String ...str) {
        for (String s1 : str) {
            System.out.print(s1+s);
        }
    }
}
```