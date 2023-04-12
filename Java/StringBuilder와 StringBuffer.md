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