# instanceOf
---
- 객체의 타입을 확인하는 연산자
- 형변환의 여부를 boolean타입으로 리턴
- 상속 관계에서의 부모/자식 확인
  
```Java
//Object instanceof Class

package com.test;

public class TestMain {
    public static void main(String[] args) {
        Animal animalObj = new Animal();
        System.out.println(animalObj instanceof Animal); //true
    }
}
class Animal {}
```