## 람다보다는 메서드 참조를 사용하라  

익명 클래스 대신 람다를 사용하여 간결한 표현을 하였다. 하지만 메서드 참조를 사용하면 더 간결한 표현을 할 수 있다.  

``` java
// Java 8 이상 Map에서 제공하는 merge method
// 키가 존재하지 않으면 해당 key에 1을 할당
// 키가 존재하면 해당 key의 값에 1을 더해줌
map.merge(key, 1, (existValue, initValue) -> existValue + initValue);
```

단순 합을 구하는 람다인데 이는 ```Integer``` 클래스에서 제공하는 sum 메소드를 사용할 수도 있다.  

``` java
map.merge(key, 1, (existValue, initValue) -> Integer.sum(existValue, initValue));

// Integer class의 sum을 메소드 참조로 전달하면 아래와 같이 더욱 간결한 표현을 할 수 있다
// 람다로 작성하면 IDE에서 보통 노란색으로 교정 제안을 해준다
map.merge(key, 1, Integer::sum);
```

> 메소드 참조보다 오히려 람다로 작성하는게 더 깔끔할 때가 있는데, 그러면 그냥 람다로 작성하는게 맞다.

<br/>

## 메소드 참조 정리  

|type|example|labmda expression|
|:--|:--|:--|
|static|```Integer::parseInt```|```str -> Integer.parseInt(str)```|
|bound(instance)|```Instant.now()::isAfter```|```Instant then = Instant.now();```<br/>```t -> then.isAfter(t)```|
|unbound(instance)|```String::toLowerCase```|```str -> str.toLowerCase()```|
|Class Constructor|```TreeMap<K,V>::new```|```() -> new TreeMap<K,V>```|
|Array Constructor|```int[]::new```|```len -> new int[len]```|