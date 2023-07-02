
url의 구조
{프로토콜}://{서브 도메인}.{루트 도메인}.{TLD}
TLD(Top Level Domain) : 도메인 네임의 가장 마지막 부분

### 사용 이유
요청하는 서버의 ip주소를 외우기 힘들기 때문에 Domain name을 만들어 Domain name에 ip주소 하나 할당되는 방식으로 매번 ip주소를 작성해 접속함으로 생기는 문제점을 개선한다.

### 브라우저의 주소창에 www.naver.com 을 입력하면 발생하는 일
1. PC에 저장되어 있는 Local DNS Server(KT, SK, LG등의 통신사에 해당하는 DNS 서버)에 해당 도메인과 호스트 name의 ip주소를 보유하고 있는지 묻는다.
2. 만약 도메인과 호스트에 대한 정보가 존재한다면 바로 ip주소를 반환한다.
3. 만약 주소정보가 존재하지 않을 경우 Root DNS 서버 해당 도메인에 대한 정보를 요청한다.
4. Root DNS는 TLD에 대한 정보를 저장하고 있는 DNS서버의 위치를 Local DNS Server로 반환한다.
5. Local DNS Server는 응답받은 DNS에게 www.naver.com 의 정보를 요청한다.
6. Local DNS Server는 TLD DNS Server로 부터 www.naver,com에 대한 ip 주소를 받아 PC에 ip주소를 전송하게 된다.
7. **DNS Cache**는 매 요청시 마다 위의 동작을 반복하게 되면 비효율적이므로 PC내의 자주 사용하는 Domain Name 주소를 저장하는 공간입니다.

DNS register, DNS Registeregistration, DNS NS Rrecord, DNS Spoofing, DNS over TLS