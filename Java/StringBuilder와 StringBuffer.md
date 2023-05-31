# String, StringBuilder, StringBuffer

## String 과 StringBuilder의 차이점

---

1. String 은 불변성을 가지고 있음
2. 때문에 자주 읽어들이는 경우 String 을 사용
3. 하지만 추가,수정,삭제 등의 연산이 많아지는 경우
4. 메모리가 부족해짐
5. StringBuffer, StringBuilder는 가변성을 가짐

## StringBuffer 와 StringBuilder 의 차이점

---

1. 동기화의 유무
2. StringBuffer는 멀티쓰레드 환경에서 안전함
3. StringBuilder는 지원하지 않음
4. 그래서 단인쓰레드에서 성능이 더 뛰어남

- String : 문자열 연산이 적고 멀티쓰레드 환경일 경우
- StringBuffer : 문자열 연산이 많고 멀티쓰레드 환경일 경우
- StringBuilder : 문자열 연산이 많고 싱글쓰레드거나 동기화를 고려하지 않아도 될 경우

# 많은 양의 문자열 연산 시 String을 사용하지 말아야하는 이유

`String은 immutable 객체 (생성 후 변경 불가한 객체)이기 때문이다.`
그래서 연산할 때마다 새로운 String 클래스 객체가 만들어지고 이전 객체는
필요 없는 쓰레기 값이 되어 GC 대상이 된다. 이런 작업이 반복 수행되면서
메모리를 많이 사용하게 되고, 응답속도에도 많은 영향을 미치게 된다.

반면 `StringBuffer`나 `StringBuilder`는 새로운 객체를 생성하지 않고,
기존에 있는 객체의 크기를 증가시키면서 값을 더한다.