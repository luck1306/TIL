2023-04-23 16:01
[update] 230912 10:11 (@DynamicUpdate 내용보강)
## @DynamicInsert
- table에 row를 삽입할 때, 이 엔티티의 준비된 sql 문에서 null이 아닌 칼럼만 참조된 동적 sql 생성을 사용해야 하는가?
### value [ATTRIBUTE]
- 이 엔티티에서 동적 삽입이 사용되야 하는가? true는 동적 삽입이 사용될 것 이다. 기본값은 true다.(일반적으로 이 Annotation을 사용한 시점부터 지금까지 사용자가 동적 삽입을 원하지 않는 한 사용되지 않는다.)

## @DynamicUpdate
- table의 row를 수정할 때, 이 엔티티는 준비된 sql 문(dirty check 후 생성된 update 문)으로부터 변경된 열만을 참조하여 동적인 sql 생성을 사용한다.
### value[ATTRIBUTE]
- 동적 수정 생성을 이 엔티티를 위해 사용되야 하는가? true는 sql 수정이 동적 생성될 것 이다. 기본 값은 true이다.(일반적으로 이 Annotation이 사용된 후 지금까지 사용자가 동적 생성을 원하지 않는 한 사용되지 않는다.)