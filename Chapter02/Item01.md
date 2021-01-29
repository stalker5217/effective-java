## 생성자 대신 정적 팩터리 메서드를 고려하라  

API를 사용하는 클라이언트 입장에서 인스턴스를 얻는 가장 직관적인 방법은 생성자 호출을 통해 생성하는 것이다. 
하지만, 클래스는 정적 팩터리 메서드를 통해 인스턴스를 제공할 수 있다. 

``` java
public static Boolean valueOf(boolean b){
	return b ? Boolean.TRUE : Boolean.FALSE;
}
```

이렇게 사용했을 때 얻을 수 있는 장점은 무엇일까? 


#### 이름을 가질 수 있다  

생성자는 클래스의 이름과 같다. 
이를 통해 생성할 때는 객체의 특성을 제대로 설명하지 못할 때가 있다. 

``` java
BigInteger a = new BigInteger(bitLength, certainty, new Random());
```

위 코드는 해당 내용만 보고는 대체 뭘 생성하는건지 파악하기가 힘들다. 
이 코드의 정체는 파라미터로 주어진 비트 길이로 표현되는 수 중에 소수를 랜덤으로 생성하는 것이다. 그리고 BigInteger Docs에서는 저런 식으로 사용하는 것보다 정적 팩토리 메서드 사용하여 생성할 것을 권장하고 있다. 

``` java
BigInteger a = BigInteger.probablePrime(bitLength, new Random());
```

이처럼 정적 팩토리 메서드를 사용하면 정확히 어떤 인스턴스를 반환할지 설명할 수 있다.

<br/>

#### 호출될 때마다 인스턴스를 생성하지 않아도 된다. 

정적 팩토리 메서드를 사용한 클래스는 언제 어떤 인스턴스가 살아있는지 통제할 수 있다. 
그렇기에 미리 인스턴스를 생성해두어 요청할 때마다 반환을 해준다거나, 
새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다. 

<br/>

#### 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다. 

이는 반환할 클래스를 자유롭게 선택할 수 있는 확장성을 가질 수 있게 된다. 
예를 들어, ```EnumSet``` 클래스 같은 경우에는 비트 필드로 집합을 나타내기 때문에 집합의 크기에 따라 구현체가 달라질 수 있다. 
64개 아래로 표현된다면 ```long``` 단일 변수로 이루어진 ```RegularEnumSet```을 반환하고, 
64개를 초과한다면 ```long``` 의 배열로 이루어진 ```JumboEnumSet```을 반환한다. 

정적 팩토리로 구현된 EnumSet을 얻는 메서드를 사용하면 이를 구분할 필요가 없다. 
파라미터만 넘기면 내부에서 알아서 결정해서 반환을 해주기 때문이다. 

결국, 이렇게 하위 타입의 인터페이스를 반환한다면 구현 클래스를 공개하지 않음으로써 사용자 입장에서 API를 간단하게 만들 수 있다. 

<br/>

#### 정적 팩토리 메서드를 작정하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.  

유연함은 더 나아가 반환하는 클래스의 구현체가 현재 존재하지 않아도 된다.
이는 Service Provider Framework를 만드는 기본 개념이 된다. 
Service Provider Framework는 세 가지 컴포넌트로 구성된다.
구현체의 동작을 정의하는 Service Interface, 
제공자가 구현체를 등록할 때 사용하는 Provider Registration API, 
클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 Service Access API이다.

``` java
/**
 * DriverManager.registerDriver() : Provider Registration API
 * (리플렉션을 통해 org.h2.Driver를 로드할 때 내부에서 호출된다)
 * Connection : Service Interface
 * DriverManager.getConnection() : Service Access API
 */
Class.forName("org.h2.Driver");
Connection conn = DriverManager.getConnection("jdbc:h2:mem:testdb", "sa","");
```

대표적인 예가 JDBC이다.
위는 JDBC의 전형적인 코드인데, 분명 데이터베이스마다 Connection은 다를 것이다. 
리플렉션을 통해 드라이버를 등록하고 이를 기반으로 동적으로 Connection이 결정되어 생성된다. 

<br/>

이처럼 많은 장점이 있다. 하지만 세상만사 좋은점만 있을 수는 없다. 분명히 단점도 있다. 
먼저 상속을 사용하려면 ```public```이나 ```protected```로 정의된 생성자가 있어야 한다. 
상속을 사용하기 위해서는 반드시 생성자를 구현하여야 한다. 

또한, 메서드를 찾기가 힘들다는 것이다. 
위에서 예로든 ```BigInteger.probablePrime``` 도 이런 메서드가 존재하고 있다는 사실을 모를 확률이 크다. API 제작시 그만큼 문서를 공들여 써줘야 한다. 

흔히, 정적 팩토리 메서드를 위해 많이 사용되는 네이밍룰이 존재한다. 
해당 네이밍으로 작성을 한다면 얼추 알아볼 수 있다. 

|name|description|example|
|:---|:---|:---|
|from|파라미터를 하나 받아 해당 타입의 인스턴스를 반환|```Date d = Date.from(instant)```|
|of|여러 파라미터를 받아 해당 타입의 인스턴스를 반환|```EnumSet.of(JACK, QUEEN, KING)```|
|valueOf|from + of|```BigInteger.valueOf(Integer.MAX_VALUE)```|
|instance, getInstance|인스턴스를 반환|```StackWalker luke = StackWalker.getInstance(options)```|
|create, newInstance|매번 새로운 인스턴스 생성을 보장|```Object newArray = Array.newInstance(classObject, arrayLen)```|
|getType|getInstance인데 다른 클래스의 인스턴스를 반환|```FileStore fs = Files.getFileStore(path)```|
|newType|newInstance인데 다른 클래스의 인스턴스를 반환|```BufferedReader br = Files.newBufferedReader(path)```|
|Type|getType or newType|```List<Complaint> litany = Collections.list(legacyLitany)```|