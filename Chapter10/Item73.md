## 추상화 수준에 맞는 예외를 던지라  

메소드가 저수준의 예외를 처리하지 않고 이를 바깥으로 전파해버리는 경우가 있다. 
이를 처리 없이 그대로 드러낸다면 내부 구현 방식을 드러내고, 이를 사용하는 윗 레벨의 API 또한 오염시킨다. 
이는 결국 추후에는 API를 사용하는 클라이언트 프로그램이 동작하지 않는 수준까지 갈 수 있다. 

이러한 문제를 해결하기 위해서 상위 계층에서는 저수준의 예외를 잡아 자신의 추상화 수준에 맞는 예외로 바꿔야 한다.
이를 예외 번역(Exception Translation)이라고 한다. 

``` java
try{
	...
}
catch(LowerLevelException e){
	throw new HigherLevelException(...);
}
```

실제로 List에서 발생하는 ```IndexOutOfBoundsException```의 구현을 예로 들 수 있다. 

``` java
public E get(int index){
	ListIterator<E> i = listIterator(index);
	try{
		return i.next();
	}
	catch(NoSuchElementExcpetion e){
		throw new IndexOutOfBoundsException(index);
	}
}
```


예외 번역에서 저수준의 예외가 디버깅에 도움이 된다면 체이닝을 사용하는 것이 좋다. 
저수준의 예외 원인을 고수준의 예외에 포함되어 만들어 필요시 체크를 할 수 있게 하는 것이다.

``` java
try{
	...
}
catch(LowerLevelException e){
	throw new HigherLevelException(e);
}
```

``` java
class HigherLevelException extends Exception {
	HigherLevelException(Throwable cause){
		super(cause);
	}
}
```