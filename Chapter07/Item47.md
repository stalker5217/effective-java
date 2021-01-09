## 반환 타입으로는 스트림보다 컬렉션이 낫다  

스트림이 등장하면서 원소 시퀀스를 반환할 때는 어떤 것으로 해야할지 어려워졌다. 
결론부터 말하자면 가능한 경우라면은 컬렉션 형태로 반환하는 것이 좋다. 
클라이언트에서 for-each 구문으로 순회하기가 힘들기 때문이다. 

하지만 스트림에서는 ```Iterable``` 인터페이스에 포함된 메서드를 모두 포함하고 있고, 거기에 맞게 동작한다. 
그럼에도 for-each 구문을 사용할 수 없는 것은 ```Iterable```을 implement하지는 않았기 때문이다.

``` java
// 오류가 나버린다
for(ProcessHandle ph : ProcessHandle.allProcesses()::iterator){
	...
}
```

하지만, 형 변환을 하면 사용할 수 있다. 

``` java
for(ProcessHandle ph : (Iterable<ProcessHandle>) ProcessHandle.allProcesses()::iterator){
	...
}
```

이런 코드는 얼핏봐도 보고 있고 싶지 않다. 
웬만하면 컬렉션을 반환하자. 
컬렉션은 for-each 구문으로 쉽게 순회 가능하며, 스트림으로의 변환 또한 지원하기 때문이다. 

스트림을 반환해야하는 경우는 메모리 이슈이다. 
컬렉션 같은 경우에는 포함하는 모든 값이 메모리에 저장하는 구조이고, 
스트림은 이론적으로는 연산 요청이 있을 때만 필요한 부분을 메모리에 올리는 구조인 일종의 Lazy Collection이라고 볼 수 있다. 

따라서, 시퀀스의 크기가 메모리에 올려도 충분히 작다면 컬렉션을 반환하는게 맞지만 그게 아니라면 스트림을 고려해야 한다. 

하지만 위의 코드는 정말이지 보기 싫다. 이를 해결하기 위해서는 별도록 어댑터 메서드를 작성하는 것이다. 

``` java
public static <E> Iterable<E> iterableOf(Stream<E> stream){
	return stream::iterator;
}
```

``` java
for(ProcessHandle p : iterableOf(ProcessHandle.allProcesses())){
	...
}
```

이렇게 별도의 메서드 구현으로 조금은 더 깔끔한 구현을 할 수 있다. 
그런데 또 ```Iterable```만 반환한다면 스트림 파이프라인을 태우기가 힘들다. 
이 때도 마찬가지로 어댑터 메서드를 작성할 수 있다. 

``` java
public static <E> Stream<E> streamOf(Iterable<E> iterable){
	return StreamSupport.stream(iterable.spliteratort(), false);
}
```