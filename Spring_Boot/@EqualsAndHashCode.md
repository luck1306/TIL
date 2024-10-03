equals와 hashCode method 자동생성

- equlas : 두 객체의 내용이 같은지 비교하는 함수
- hashCode : 두 객체가 같은 객체인지 비교하는 함수

callSuper : equals와 hashCode method 자동생성 시 부모 클래스의 필드까지 감안할지의 여부를 결정할 수 있음
@EqualsAndHashCode(callSuper = true)