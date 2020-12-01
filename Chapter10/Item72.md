## 표준 예외를 사용하라  

가능하다면 재사용 가능한 기능은 재사용을 하자. 
그리고 자바 라이브러리는 대부분 경우 재사용 가능한 충분한 예외를 제공하고 있다.  
상황이 맞는다면 표준 예외를 사용하는 것이 가독성이 높고 다른 사람의 사용이 쉽다.  

널리 재사용 되고 있는 일부 예외들은 다음과 같다. 

|Exception|Description|
|:----|:----|
|```IllegalArgumentException```|부적합한 값이 인자로 넘어왔을 때|
|```IllegalStateException```|객체가 메소드를 수행하기에 적합하지 않은 상태일 때|
|```NullPointerException```|null을 허용하지 않는 메소드에 null을 전달했을 때|
|```IndexOutOfBoundsException```|인덱스가 범위를 넘어섰을 때|
|```ConcurrentModificationException```|허용하지 않는 동시 수정이 발견됬을 때|
|```UnsupportedOperationException```|호출한 메소드를 지원하지 않을 때|  

하지만 ```Exception```, ```RuntimeException```, ```Throwable```, ```Error```는 직접 사용하지 않는 것이 좋다. 
이 예외들은 다른 예외들을 구현하기 위한 상위 클래스이다.
즉, 여러 성격의 예외들을 모두 포함하고 있기 때문에 안정적인 테스트를 할 수 없다.