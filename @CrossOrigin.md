### Same-origin policy(동일 출처 정책)
- 어떤 사이트에서 사용하는 api가 있는데 그 api는 비공개 api다.
- 하지만 다른 서비스에서 허락없이 무단으로 사용하는 것을 비공개 api는 원치 않을 것
- 그래서 protocol, domain, port가 모두 같아야 __동일 출처 정책__ 을 따른다고 볼 수 있다.

### CORS(Cross -origin resource sharing)
- 웹 페이지의 제한된 자원을 외부 도메인에서 접근을 허용해줌
- 서로 다른 도메인에서 리소스를 공유 가능케 바꿔줌

### 그래서 @CrossOrigin은?
- 스프링 4.2 부터 지원되는 @CrossOrigin은 cors를 설정할 수 있는 annotation이다.
- 이 annotation을 추가 시 "모든 도메인, 모든 요청방식"에 허용한다는 뜻
- 특정 도메인만 허용하고 싶을 때 속성 값으로 origin = "http:// {허용할 도메인 주소}"