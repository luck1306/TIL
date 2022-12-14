# Abstract Class

__추상 메서드(abstract method)__
- 자식 클래스에서 반드시 override받아야 하는 method를 의미
- 선언부만 존재한다.

__추상 클래스(abstract class)__
- 하나 이상의 추상 메서드를 포함하는 클래스
- 이 추상 클래스를 상속받는 모든 class는 추상 클래스에 선언되어 있는 추상 메서드를 구현해야한다.
- 추상 클래스는 인스턴스화가 안된다.( new abstractClass() (X) )
- 추상 클래스를 상속받은 자식 클래스는 추상 클래스의 모든 추상 메서드를 override해야 자식 클래스의 인스턴스를 생성할 수 있다.

__사용 이유__
- 통일된 코드 규격화 가능

# Interface
- 인터페이스를 구현한 클래스 역시 인터페이스에 선언되어 있는 메서드를 반드시 구현해야 한다.
- 추상 클래스는 추상 클래스에 선언된 멤버변수 수정이 가능하지만 인터페이스는 불가
- 다중 상속이 가능하다
- 추상 클래스와 마찬가지로 인터페이스에 선언된 모든 메서드들은 추상 메서드와 같이 상속받은 class에서 정의해야 한다.

__사용 이유__
- 상속받은 모든 클래스에서 인터페이스가 가진 메서드를 구현하고 사용한다는 보장이 있을 때
- 다중상속이 필요할 때