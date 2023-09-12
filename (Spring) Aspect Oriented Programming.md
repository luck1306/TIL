[created] 230901 13:53~ 16:39
[update] 230904 10:00 ~ 16:44
AOP는 프로그램 구조에 대한 생각에서 다른 방법을 제공하는 것으로 OOP를 보완합니다. OOP에서 모듈화의 핵심단위는 class인 반면에 AOP의 모듈화 단위는 Aspect(관점)입니다. Aspect는 다양한 타입과 객체에서 공통적으로 사용하는 관계(트랜잭션 관리와 같은 것들)의 모듈화가 가능합니다. (이런 우려는 AOP 문헌에서 "crosscutting" 걱정이라 불립니다.)
AOP framework는 Spring의 주요한 구성요소 중 하나입니다. ...(ref : https://docs.spring.io/spring-framework/reference/core/aop.html)

- Aspect : 다수의 클래스를 가로지르는 관계의 모듈화. 트랜잭션 관리는 enterprise java application에서 가로지르는 관계(Aspect)의 좋은 에시입니다. Spring AOP에서 aspect는 표준 클래스나 @Aspect annotaion과 같은 표준 annotation 클래스를 사용해 구현됩니다.
- Join Point : 메서드 핸들링이나 예외 처리의 실행과 같은, 프로그램이 실행하는 도중의 지점이다. Spring AOP에서 join point는 언제나 메서드의 실행을 의미합니다.
- Advice : 특정한 join point에서 수행되는 aspect입니다. Advice의 다양한 타입으로 "around", "before", "after"가 있습니다. (Advice의 타입에 대해서는 후에 다룹니다.) Spring을 포함하는 많은 AOP framework에서 Advice를 interceptor 모델로 삼으며 join point 주위의 interceptor 연결을 유지합니다.
- Pointcut : 앞의 join point에 대응됩니다. Advice는 pointcut 표현식과 연관되고 예를 들어 특정한 이름을 포함한 메서드의 표현식과 같은 pointcut에 의해 실행 시 join point와 대응됩니다. pointcut 표현식에 의해 대응되는 join point의 개념은 AOP의 핵심입니다. 또한 Spring은 기본적으로 AspectJ pointcut 표현언어를 사용합니다.
- Introductioin : 타입적 이익때문에 추가적으로 메서드나 필드를 선언하는 것입니다. Spring AOP는어느 Advice가 적용된 객체로 사용자의 새로운 인터페이스(그리고 그에 상응하는 구현체)를 도입(Introduction)하는것을 허락합니다. 예를 들어 도입(Introduction)으로 Bean을 "IsModified" 인터페이스로 구현하고 캐싱을 단순화할 수 있습니다.(도입(Introduction)은 AspectJ 커뮤니티에서 타입 간 선언이라고 불립니다.)
- Target Object : 하나 이상의 aspect에 의해 조언받는(공통로직이 실행되는) 객체입니다. 또한 "조언받는 객체"로서 참조되며 Spring AOP가 runtime proxies로 부터 구현되므로 이 객체는 항상 프록시 객체입니다.
- AOP proxy : aspect 표현식을(advise 메서드의 실행이나 기타 등등) 구현하기 위해 AOP framework 객체를 만듭니다. Spring framework에서 AOP proxy는 JDK dynamic Proxy나 CGLIB proxy입니다.
- Weaving : aspect를 다른 어플리케이션 타입이나 advise 객체를 만드는 객체로 연결하는 것입니다. Weaving은 컴파일 타임(예를 들어 Asepctj compiler를 사용하는 것), 로드 타임 혹은 런타임에 완료될 수 있습니다. 

spring aop는 proxy객체를 만들어 aop를 구현하는데, aop를 만들기 위한 2가지 방법이 있습니다. jdk dynamic proxy와 cglib다. jdk dynamic proxy 방식은 advice를 적용할 target의 join point에 대한 proxy객체를 생성해 aop를 구현합니다. 하지만 이 과정에서 해당 target 객체가 구현하는 interace가 반드시 필요합니다. 만약 target 객체가 interface를 구현하지 않은 class를 기반으로 aop기능을 사용하고 싶을 때 cglib를 사용합니다. 일반적으로 MethotInterceptor 방식으로 aop를 구현합니다


```
└─AOP
   ├─JDK Dynamic Proxy (Proxy.newProxyInstance)
   └─CGLIB
      ├─InvocationHandler
      └─MethodInterceptor
```


### JDK Dynamic Proxy
- 스프링 AOP 구현의 표준으로 사용되던 방법
- java.lang.reflect.Proxy 객체 이용
- interface 반드시 필요
- invocationHandler interface 정의 필요
- runtime시 동적으로 타입을 분석하고 정보를 가져오는 reflection api를 사용하기에 사용시 성능 오버헤드가 발생한다.

![[Pasted image 20230904163449.png]]
[example](./JDK_Dynamic_Proxy.md)

### CGLIB
- interface를 구현하지 않은 객체에 AOP를 적용하기 위한 방법
- 현재 Spring AOP 구현의 표준으로 사용되는 방법
- 구체적인 class를 기반으로 proxy를 만듦
- 컴파일된 바이트코드를 조작해 proxy를 생성하며 이로인해 reflection api를 이용하는 방법보다 오버헤드가 적음
- MethodInterceptor interface 구현으로 proxy 정의

![[Pasted image 20230904163532.png]]
[example](./CGLIB.md)
