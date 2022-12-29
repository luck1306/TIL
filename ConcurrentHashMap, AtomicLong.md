
_배경 지식_
java synchronized는 같은 객체를 여러 thread가 사용할 때 데이터 무결성의 유지를 위해 동기 방식으로 처리하게 돕는 것

```java
public class Main {
	public static void main(String[] args) {
		Hello sync = new Hello();
		Thread thread1 = new Thread(() -> {  
		    sync.method1();  
		});  
		Thread thread2 = new Thread(() -> {  
		    sync.method2();  
		});  
		Thread thread3 = new Thread(() -> {  
		    sync.method3();  
		});  
		thread1.start();  
		thread2.start();  
		thread3.start();
	}
}
```

```java
public class Hello {
	public void method1() {  
	  
	    for (int i = 0; i < 10; i++) {  
	        System.out.println("method1");  
	        try {  
	            Thread.sleep((int) (Math.random() * 1000));  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	    }  
	}  
	  
	public void method2() {  
	  
	    for (int i = 0; i < 10; i++) {  
	        System.out.println("method2");  
	        try {  
	            Thread.sleep((int) (Math.random() * 100));  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	    }  
	}  
	  
	public void method3() {  
	  
	    for (int i = 0; i < 10; i++) {  
	        System.out.println("method3");  
	        try {  
	            Thread.sleep((int) (Math.random() * 1000));  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	    }  
	}
}
```

__위와 같은 코드에서는 method(1~3)이 무작위로 찍힌다 - (하나의 작업이 모두 끝나기 전에 객체 사용 가능)__
	이와 같은 일로 생기는 문제의 대표적인 예) db에 데이터를 조회하기 전에 조회할 데이터를 수정하면 올바른 데이터가 조회되지 않는다.

쓰레드 동기화를 위해서는 Hello의 모든 method에 'synchronized'를 추가한다.

```java
public class Hello {
	public synchronized void method1() {  
	  
	    for (int i = 0; i < 10; i++) {  
	        System.out.println("method1");  
	        try {  
	            Thread.sleep((int) (Math.random() * 1000));  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	    }  
	}  
	  
	public synchronized void method2() {  
	  
	    for (int i = 0; i < 10; i++) {  
	        System.out.println("method2");  
	        try {  
	            Thread.sleep((int) (Math.random() * 100));  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	    }  
	}  
	  
	public synchronized void method3() {  
	  
	    for (int i = 0; i < 10; i++) {  
	        System.out.println("method3");  
	        try {  
	            Thread.sleep((int) (Math.random() * 1000));  
	        } catch (InterruptedException e) {  
	            e.printStackTrace();  
	        }  
	    }  
	}
}
```

## ConcurrentHashMap
- Hashtable은 모두 동기 처리가 되어있어 multi-thread 환경에서 사용하기 적합하지만 속도가 너무 느리다.
- Hashtable의 느린 속도를 개선한 클래스가 ConcurrentHashMap이다.
	- Hashtable과 다르게 특정 블록만 동기 방식으로 처리한다
- ConcurrentHashMap은 동시에 작업 가능한 쓰레드 수(DEFAULT_CONCURRENT_LEVEL)와 최대 버킷의 수(DEFAULT_CAPACITY)를 지정 가능한데 