# Process, Thread

## 프로세스란
---
실행 중인 프로그램을 말함

## 스레드란

---

1. 프로세스 내에서 각각의 Stack 만 따로 할당 Code, Data, Heap 영역은 공유
2. 한 프로세스 내에서 동작되는 여러 실행의 흐름
3. 같은 프로세스 내의 여러 쓰레드는 같은 힙 공간을 공유

## 스레드와 프로세스의 차이

---

프로세스 : 운영체제로부터 작업을 할당받는 작업의 단위

쓰레드 : 프로세스가 할당받은 자원을 이용하는 실행의 단위

## 스레드를 구현하는 방법

---

1. Thread 클래스를 상속
2. Runnable 인터페이스 구현

→ Thread 클래스를 상속하면 다른 클래스 상속이 불가해

일반적으로 Runnable 인터페이스를 구현함