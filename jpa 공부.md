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

### Enumerated
- EnumType.ORDINAL : enum에 정리된 순서대로 데이터베이스에 값이 저장됨
	- 예시 : enum이 아래와 같을 때 ADMIN은 0, USER는 1로 저장됨
```java
public enum ROLE() {
	ADMIN, USER
}
```
- EnumType.STRING : enum이름 그대로 데이터베이스에 저장됨
	- 예시 : enum이 위와 같을 때 ADMIN은 "ADMIN", USER는 "USER"로 저장된다.

@DynamicInsert : hibernate (나중에 공부)


@TimeToLive : redis

### @QueryProjection 
프로젝션은 select 절에 대상을 지정하는 것이다. (테이블에서 원하는 column만 뽑아서 조회하는 것)
_프로젝션이 하나일 때는 대상의 타입으로 반환되지만 복수의 프로젝션은  dto로 변환될 수 있다._
__sql (π)__ 
```java
@Entity
public class Member() {
	@Id
	private Long id;
	private String name;
	private int age;
}


@Data
public class MemberDto {
    private String name;
    private int age;
    
    pulbic MemberDto() {}
    @QueryProjection
    public MemberDto(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public void findDtoByQueryProjection() {
	queryFactory
		.select(new QMemberDto(member.name, member.age))
		.from(member)
		.fetch();
}
```
- dto의 생성자에 @QueryProjection annotation을 추가한다
- 기존의 dto를 기반으로 만들어진 qdto는 모든 layer에서 사용될 수 있으며 사용하는 것으로 querydsl에 의존적이게 됩니다.
- 사용 방법은 위의 findDtoByQueryProjection과 같이 사용합니다.


@MapsId : foreign key를 primary key로 사용할 때 사용


@ColumnDefault : 추가한 column에 기본 값을 설정한다.


@Digits : https://javacan.tistory.com/entry/Bean-Validation-2-Spring-5-valiidatiion (잘 모르겠다)


@Transient : https://gmoon92.github.io/jpa/2019/09/29/what-is-the-transient-annotation-used-for-in-jpa.html (정리 잘 됨) (객체에 임시로 어떤 값을 보관하고 싶을 때 사용)


@Indexed : (잘 모르겠다)


*refrence : https://wildeveloperetrain.tistory.com/76*
- @CreateDate : 생성된 시간 정보
- @MappedSuperClass : 반복되는 매핑정보( (예시) : id, name, createAt ...)를 부모클래스에 두고 속성만 상속하고 싶을 때 부모클래스에 추가하는 annotation
- @LastModifiedDate : 마지막으로 수정된 시간 정보
- @EntityListeners : jpa의 이벤트 발생 시 특정 로직을 실행시킬 수 있는 anntation


@PreAuthorize : (잘 모르겠다)

__Jpa Repository method 선언 방법__
- findBy : By 뒤의 조건으로 검색된 결과 하나를 반환함
- findAllBy : By 뒤의 조건으로 검색된 결과 __모두__ 반환함
	_By뒤에 Entity의 속성을 넣습니다(그 속성으로 원하는 튜플을 찾기 위해서)_ 
	- And : 속성을 추가할 때 사용합니다.