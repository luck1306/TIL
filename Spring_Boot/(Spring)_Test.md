
create 230907 10:17~17:00 (Test의 종류, Unit Test)

### Test의 종류
- unit test : 기능 하나 단위의 테스트
- integration test : 어플리케이션 전체 단위의 테스트

### Unit Test(단위 테스트) 
	unit test에 대한 대부분의 뭐시기는 다음 글의 내용을 참조한 것입니다. 
	( https://mangkyu.tistory.com/143 )
	하나의 모듈을 기준으로 독립적으로 진행되는 가장 작은 단위의 테스트

<details>
	<summary>모듈이란?</summary>
	<div>
		unit test에서 모듈은 하나의 기능이나 method로 해석할 수 있습니다.
	</div>
</details>

- "테스트 코드"라 불리는 작업의 통상적 의미(=unit test)
- 개발한 기능의(method) 문제 여부 확인 가능
- 통합 테스트에 비해 시간 & 비용의 단축이 크다.

__단점__
- 대부분의 기능은 기능 처리를 위해 다른 객체들과 상호작용합니다.
- 하나의 모듈에 대한 독립적 테스트이므로 가짜 객체(Mock Object)를 주입해야 함
- 가짜 객체에 어떤 결과를 입력해 테스트 성공 시 반환할 결과를 준비하는 행위를 **STUB**이라 함

__지향해야 할 단위 테스트__
- 추가된 요구사항으로 변경된 코드도 검증 가능하도록 테스트 코드를 작성
- 기존 코드의 변경으로 불가피하게 테스트 코드 또한 변경될 경우를 염려해 테스트 코드의 가독성을 신경써서 작성
	- 각각의 테스트 함수에 assert 사용 최소화
	- 각각의 테스트 함수는 1가지 개념만 테스트
- **FIRST** 규칙 준수
	- Fast : 테스트는 빠르게 동작하여 자주 작동할 수 있어야 한다.
	- Isolated / Independent : 각각의 테스트는 독립적으로 진행 하며 서로 의존을 금한다.
	- Repeatable : 테스트는 반복가능하고 항등해야한다. 테스트에 대한 결과 값은 다른 환경에서 실행을 기반으로 변경되서는 안된다.
	- Self-Validating : 테스트는 성공 혹은 실패(boolean 값)으로 결과를 도출해 자체적으로 검증되야 한다.
	- Timely / Thorough (ref : https://dzone.com/articles/first-principles-solid-rules-for-tests ) :
		- "Robert. C. Martin"를 포함해서 TDD를 믿는 사람들이 이 편지(?)를 Timely(시기 적절함)라고 설명하고 테스트를 제때 작성해야 한다고 명시합니다.  TDD의 경우 production code를 작성하는 도중을 의미합니다. 이건 더 많은 테스트 가능하고 보다 깨끗한 코드를 작성하도록 강제하며 추가로, 그들은 production code 후에 test를 작성하는 것이 더 더럽고 테스트할 수 없는 코드로 이끌 수도 있다고 주장했습니다. 그게 unit test를 하는동안 몇몇의 이점을 가져다 줄 수 있지만 다른 유형의 테스트에서 좋은 방법이 아닐 수 있습니다.
		- 이게 내가  개인적으로 이 편지를 Thorough(철저함)라고 설명하기를 선호하는 이유 중 하나입니다. 우리가 method를 테스트할 때 우리는 올바른 경로의 예외 뿐만 아니라 올바르지 않은 경로와 발생 가능한 오류 또한 처리해야함을 의미합니다. 그건 우리의 테스트를 더 의미있고, 더 완성되고, 우리에게 어려운 코드 부분을 더 잘 이해하도록 돕습니다. 자연스럽게 이 두 설명을 따르는것과 그 장점을 합치는 것은 어려운 일이 아닙니다.

```java

@ExtendWith(MockitoExtension.class)
class ServiceTest {
	//
}
```

### Integration Test
