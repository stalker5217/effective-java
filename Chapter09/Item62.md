## 다른 타입이 적절하다면 문자열 사용을 피하라  

문자열이 좋은 기능을 제공하다보니 많은 타입을 문자열로 퉁쳐서 사용하는 경우가 많다.  

1. 다른 타입의 값을 대신 표현하지 않는다.  

	숫자라면 int, double 등의 타입을 사용하고, "Y", "N"과 같은 플래그보다는 boolean 변수를 사용한다.  

	<br/>

2. 상수를 표현할 때는 Enum 타입이 더 적합하다.  

	``` java
	// 권장하지 않는 표현
	private static final String ONE = "ONE";
	private static final String TWO = "TWO";

	public enum Number{
		ONE, TWO
	}
	```

	<br/>

3. 문자열은 혼합 타입을 대신하기에 적합하지 않다.

	복합적인 정보를 구분자를 두어 붙여서 사용하지 말자.

	``` java
	String value = name + "#" + age;
	```

	<br/>

4. 문자열은 권한으로 사용하는데 적합하지 않다.  

	스레드에서 사용하는 지역변수를 구현하는데 있어 클라이언트에서 제공하는 String을 키 값으로 사용한다고 가정해보자.

	``` java
	public class ThreadLocalVariable{
		// 생성 불가능하게 막기
		private ThreadLocel() {}

		public static void set(String key, Object value);
		public static Object get(String key);
	}
	```

	키 값을 클라이언트에서 제공하는대로 사용하기 때문에 항상 고유한 값을 가져야하고 같은 문자열을 사용하면 오작동이 발생한다.

	``` java
	public class ThreadLocalVariable{
		// 생성 불가능하게 막기
		private ThreadLocal() {}

		// 권한을 나타내는 클래스
		public static class Key {
			Key() {}
		}

		// 위조 불가능한 고유 키 생성
		public static Key getKey(){
			return new Key();
		}

		public static void set(Key key, Object value);
		public static Object get(Key key);
	}
	```  

	이렇게 된 이상 Key가 각 스레드의 지역 변수를 가져오기 위한 역할이 아니라 그 자체가 스레드의 지역 변수가 된다. 
	현재 구현된 ThreadLocalVariable 클래스의 역할이 없어지므로 Key 클래스 자체를 ThreadLocalVariable로 바꾼다.

	``` java
	public final class ThreadLocalVariable{
		public ThreadLocal();

		public void set(Object value);
		public Object get();
	}
	```

	여기서 변수의 타입을 Object로 하여 형변환하여 사용하지 말고 매개변수화 타입을 사용하면 더 깔끔해진다.

	``` java
	public final class ThreadLocalVariable<T> {
		public ThreadLocal();

		public void set(T value);
		public T get();
	}
	```