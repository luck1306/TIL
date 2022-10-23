
### ResponseEntity
- 배경 지식
spring framwork에는 HttpHeader와 HttpBody를 포함하는 HttpEntity라는 클래스가 있다.
HttpEntity 클래스를 상속받아 구현한 클래스가 RequestEntity, ResponseEntity다.

- 그래서 ResponseEntity는
사용자의 HttpRequest에 대한 응답 데이터를 포함하는 클래스
HttpStatus, HttpHeaders, HttpBody를 포함함
그냥 응답할 때 쓰는 Entity 같다.

자세한 내용 : https://devlog-wjdrbs96.tistory.com/182

### Optional

- 배경 지식
null이 Runtime에서 NullPointerException을 발생시키거나 NullPointerException의 문제를 핸들링 하기 위해 체크 로직으로 코드 가독성, 유지 보수성이 어려워 진다.
함수형 프로그래밍 언어에서는 위 문제를 "존재할 지 안 할지 모르는 값"이라는 타입과 그 타입을 제어할 수 있는 여러 API를 통해 간접적으로 그 값에 접근하는 방식으로 이 문제를 해결했다.

- Optional이란?
"존재할 지 안 할지 모르는 객체", 즉 "null이 될 수 있는 객체"를 감싸는 Wrapper class 이다.
Optional 객체는 null을 직접 다루지 않아도 된다.
수고롭게 null check를 직접 하지 않아도 된다. (확인 하는 메소드 있음)

- Optional method

ofNullable(value) : value 값을 생성할 Optional객체에 저장시켜 생성한다.

get() : Optional 객체의 값을 반환함, 비어있을 때는 NoSearchElementException 발생

orElse(T other) : 객체의 값을 반환함, 비어 있을 때는 받아온 인자 반환

orElseGet(Supplier< ? extends T > other) : 객체의 값을 반환함, 비어 있을 경우 받아온 함수형 인자로 생성된 객체 반환

orElseThrow(Supplier< ? extends T > exceptionSupplier) : 객체의 값을 반환함, 비어 있는 경우 받아온 함수형 인자로 생성된 예외를 throw한다

isPresent() : 값이 있다면 true, 값이 없다면 false를 반환함

자세한 내용 : https://www.daleseo.com/java8-optional-effective
**1번, 2번, 3번 포스팅 모두 볼 것**