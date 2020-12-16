## 예외의 상세 메시지에 실패 관련 정보를 담아라  

프로그램이 예외 처리를 하지 못하면 stack trace 정보를 출력해준다. 
이는 예외 객체의 ```toString``` 메서드를 호출하여 얻는 결과이다. 
그렇기에 예외를 정의할 때는 toString 메서드에 그 원인에 대한 정보를 가능한 많이 담아 반환해야한다. 

예외가 발생했을 관련된 모든 매개변수와 필드의 값이 메시지에 포함되어야 한다. 
예를 들어 ```IndexOutOfBoundsException``` 같은 경우에는 유효한 인덱스의 시작과 끝 값, 그리고 잘못 접근한 인덱스 정보를 포함하는 것을 예로 들 수 있다. 

``` java
public IndexOutOfBoundsException(int lowerBound, int upperBound, int index){
	super(String.format("Min: %d, Max: %d, Index: %d", lowerBound, upperBound, index));

	this.lowerBound = lowerBound;
	this.upperBound = upperBound;
	this.index = index;
}
```