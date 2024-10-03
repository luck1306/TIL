230825 17:08~

국제화를 제공하는 인터페이스
![[스크린샷 2023-08-25 오후 5.09.34.png]]
↑ 이런거 

메시지 설정 파일의 집합으로 국가 마다 웹 사이트의 언어 로컬라이징을 진행함으로 지역의 주 사용 언어에 맞는 메시지 제공 가능

### 대표적인 구현체
- org.springframework.context.support.ResourceBundleMessageSource
	- 기본 구현체
- org.springframework.context.support.ReloadableResourceBundleMessageSource
	- 필드 설정으로 메시지 cache 시간 지정 가능(캐시 시간 단위로 메시지 파일을 reload 함)
- org.springframework.context.support.StaticMessageSource
	- MessageSource interface의 간단한 구현체
	- 기본적인 국제화 지원

```properties
# message.properties
message.hello=HI
```

```java
@Component
public class Test {
	@AuthWired
	private MessageSource messageSource; // ResourceBundleMessageSource

	public static void run(String[] args) {
		messageSource.getMessage("message.hello"); //properties 파일에서 원하는 값에 대한 키
	}
}
```

```terminal
HI
```
