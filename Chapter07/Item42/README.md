## 익명 클래스보다는 람다를 사용하라  

### Anonymous Class

초기 자바에서는 함수를 표현할 때 추상 메서드를 하나만 담은 인터페이스를 구현할 때, 익명 클래스를 자주 사용하였다.

``` java
Collections.sort(words, new Comparator<String>(){
	public int compare(String s1, String s2){
		return Integer.compare(s1.length(), s2.length());
	}
})
```

### Lambda  

Java 8 이후 부터는 하나의 추상 메소드를 담는 인터페이스를 함수형 인터페이스라고 명명하고, 
이들을 람다식을 사용해 만들 수 있게 되었다.  

``` java
Collections.sort(words, (s1, s2) -> Integer.compare(s1.length(), s2.length()));
```

이처럼 람다를 통해 return type, parameter type, interface name을 모두 생략하고 간결한 표현을 할 수 있다. 이는 컴파일러가 알아서 컨텍스트를 살펴 알아서 추론한다. 

그리고 이를 좀 더 간결하게 표현할 수 있는 문법도 있다.

``` java
Collections.sort(words, comparingInt(String::length));
```

``` java
words.sort(comparingInt(String::length));
```

간결하게 표현할 수 있는 만큼 람다를 작성할 때 주의해야할 점도 존재한다. 
- 람다는 이름이 없기에 문서화를 하기 어렵기에 코드 자체로 명확하게 기능이 설명되지 않거나 코드가 길어진다면 람다를 지양해야 한다.
- 람다는 자기 자신을 참조할 수 없다. this는 람다 본인이 아닌 외부 인스턴스를 가리킨다.
- 람다는 직렬화 형태가 구현에 따라 상이할 수도 있기에 직렬화는 되도록 피해야한다.