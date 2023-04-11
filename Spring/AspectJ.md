
# 1. 목차
- [1. 목차](#1-목차)
- [2. AspectJ](#2-aspectj)
  - [2.1. 장점](#21-장점)
  - [2.2. 라이브러리 의존성 설정](#22-라이브러리-의존성-설정)
  - [2.3. 메소드 임포트](#23-메소드-임포트)
  - [2.4. 테스트 대상 지정](#24-테스트-대상-지정)
  - [2.5. 문자열 테스트](#25-문자열-테스트)
  - [2.6. 숫자 테스트](#26-숫자-테스트)


# 2. AspectJ

## 2.1. 장점

---

- 메소드 체이닝 지원 ⇒ 가독성 좋은 테스트 코드 작성 가능
- 거의 모든 메소드 제공

## 2.2. 라이브러리 의존성 설정

---

- JAVA 8 이상은 3.x
- JAVA 7 이하는 2.x

## 2.3. 메소드 임포트

---

```java
import static org.assertj.core.api.Assertions.*;
```

## 2.4. 테스트 대상 지정

---

- `assertThat()`

```java
assertThat(Target).method1().method2().method3();
```

## 2.5. 문자열 테스트

---

```java
assertThat("Hello, world! Nice to meet you.")
	.isNotEmpty() // 비어있지 않고
	.contains("Nice") // "Nice"를 포함하고
	.contains("world") // "world"도 포함하고
	.doesNotContain("ZZZ") // "ZZZ"는 포함하지 않으며
	.startsWith("Hell") // "Hell"로 시작하고
	.endsWith("u.") // "u."로 끝나며
	.isEqualTo("Hello, world! Nice to meet you."); // "Hello, world! Nice to meet you."과 일치합니다.
```

## 2.6. 숫자 테스트

---

```java
assertThat(3.14d) // 주어진 3.14라는 숫자는
	.isPositive() // 양수이고
	.isGreaterThan(3) // 3보다 크며
	.isLessThan(4) // 4보다 작습니다
	.isEqualTo(3, offset(1d)) // 오프셋 1 기준으로 3과 같고
	.isEqualTo(3.1, offset(0.1d)) // 오프셋 0.1 기준으로 3.1과 같으며
	.isEqualTo(3.14); // 오프셋 없이는 3.14와 같습니다
```