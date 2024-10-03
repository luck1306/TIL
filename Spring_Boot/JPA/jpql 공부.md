# JPQL 공부
*java persistence query language*

- jpa를 사용하면 엔티티 중심으로 개발함
- 검색을 할 때도 테이블 중심이 아닌 엔티티 중심 개발을 해야함
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능하다
- 따라서 어플리케이션에서 필요한 데이터만 DB에서 불러오기 위해서는 검색 조건이 포함된 SQL이 필요하다.
- JPQL은 엔티티를 대상으로 query를 질의하고
- SQL은 테이블을 대상으로 query를 질의한다

```java
String jpql = "select m from Member as m where m.username = 'kim'";

List<Member> resultList = em.createQuery(jpql, Member.class).getResultList();
```
