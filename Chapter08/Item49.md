## 매개변수가 유효한지 검사하라  

대체로 메소드의 파라미터들은 값이 특정 조건을 만족하기를 바란다. 
예를들어, 인덱스 값은 음수면 안되고 객체 참조는 ```null```이 아니여아 한다.  

이러한 제약은 메서드의 시작에서 바로 검사해야하며, 반드시 문서화해야 한다. 
그렇지 않으면 메소드가 실행 중간에 모호한 예외를 발생시키거나, 
더 안좋은 상황은 예외 없이 돌아가면서 이상한 값을 리턴해버리는 것이다. 

``` java
/**
 *  @param m 계수 (양수여야 함)
 *  @return 현재 값 mod m
 *  @throws ArithmeticException m이 0보다 작거나 같으면 발생
 */
 public BigInteger mod(BigInteger m){
	 if(m.signum() <= 0) throw new ArithmeticException("m must be positive number");
 }
``` 

위 코드는 메소드의 제약은 설정되어 있으나 만약 m이 ```null```이 들어오면 ```NullPointerException```이 발생한다. 
null에 대한 방어는 어노테이션 기반으로 ```@NotNull```이나 ```@NonNull``` 같이 표현할 수도 있지만 이는 표준적인 방법은 아니다. 
Java 7에서부터는 ```java.util.Objects.requireNonNull``` 메소드를 제공한다.

``` java
/**
 *  @param m 계수 (양수여야 함)
 *  @return 현재 값 mod m
 *  @throws ArithmeticException m이 0보다 작거나 같으면 발생
 */
 public BigInteger mod(BigInteger m){
	 m = Objects.requireNonNull(m); // m이 null이면 NullPointerException 발생
	//  m = Objects.requireNonNull(m, "m is null"); // Exception Message 정의 가능

	 if(m.signum() <= 0) throw new ArithmeticException("m must be positive number");
 }
``` 

또한, 파라미터의 유효성을 검사하는 구문 중에는 ```assert```가 있다. 
공개되지 않는 메소드 즉, public 메소드라면 반드시 유효한 값의 파라미터가 넘어온다는 것을 보장할 수 있고 
이 때 ```assert```를 통해 유효성 검증을 한다.

assert
- 실패하면 ```AssertionError```를 던진다.
- 런타임에 아무런 효과도, 성능 저하도 없다.

``` java
private static void sort(long a[], int offset, int length){
	assert a != null;
	assert offset >= 0 && offset <= a.length;
	assert length >= 0 && length <= a.length - offset;
}
```  

규칙에도 항상 예외는 있다. 유효성 검사에 대한 비용이 너무 높거나, 암묵적으로 검사가 진행될 때다.
예를들면, 객체를 정렬하는 ```Collection.sort(list)``` 같은 경우 리스트 안의 모든 요소들은 상호 비교가 가능해야 한다.
그렇지 않으면 정렬 과정에서 ```ClassCastException```이 발생할 것이다. 
이처럼 사전에 모든 비교를 통해 검증을 하더라도 암묵적으로 검증을 할 수 있을 때는 예외적으로 유효성 체크를 하지 않기도 한다. 