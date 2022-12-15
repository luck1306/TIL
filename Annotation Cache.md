#### @EnableCaching

- Cache 관련 annotation이 붙은 public method의 모든 스프링 빈을 스캔하고 intercept해서 캐싱 기능이 추가된 프록시 생성
```yml
spring:
	cache:
		type: redis
	redus:
		host: {$HOST}
		port: {$PORT}
```


- @Cacheable : 캐시 생성 수행
- @CacheEvict : 캐시 삭제 수행
- @CachePut : 캐시 수정 수행
- @Caching : method에 적용할 cache group 정의
- @CacheConfig : class level에서 캐시 관련 설정을 공유