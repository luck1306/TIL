
created 230905 14:00~14:30

```java
public class TargetClass {
	Logger log = Logger.getLogger(Target.class.getName());
	public void run() {
		log.info("this is TargetClass.run")
	}
}
```

```java
public class ProxyInterceptor implements MethodInterceptor {
	Object target;
	Logger log = Logger.getLogger(ProxyInterceptor.class.getName());
	public ProxyInterceptor(Object target) {this.target = target;}
	@Override
	publoc Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Trowable {
		log.info("before call method");
		proxy.invoke(target, args);
		log.info("after call method");
		return method;
	}
}
```

```java
public class Main {

	public static void main(String[] args) {
		Enhancer enhancer = new Enhancer();
		enhancer.setSuperClass(TargetClass.class);
		enhancer.setCallback(new ProxyInterceptor(new Targetclass()));
		TargetClass target = (TargetClass) enhancer.create();
		target.run();
	}
}
```

```terminal
before call method
this is TargetClass.run
after call method
```

update 230905 14:34 ~ 16:18
```java
public interface MethodInterceptor extends Callback {
	public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable;
}
```

obj : "this", enhanced object (원본 클래스)
method : intercepted Method (원본 클래스에서 실행될 method 객체)
args : 원시타입으로 감싼 인자 배열
proxy 
- invoke() : 같은 타입이면서 다른 객체 사용 시(매개변수 "obj" 사용하지 않고 새로 객체를 만들 때) 사용
- invokeSuper() : 매개변수로 받아온 객체 "obj"로 호출할 때 사용