## 스트림은 주의해서 사용하라   

### 스트림은 가독성을 해칠 수 있다. 

아래는 입력에 대해서 아나그램을 구성하고 출력하는 코드이다. 
아나그램이란 단어를 구성하는 알파벳 구성이 같고 순서만 다른 단어들을 말한다. 
예를 들어, "staple"과 "aplest"는 모두 알파벳 "aelpst"로 이루어진 단어이므로 같은 아나그램에 속한다. 

``` java
public class Test {
    public static void main(String[] args) {
        List<String> words = new ArrayList<>();
        words.add("staple");
        words.add("aplest");
        words.add("taples");
        words.add("apple");

        anagrams(words);
    }

    public static void anagrams(List<String> words){
        Map<String, Set<String>> groups = new HashMap<>();
        for(String word : words){
            groups.computeIfAbsent(alphabetize(word), (unused) -> new TreeSet<>()).add(word);
        }

        for(Set<String> group : groups.values()){
            System.out.println(group.size() + ": " + group);
        }
    }

    private static String alphabetize(String s){
        char[] a = s.toCharArray();
        Arrays.sort(a);

        return new String(a);
    }
}
```

스트림은 API 순차적이든 병렬적이든 다량의 데이터를 처리를 위해 자바8에서 도입되었다. 
스트림은 만능이기에 어떠한 기능도 구현 가능하다. 
하지만 구현 가능하다는 거지 기존의 for문을 스트림으로 대체하는 것이 만능은 아니다. 
잘 사용하면 읽기 쉽고 짧은 코드가 나오지만, 과하게 사용하면 끔찍한 코드가 나온다.

``` java
public static void anagram(List<String> words){
	words.stream().collect(Collectors.groupingBy(word ->
					word.chars().sorted().collect(StringBuilder::new, (sb, c) -> sb.append((char) c),
							StringBuilder::append).toString()))
			.values().stream()
			.map(group -> group.size() + ": " + group)
			.forEach(System.out::println);
}
```

이처럼 스트림을 사용했을 때 발생할 수 있는 문제점은 가독성이다.
위 코드는 모든 기능을 구문하나에 스트림과 람다를 사용해 다 때려박은 무시무시한 코드이다. 
이런걸 작성하고 트렌디하게 스트림을 썼다고 흐뭇해하지 말자. 

``` java
public static void anagram(List<String> words){
	words.stream().collect(Collectors.groupingBy(word -> alphabetize(word)))
			.values().stream()
			.forEach(group -> System.out.println(group.size() + ": " + group));
}

private static String alphabetize(String s){
	char[] a = s.toCharArray();
	Arrays.sort(a);

	return new String(a);
}
```

### 스트림 안의 데이터는 객체 또는 int, long, double 세 가지 기본 타입이다. 

``` java
"Hello world!".chars().forEach(System.out::print);
```

위 코드는 "Hello world!"가 그대로 출력되는 것이 아니라 7210... 과 같은 숫자가 출력된다. char은 스트림에서 기본적으로 제공하는 타입이 아니며 ```.chars()```가 반환하는 것은 ```IntStream```이기 때문에 숫자를 출력한다. 

``` java
"Hello world!".chars().forEach(c -> System.out.print((char) c));
```

위 같이 구현하면 그대로 출력할 수 있지만 이는 잘못 구현하여 오동작의 확률이 높아지고, 
성능상으로도 느려질 수 있다. 이처럼 ```char```을 처리할 때는 스트림을 삼가하는 것이 좋다. 

### 스트림 내부에서 사용하는 함수 객체나 람다에서는 할 수 없는 일이 존재한다. 

1. 람다에서는 지역변수에 접근하여 수정하는 것은 불가능하고 ```final``` 변수를 읽을 수만 있다. 
2. 반복문 도중 특정한 조건을 만족하면 ```return```이나 ```break```을 호출하여 탈출하거나 ```continue```로 건너뛰는 것이 불가능하다. 

이같이 분명히 일반 for문으로 구현했을 때 효과적인 내용이 있고, 
스트림과 람다를 사용하여 구현하는 것이 좋은 내용도 있다. 
아래 내용이 스트림으로 구현하기가 적합한 내용들이다. 

1. 원소의 시퀀스를 일관되게 변환한다.
2. 원소들의 시퀀스를 필터링한다.
3. 원소들의 시퀀스를 하나의 연산을 사용해 결합한다. (+, -, 최솟값 등)
4. 원소들의 시퀀스를 컬렉션에 모은다.
5. 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는다. 

### 스트림에서 처리하기 어려운 일이 존재한다.  

10개의 메르센 소수를 출력하는 프로그램을 생각해보자. 

> 메르센 수란 $ 2^p - 1 $ 형태의 수이다. p가 소수이면 이 때의 메르센 수도 소수일 수 있는데 이를 메르센 소수라고 한다. 

``` java
int cnt = 0;
for(BigInteger p = BigInteger.TWO ; cnt < 10 ; p = p.nextProbablePrime()){
	BigInteger powVal = BigInteger.TWO.pow(p.intValueExact()).subtract(BigInteger.ONE);

	if(powVal.isProbablePrime(50)){
		System.out.println(p + ": " + powVal);
		cnt++;
	}
}
```

이를 스트림으로 구할 수도 있다.

``` java
Stream
		.iterate(BigInteger.TWO, BigInteger::nextProbablePrime)
		.map(p -> BigInteger.TWO.pow(p.intValueExact()).subtract(BigInteger.ONE))
		.filter(mersenne -> mersenne.isProbablePrime(50))
		.limit(10)
		.forEach(mp -> System.out.println(mp.bitLength() + ": " + mp));
```

하지만 스트림을 사용하는 경우 실제 메르센 소수는 구할 수 있지만 지수(p)를 출력하기는 쉽지 않다. 이미 ```map```에서 값을 매핑하면 기존의 값에 접근할 수 없기 때문이다. 
여기서는 이진수로 표현했을 때 비트의 길이를 측정하여 출력하면 결과를 얻을 수 있다. 
하지만 분명히 직관적이지는 않다. 

결과적으로 반복문을 통해 구현하는게 적합한 것이 있고, 스트림을 사용하는게 적합한 것이 있다. 스트림이 최신 기술이라고 무조건 남용하면 안되고 각각의 케이스에 둘 중 더 적합한 구현 방법을 찾아 혼용하여 구현하는 것이 가장 나이스하다!