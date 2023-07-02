2023-04-23 16:01
### Signature Algorithm Key

원본 및 알고리즘 종류 : https://github.com/jwtk/jjwt#jws-key

JJWT는 선택한 알고리즘에 요구사항에 맞는 키를 사용해야한다. 알고리즘의 요구사항에 맞지 않는 키로 암호화 진행 시 JJWT가 예외를 발생시킨다.
이는 알고리즘의 필수 요구사항 미준수 시 보안 모델이 무너져 보안설정을 하지 않은것과 다른 바 없는 상태가 될 수 있기 때문에 요구사항을 반드시 준수해야 한다.

HMAC-SHA Algorithm
요구 사항 : 주어진 길이의 비트를 가진 키 (EX : 256이면 32byte)
- HS256 (32B)
- HS384 (48B)
- HS512 (64B)

RSA Algorithm
요구 사항은 아니지만 JJWT는 다음을 권장한다
- "RS256"과 "PS256"의 최소 키 비트 길이 2048
- "RS384"과 "PS384"의 최소 키 비트 길이 3072
- "RS512"과 "PS512"의 최소 키 비트 길이 4096

### Creating Safe Keys
만약 네가 비트 길이 요구 사항에 대해 생각하지 않는 것을 원하거나 네가 더 편해지고 싶다면, JJWT는 네가 원하면 사용할 수 있는 다른 주어진 JWT 서명 알고리즘에 적합한 충분한 보안 키를 생성할 수 있는 "io.josnwebtoken.security.Keys" utility Class를 지원합니다.

### Secret Keys
만약 네가 JWT HMCA-SHA 알고리즘 사용 대상으로 충분할 만큼 견고한 SecretKey를 생성하기를 원한다면, Keys.secretKeyFor(SignatureAlgorithm)를 사용해라
```java
SecreKey key = Keys.secretKeyFor(SignatureAlgorithm.HS256); 
// 인자 값 SignatureAlgorithm.HS384, SignatureAlgorithm.HS512
```
이 method에 대한 기능은(Under the hood -> 몰라도 사용 하는 데에 지장 없지만 제대로 사용하기 위해 원리를 알 필요 있는... 정도로 해석) JJWT는 주어진 알고리즘을 위한 올바른 최소 길이로 secure-random key를 제작하기 위한 JCA(Java Cryptography Architecture) 공급자의 KeyGenerator를 사용했습니다.

네가 새 SecretKey를 저장하기 위해 필요하다면, 너는 Base64로 그것을 인코딩할 수 있습니다.
```java
String secreString = Encoders.Base64.encode(key.getEncoded);
```
당신이 어딘가에 저장한 secretString의 결과가 저장 하는 것을 보증하기 위함입니다. (Base64-encoding은 암호화가 아닙니다), 그래서 그건 여전히 예민한(신경 써야 하는) 정보로 여겨진다.
당신은 그걸 더 암호화할 수 있다.

### 또 다른 방법
위의 SecretKey 생성은 JJWT에서 자동으로 SecretKey를 생성해 준다. 하지만 사용자 지정 문자열이나 byte를 기반으로 SecretKey를 생성할 수 있다.
```java
import io.jsonwebtoken.security.Keys;
import javax.crypto.SecretKey;
import io.jsonwebtoken.io.Decoders;

SecretKey key = Keys.hmacShaKeyFor(encodedKeyBytes); // encoding 된 바이트 배열

SecretKey key = Keys.hmcaShaKeyFor(Decoders.BASE64.decode(secretString)); 
// encoding된 문자열 ↕
SecretKey key = Keys.hmcaShaKeyFor(Decoders.BASE64URL.decode(secretString));

SecretKey key = Keys.hmcaShaKeyFor(secretString.getBytes(StandardCharsets.UTF_8));
// ↑ 해당 key는 encode되지 않은 문자열 사용으로 위의 방법보다 보안성이 떨어진다
```
항상 secretString.getBytes()를 사용하는 것은 올바르지 않다.(StandardCharsets같은 문자 집합 제공이 없을 경우)
그러나 이와 같은 문자열 비밀번호, 예를 들어 `correcthorsebatterystaple` should be avoided whenever possible because they can inevitably result in weak or susceptible keys. Secure-random keys are almost always stronger. If you are able, prefer creating a [new secure-random secret key](https://github.com/jwtk/jjwt#jws-key-create-secret) instead.

### Creating a JWS
1. Jwts.builder() method로 JwtBuilder 인스턴스를 생성한다.
2. 헤더 파라미터를 추가하기위해 JwtBuilder 메서드를 호출하고 원하는대로 요구한다.
3. 네가 JWT를 서명하고 사용되기를 바라는 특정한 SecreyKey나 비대칭 PrivateKey를 지정한다.
4. 마지막으로, compact() method를 호출하는 것으로 서명한다.
```java
SecretKey key = Keys.secretKeyFor(SignatureAlgorithm.HS256)
String jws = Jwts.builder()
	.setSubject("Bob")
	.signedWith(key)
	.compact();
```
### Header Parmeters
JWT는 본문에 대한 메타데이터를 제공합니다. JWT의 Claims와 관련된 형식과 암호화 작업
```java
String jws = Jwts.builder()
	.setHeaderParam("kid", "myKeyId")
```

### Claims
Claims는 JWT의 body이며 JWT 제작자가 바라는 JWT 수신자의 현재까지 정보를 내포한다.
```java
String jws = Jwts.builder()
	.setIssuer("발급자")
	.setSubject("사용자 식별자")
	.setAudience("수신자")
	.setExpiration(new Date(System.currentTimemilis + expired)) // java.util.Date
	// 토큰 만료 시간
	.setNotBefore() // java.util.Date
	.setIssuedAt(new Date()) // 발급 시간
	.claim("key", "value") // 사용자 지정 claim
	.compact()
```