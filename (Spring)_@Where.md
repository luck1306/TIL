create 23.11.21 17:44:45 ~ 18:00:00

엔티티나 collection으로 생성되는 SQL에 추가할 Native SQL의 작성된 제한을 지정함
예를 들어 @Where은 entity class 자기 스스로 소극적으로 지워진(column을 update하는 형식으로) 적 있는 entity 인스턴스를 숨기는데 사용될 수 있다.

```java

@Getter
@NoArgsConstructor(access = AccessLevel.PROPTECTED)
@Where(clause = "deleted_at == null")
@Entity
public class Document {
	...
}

...

{
	@OneToMany(mappedBy = "other")
	@Wehere(clause = "deleted_at == null")
	List<Document> documents;
}
```