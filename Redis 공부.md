# Redis

_Redis Command Collection_

|명령어|설명|종류|
|---|---|---|
|set {key} {value}|key value 저장| create|
|mset {key1} {value1} {key2} {value2}| 여러 개의 key value 저장|create|
|setex {key} {seconds} {value}| key, value 저장 seconds가 지나면 소멸| create|
|-|-|-|
|keys \*| 현재 저장된 모든 키 값 확인| select|
|get {key}|key에 해당되는 value 가져옴|select|
|mget {key1} {key2}|각 key에 해당되는 value를 한꺼번에 가져온다|select|
|ttl {key}|key의 만료시간을 초단위로 보여줌|select|
|pttl {key}|key의 만료시간을 밀리초단위로 보여줌|select|
|type {key}|key에 해당되는 value의 type 확인|select|
|-|-|-|
|del {key1} {key2}|key들을 삭제|delete|
|-|-|-|
|rename {key} {newkey}|key를 newkey로 바꿈|update|
|expire {key} {seconds}|key의 만료시간을 seconds로 설정|update|
|-|-|-|
|randomkey|랜덤한 key 출력|etc|
|ping|연결이 잘 되있는지 확인 pong출력|etc|
|dbsize|현재 사용중인 db의 크기(key의 개수) 리턴|etc|
|flushall|Redis server의 모든 데이터 삭제|etc|
|flushdb|현재 사용중인 DB의 모든 데이터 삭제|etc|

### flushall vs flushdb
- flushall은 모든 DB의 모든 데이터 삭제
- flushdb는 현재 DB의 모든 데이터 삭제


### Spring Boot Redis Configure
_Redis Dependency_
```java
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

_Redis yml config_
```yml
spring:
	redis:
		host: localhost
		port: 6379
```
---
RedisConnectionFactory
LettureConnectionFactory
RedisTemplate
RedisTemplate.set(Key/Value)Serializer() <- method
RedisTemplate.setConnectionFactory() <- method
redisConnectionFactory
ValueOperations
RedisTemplate.opsForValue() <- method

~~@PostConsturct ~~(기각 : bean 생성주기 기타등등 공부하고 오자)
- DI가 이루어진 후에 초기화를 수행함
- 다른 리소스에서 호출되지 않는다 해도 수행됨
- _쓰는이유_

@ConditionalOnMissingBean
