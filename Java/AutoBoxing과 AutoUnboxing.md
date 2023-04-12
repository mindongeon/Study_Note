# AutoBoxing, AutoUnboxing

Boxing : 기본 타입의 데이터 → Wrapper 

Unboxing : Wrapper → 기본 타입 데이터

이것을 자동으로 해줌

```java
Integer num = new Integer(10); //Boxing
int n = num.intValue(); //Unboxing

Character ch = 'X'; // Character ch = new Character('X'); : AutoBoxing
char c = ch; // char c = ch.charValue(); : AutoUnboxing
```