# JPA

## JPA vs myBatis
myBatis의 Mapper = JPA의 영속성 Context

## Spring Data
webMVC = Servlet을 사용하기 쉽게 spring에서 제공
spring data = JPA를 사용하기 쉽게 spring에서 제공

## hibernate
JPA 스펙을 준수해서 구현한 친구

## h2
- 실습용 DB
- 가벼움
- 웹용 쿼리툴 제공
- mySQL, Oracle 데이터베이스 시뮬레이션 기능
- 시퀀스, Auto Increment 기능 지원

## 데이터베이스 방언
- JPA는 특정 데이터베이스 종속되지 않는다.
- 각각의 데이터베이스가 제공하는 SQL 문법과 함수는 조금씩 다르다.
  - 가변 문자 : 
    - MySQL : `VARCHAR` 
    - Oracle : `VARCHAR2`
  - 문자열을 자르는 함수
    - SQL 표준 : `SUBSTRING()`
    - Oracle : `SUBSTR()`
  - 페이징
    - MySQL : `LIMIT`
    - Oracle : `ROWNUM`
  - 방언 : SQL 표준을 지키지 않는 특정 데이터베이스만의 고유한 기능

