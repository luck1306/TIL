
created : 23.11.21 16:44:55 ~ 17:35:55

org.hibernate.annotations.SQLDelete
DB부터 Entity나 Collection 행이 지워질 때 Hibernate에 의해서 생성되는 기본 SQL로 사용되는 특정한 사용자 지정 SQL DML.
주어진 SQL은 Hibernate 요구 조건에 부합하며 Hibernate가 요구하는 JDBC "?" Paramaters의 수를 정확히 가져야 한다. Entity에 버전화된 경우 기본 키 열은 버전 열 앞에 온다.

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@SqlDelete(sql = "UPDATE USER SET deleted_at = NOW() WHERE user_id = ?")
@Entity
public class User {
	...
}
```
