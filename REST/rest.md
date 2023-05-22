# 사용하는 이유
데이터를 text로 전송하기 때문에 환경에 제약되지 않는다.

# method

## Web에서의 method
- GET = SELECT 
- POST = CREATE

## Rest API 에서의 method

- GET = SELECT
- POST = CREATE
- PUT = UPDATE (column 하나씩 가능)
- PATCH = UPDATE (column을 통째로) => PUT으로 대체됨
- DELETE = DELETE

# 동기 vs 비동기

- 동기 : Block, 리턴값이 없음
- 비동기 : Non-Blcok, 리턴값이 있음

# 3가지
- Web에서 보낼 때 : axios
- URL, Method
- Rest -> Web : Java 객체 -> JSON
