
created 230904 : 17:00~18:00

예제

```java
public interface Framework {
	void run(String name);
}
```

__원본 객체 역할__
```java
public class Spring implements Framework {

	private String name;

	public String getName() { return this.name; }

	public void setName(String name) { this.name = name; }
	
	@Override
	public void run(String name) {
		System.out.println("spring framework run");
	}
}
```

__proxy 객체 역할__
```java
public class SpringProxy implements InvokationHandler {

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) {
		FrameWork framework = new Spring();
		framework.setName("demo");
		System.out.println("before invoke method");
		method.invoke(framework, args);
		System.out.println("after invoke method");
	}
}
```

__main__
```java
public class Main {

	public static void main(String[] args) {
		Framework proxyFramework = (Framework) Proxy.newProxyInstance(
			Framework.class.getClassLoader(),
			new Class[] {Framework.class},
			new SpringProxy()
		)
		proxyFramework.run("aaaa");
	}
}
```

__실행 결과__
```terminal
before invoke method
spring framework run
after invoke method
```
