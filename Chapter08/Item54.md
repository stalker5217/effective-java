## null이 아닌, 빈 컬렉션이나 배열을 반환하라  

``` java
private final List<Cheese> cheesesInStock;

/**
 *  @return 매장 안의 모든 치즈 목록 반환.
 */
public List<Cheese> getCheeses(){
	return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
}
```  

흔히 볼 수 있는 코드다. 재고가 만약 없으면 null을 반환하고 있는데 사실 재고가 없다고해서 특별 취급할 필요가 없다.  

이와 같은 코드는 API를 구현하는 측과 클라이언트 모두에게 손해이다. 
크기가 0일 때만 특별 취급하여 null을 반환하고, 클라이언트는 이를 핸들링하기 위한 방어 코드를 작성해야 한다. 

``` java
// 전형적으로는 아래처럼 작성할 수 있다.
public List<Cheese> getCheeses(){
	return new ArrayList<>(cheesesInStock);
}

// cheesesInStock.size()와 Parameter 배열의 사이즈 중 큰 놈으로 알아서 만들어진다.
public Cheese[] getCheeses2(){
	return cheesesInStock.toArray(new Cheese[0]);
}

// 참고로 배열을 미리 할당하는 것은 추천하지 않는다
// 오히려 성능이 떨어진다
// Wrong Code!
public Cheese[] getCheeses3(){
	return cheesesInStock.toArray(new Cheese[cheesesInStock.size()]);
}
```

크기가 0인데 불필요하게 비어있는 리스트를 만들어 오버헤드를 만드는 것보다 null을 사용하는 것이 낫다는 생각을 할 수 있다.
하지만 이는 두 가지 측면에서 잘못되었는데 
첫번째는 어지간한 경우에는 단순히 빈 컬렉션을 생성하는 것은 퍼포먼스에 크게 영향을 끼치지 않는다는 것이고, 
두번째는 빈 컬렉션이나 빈 배열을 반환할 때는 굳이 새롭게 할당하지 않는 방법도 존재하기 때문이다.  

``` java
// 빈 컬렉션을 빈번하게 할당하는 것을 막는다
// Collection의 경우에는 'Collections.empty____()' 형태로 immutable object를 제공한다.
public List<Cheese> getCheeses(){
	return cheesesInStock.isEmpty() ? Collections.emptyList() : new ArrayList<>(cheesesInStock);
}
```

``` java
// 빈 컬렉션을 빈번하게 할당하는 것을 막는다
// Collection의 경우에는 'Collections.empty____()' 형태로 immutable object를 제공한다.
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses(){
	// size가 0이라면 항상 EMPTY_CHEESE_ARRAY를 반환한다.
	return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```