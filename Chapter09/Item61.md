## 박싱된 기본 타입보다는 기본 타입을 사용하라  

자바에는 기본 타입인 int, double, boolean 등이 존재하고 
이에 상응하는 박싱된 타입인 Integer, Double, Boolean 등이 존재한다.  

둘 사이 간에 오토박싱과 오토언박싱으로 두 타입을 크게 구분하지 않고 사용할 수 있지만 차이는 분명히 존재한다.

1. **기본 타입은 값만 가지고 있으나, 박싱된 기본 타입은 값과 identity 속성을 가진다.**

String에서 문자열 일치를 비교할 때 동등 연산자가 아닌 equal 메소드를 사용하는 것 처럼 박싱된 기본 타입에 동등 연산자를 사용하는 것은 위험이 있다. 
아래 코드는 Not Equal을 출력하고 언박싱을 해야 올바른 결과를 출력한다.

``` java
public class Test{
	public static void main(String[] args){
		Integer a = new Integer(1);
		Integer b = new Integer(1);

		if(a == b){
			// Execute
			System.out.println("Equal!");
		}
		else{
			System.out.println("Not Equal!");
		}

		int aa = a;
		int bb = b;

		if(aa == bb){
			System.out.println("Equal!");
		}
		else{
			// Execute
			System.out.println("Not Equal!");
		}
	}
}
```  

2. **기본 타입의 값은 언제나 유효하나, 박싱된 기본 타입은 null 값을 가질 수 있다.**  

``` java
public class Test{
	static Integer i;
	
	public static void main(String[] args){
		// NullPointerException 발생!
		if(i == 42){
			System.out.println("Unbelievable!");
		}
	}
}
```  

3. **기본 타입이 박싱된 기본 타입보다 확실히 시간과 메모리면에서 효율적이다.**

그리고 둘을 섞어 쓰면 박싱과 언박싱 연산이 반복 발생하며 체감될 정도로 느려진다.  

``` java
public class Test{
	public static void main(String[] args){
		// 합계는 Wrapper Type
		Long sum = 0L;

		// Iterator는 Primitive Type
		for(long i = 0 ; i <= Integer.MAX_VALUE ; i++){
			sum += i;
		}

		System.out.println(sum);
	}
}
```  

기본 타입만으로 해결할 수 있는 경우라면 기본 타입으로 해결하자. 그렇다면 기본 타입으로만으로 해결할 수 없는 경우는 어떤게 있을까? 

가장 대표적으로는 Collection의 Element, Key, Value가 되는 값들은 Primitive 타입을 사용해야 한다. 
일반화하여 말하면 타입 매개변수로는 박싱된 기본 타입을 써야 한다. 또한 리플렉션을 통해 메소드를 호출할 때도 박싱된 기본 타입을 써야한다.