# JPA Study
### @Transactional (org.springframework.transaction.annotaion.Transactional)
- 간단하게 요약해서...
- Transaction은 Database, 영속성 컨텍스트와 밀접한 관련이 있다.
- 이 annotation이 붙은 method는 transaction을 지원하고 transaction은 모든 비즈니스 로직이 오류 없이 완벽하게 실행완료 되었을 때 해당 method가 비로소 Database의 값을 바꾸게한다.
- 명령어
    - commit(커밋) : 모든 작업을 정상적으로 처리하겠다고 확정하는 명령어다.
    - rollback(롤백) : 작업 중 문제가 발생 했을 때 작업 중인 내용의 변경사항을 작업 시작 전으로 되돌린다.
- @Transactional - Option
    - isolution : 격리 수준이라는 뜻을 가지고 있고, 일관성 없는 데이터를 허용하도록 하는 수준을 말한다.
        - DEFAULT : 기본, DB가 설정한 격리 수준 level을 따름
        - READ_UNCOMMITTED (level 0) : transaction에서 처리 중인, commit되지 않은 값을 다른 transaction이 읽는 것을 허용한다
        - READ_COMMITTED : commit되지 않은 값을 사용하는 것을 금지한다.(transaction이 커밋된 값만 읽는다.)\
    **자세한 내용 : https://taetaetae.github.io/2016/10/08/20161008/**
    - progagation : transaction 동작 중 다른 transaction을 호출할 때 우선 순위를 설정하는 option
    **자세한 내용 : https://taetaetae.github.io/2016/10/08/20161008/**\
    - readOnly : transaction을 읽기 전용으로 설정

@DynamicInsert
@TimeToLive
@QueryProjection
@MapsId
@ColumnDefault
@Digits
@Transient
@MappedSuperClass
@Indexed
@CreateDate
@LastModifiedDate
@EntityListeners
@PreAuthorize