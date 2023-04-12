```
💡 WAS가 뜰때 한번만 사용
```

- `@PostConstruct`는 WAS가 뜰 때 bean 생성 후 한번만 실행
    
    ⇒ 초기화시 유용
    
- `@PreDestroy`는 컨테이너에서 bean을 제거하기 전에 실행
    
    ⇒ 자원 반환 등 종료 처리 시 유용
    