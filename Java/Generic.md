# 1. Generic

- [1. Generic](#1-generic)
  - [1.1. 제네릭 사용하는 이유](#11-제네릭-사용하는-이유)
  - [1.2. 제네릭 사용하면 좋은 점](#12-제네릭-사용하면-좋은-점)
  - [1.3. class generic , method generic](#13-class-generic--method-generic)
  - [1.4. wildcard generic](#14-wildcard-generic)


## 1.1. 제네릭 사용하는 이유

1. 잘못된 타입이 사용하는 경우를 컴파일 과정에서 제거
2. 클래스, 인터페이스, 메소드를 정의할 때 타입을 파라미터로 사용할 수 있도록 함

## 1.2. 제네릭 사용하면 좋은 점

1. 컴파일 시 강한 타입 체크 가능
2. 타입 변환을 제거

## 1.3. class generic , method generic

```java
// 제네릭 클래스
class ClassName<E> {
	private E element; //제네릭 타입 변수

	void set(E element) { // 제네릭 파라미터 메소드
		 this.element = element;
	}

	E get() { // 제네릭 타입 반환 메소드
		return element;
	}
}

class Main {
	public static void main(String[] args) {
		
		ClassName<String> a = new ClassName<String>();
		ClassName<Integer> b = new ClassName<Integer>();

		a.set("10");
		b.set(10);

		sysout("a data : " + a.get()); ::: a data : 10
		sysout("a E Type : " + a.get().getClass().getName());
		::: a E type : java.lang.String
		
		sysout("b data : " + b.get()); ::: b data : 10
		sysout("b E Type : " + b.get().getClassName().getName());
		::: b E Type : java.lang.Integer
	}
}
```

```java
public <T> T genericMethod(T o) { // 제네릭 메소드
		...
}

[접근 제어자] <제네릭 타입> [반환 타입] [메소드명]([제네릭타입][파라미터]) {
		...
}
```

## 1.4. wildcard generic

```java
<K extends T> // T와 T의 자손 타입만 가능 (K는 들어오는 타입으로 지정됨)
<K super T> // T와 T의 조상 타입만 가능 (K는 들어오는 타입으로 지정됨)

<? extends T> // T와 T의 자손 타입만 가능
<? super T> // T와 T의 조상 타입만 가능
<?> // 모든 타입 가능. <? extends Object> 와 같은 의미

extends T : 상한경계
? super T : 하한경계

<?> : 와일드 카드
```
