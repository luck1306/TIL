
[create] 230913 09:35~11:36

Http 요청의 parameter를 지정한 객체에 대응시키는 annotation으로 Http 요청에 대응될 클래스는 모든 필드에 getter와 setter를 지녀야 합니다.
Restful한 서버 어플리케이션에서, 단순히 여러 parameter의 대응을 간소화 하기 위해 @ModelAttribute를 사용하지만 직접 페이지를 반환하는 서버 어플리케이션에서 보다 더 여러 기능이 추가됩니다. @ModelAttribute의 인자 값을 지정한 후 Model 객체가 view로 반환될 때 Model 객체에 지정한 인자 값으로 새 속성이 추가됩니다.

```java

@RequstMapping("/test/api")
@Controller
public class TestController {

	@GetMapping("/ma-test")
	public String testMaMapping(@ModelAttribute("MA") RequestVo requst) {
		return "jsonview";
	}

	@Getter
	@Setter
	public static class RequestVo {
		private String name;
		private String intro;
	}
}
```