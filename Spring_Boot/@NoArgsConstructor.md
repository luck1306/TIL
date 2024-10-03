# @NoArgsConstructor
- (access = AccessLevel.PROTECTED) 사용이유
무분별한 객체 생성을 막기 위해서
    - accesslevel이 없거나 public일 경우
    생성된 객체의 필드를 모두 설정하지 않는 경우 불완전한 객체가 만들어지기 때문
    *여기서 부터는 뇌피셜 : acccesslevel.protected의 의미는 이 생성자의 접근제어자를 protected로 한다.*
- @NoArgsConstructor과 @Builder는 함께 사용불가하다.
@Builder는 해당 class에 생성자가 없을 때 생성자를 생성할 수 있다.
하지만 @NoArgsConstructor가 부착된 상태로 모든 필드를 생성자의 매개변수로 받기 위해서 class위에 위치시키면 @Builder는 생성자를 생성시키지 않는다.
따라서 class에 새로운 생성자를 생성하기 위한 목적으로 @Builder를 생성할 때 @NoArgsConstructor만 사용한다면 (다른 생성자 생성 annotation들 예 : @AllArgsConstructor, @RequiredArgsConstructor ) 예외가 발생한다.