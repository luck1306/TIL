# 구 Swith, 현 Photo Diary

프로젝트 진행 중 만들어진 api를 확인하기 위해 postman을 이용해 spring application을 테스트하던 중 문제가 발생했다.\
바로 JwtFilter가 실행된 것이었다.\
이것이 문제가 된 이유는 JwtFilter는 요청을 받아서 요청받은 헤더(Authorization)에서 토큰을 꺼내온 후 그 토큰이 유효한지 검증 후 토큰의 Subject(사용자 아이디)를 SecurityContextHolder에 넣는다.\
하지만 회원가입은 로그인 하지 않은 사용자, 즉 토큰이 없는 상태의 사용자가 사용하는 기능이고 따라서 회원가입을 할 수 있는 상태에서는 토큰이 존재하지 않는다.\
나도 이를 인지하고 SecurityConfig에 회원가입 시 사용되는 경로와 http method에 permitAll(인증과정을 거치지 않음)을 사용했지만 어째선지 permitAll 기능이 정상적으로 작동되지 않아 토큰이 없는데 토큰을 받아야만 회원가입을 사용할 수 있는 문제에 봉착했다.\
처음에는 검색을 통해서 문제를 해결하고 싶었으나 처음보는 문제이기에 어떤 식으로 검색하는지도 몰랐고 유사한 다른 프로젝트에서 아무런 문제가 없었기에 나보다 spring boot를 오래한 친구에게 질문했다.

처음에는 SecurityConfig에서 csrf와 formlogin을 disable상태로 전환했지만 실패했다.\
그래서 직접적으로 문제가 보이는 JwtFilter를 확인하기 시작했다.\
우선 토큰이 Bearer로 시작하면 앞의 Bearer를 삭제하고 반환하는 함수이 resolveToken을 try catch문으로 감싸 만약 token에 값이 존재하지 않을 경우 null을 반환하게 했다.\
그리고 기존의 doFilterInternal method에서 resolveToken을 거친 토큰이 유효한지 검증한 후에 진행되는 조건을 resolveToken을 거친 토큰이 blank(null, "", " ")가 아닐 경우를 추가해서 SecurityContextHolder에 정보를 저장하는 작업을 진행시킨다.\
하지만 또 예외가 발생했는데 이번에는 validateToken method를 수정했다.\
예외는 토큰을 해석하는 과정에서 일어났다. 오류를 해석하는 method에 null값을 넣으면 예외가 발생된다.\
결국 그 친구가 해결한 방법은 SecurityContextHolder에 저장하지않고 바로 다음 filter로 넘기는 방식이 것 같다.(뇌피셜)
```java
if (!jwt.isBlank() && tokenProvider.validateToken(jwt))
```
위 조건문을 충족시켰을 때 SecurityContextHolder에 정보를 저장하는데 회원가입에는 jwt가 없기 때문에 SecurityContextHolder에 저장되지 않는다.\
그렇기에 validateToken에 의한 Exception이 발생되지 않는다. 

*근데 이 방법은 근본적인 문제 해결이 아닌것 같다.*