create 230906 15:06~ 15:52

### @OnDelete
RDBMS에서 DDL 사용시(schema 생성 시) on delete 제약조건 추가

_좀 더 공부_

### @DataJpaTest
- JPA test를 위한 annotation으로 실제 운영(실행)환경과 유사하게 테스트 환경을 구성해줍니다.
	_실제 DB가 아닌 in memory에 DB구성 및 test 종료시 설정 rollback_
- datasource 유효성 검증, 생성, 조회, 수정, 삭제 등의 기능 테스트
- @Transactional, @AutoConfigure(DataJpa / TestDatabase / TestEntityManager)등 JPA test를 위한 annotation 적용
- https://twpower.github.io/293-test-jpa-repository-using-data-jpa-test
- https://0soo.tistory.com/40