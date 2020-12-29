## 표준 함수형 인터페이스를 사용하라  

자바에서 람다를 지원하면서 모범적인 API의 작성 방법에도 변화가 생겼다. 
기존 클래스에서 상속을 통해 기능을 재정의하는 템플릿 메서드 패턴를 통한 구현 대신에, 
함수 객체를 받아서 적용하는 팩토리나 생성자를 구현하는 것으로 말이다. 

구체적인 예시로 확인하자면 ```LinkedHashMap``` 객체가 있다. 
이는 삽입 순서를 유지할 수 있는 Map 자료구조이며, 여기에는 protected로 구현된 ```removeEldestEntry``` 메서드가 있다. 
이 메소드는 ```put()```이 호출될 때마다 호출되며 Map에서 가장 오래된 원소를 파라미터로 받는다. 
그리고 boolean으로 ```true```가 반환되면 이 원소를 삭제한다. 

이 메서드를 새롭게 정의하면 일정 크기를 유지하는 캐시로 사용할 수 있다. 

``` java
protected boolean removeEldestEntry(Map.Entry<K, V> eldest){
	return size() > 100;
}
```

위에서 설명한 것처럼 현대 자바에서는 이러한 템플릿 메서드 패턴 대신에 
팩토리나 생성자를 통해 함수 객체를 주입 받는 식으로 구현할 수 있다. 

``` java
@FunctionalInterface interface EldestEntryRemovalFunction<K, V>{
	boolean remove(Map<K, V> map, Map.Entry<K, V> eldest);
}
```

이렇게 구현하여도 무방하나 직접 구현하는 것보다는 자바 표준 라이브러리에서 동일한 형태의 인터페이스를 이미 제공하고 있다. 
```java.util.function``` 패키지에서는 다양한 인터페이스를 제공하고 있으며, 
**여기서 제공하고 있는 인터페이스라면 이 표준 함수형 인테페이스를 사용하는 것이 좋다.**

API가 다루는 개념의 수도 줄어들며, 또한 이러한 인터페이스들은 유용한 디폴트 메서드를 제공하므로 기능적으로 풍부하다. 

해당 패키지에는 43개의 인터페이스를 제공하고 있고, 대표적인 몇 가지를 파악함으로써 나머지를 유추할 수 있다. 

|Interface|Function Signature|Example|
|:---|:---|:---|
|```UnaryOperator<T>```|```T apply(T t)```|```String::toLowerCase```|
|```BinaryOperator<T>```|```T apply(T t1, T t2)```|```BigInteger::add```|
|```Predicate<T>```|```boolean test(T t)```|```Collection::isEmpty```|
|```Function<T,R>```|```R apply(T t)```|```Arrays::asList```|
|```Supplier<T>```|```T get()```|```Instant::now```|
|```Consumer<T>```|```void accept(T t)```|```System.out::println```|

대부분의 상황에서는 이를 사용하여 기능 구현을 할 수 있다. 
하지만, 분명히 직접 구현해야할 필요도 있다. 

대표적으로는 ```Comparator<T>```가 있다. 
구조적으로는 ```ToIntBiFunction<T,U>```와 동일한 구조이다. 
하지만 ```Comparator<T>```가 지속되어 사용되는 이유는 아래와 같다.

1. 자주 사용되며, 이름 자체가 그 용도를 명확히 설명한다.
2. 구현하는 쪽에서 반드시 지켜야할 규약을 담고 있다. 
3. 비교자들을 변환하고 조합해주는 디폴트 메소드가 많이 존재한다. 

이 같은 조건이 구현에 필요하다면 직접 구현하는 것을 고려할만 하다. 
그리고 직접 구현할 때는 ```@FunctionalInterface``` 어노테이션을 반드시 넣어주자. 
