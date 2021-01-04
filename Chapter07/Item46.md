## 스트림에서는 부작용 없는 함수를 사용하라  

스트림 패러다임의 핵심은 계산을 일련의 변환으로 재구성하는 부분이다. 
각 단계는 이전 단계의 결과만을 받아 처리하는 **순수 함수**이다. 
순수 함수란 입력 외에는 결과에 영향을 주지 않는 함수를 말하며, 다른 가변 상태를 참조하지 않는다. 
스트림 연산에 포함되는 함수 객체나 람다는 모두 부작용이 없어야 하며 
이 때 ```final``` 이외의 외부 상태를 참조하려고 하면 에러가 발생하는 이유이다. 

아래는 텍스트 파일에서 단어의 빈도를 카운트하려는 코드이다. 

``` java
Map<String, Long> freq = new HashMap<>();
try(Stream<String> words = new Scanner(file).tokens()){
	words.forEach(word -> {
		freq.merge(word.toLowerCase(), 1L, Long::sum);
	});
}
```

하고자하는 목적은 달성하지만 이는 스트림 코드로 볼 수 없다. 
단순히 반복문을 스트림의 ```forEach```로 표현한 것이며 
그냥 반복문을 쓰는 것보다 가독성도 떨어지고 유지보수만 나빠진다. 

스트림을 처음 사용할 때에는 가장 익숙한 ```forEach```문을 이렇게 사용하려고 할 수 있을 것이다.
하지만 ```forEach``` 연산은 가장 스트림스럽지 않고 병렬화할 수도 없다. 
```forEach``` 구문을 사용할 때는 스트림 계산 결과를 표현할 때에만 사용하고 계산 시에는 사용하지 않도록 하자. 

올바른 스트림 코드는 아래와 같이 구성할 수 있다. 

``` java
Map<String, Long> freq;
try(Stream<String> words = new Scanner(file).tokens()){
	freq = words.collect(groupingBy(String::toLowerCase, couunting()));
}
```

```groupingBy``` 같은 구문은 ```java.util.stream.Collectors```를 static import 하여 사용하고 있으며 
스트림을 잘 사용하기 위해서는 알아두도록 하자. 

[Class Collectors](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)  

몇 가지를 알아보자면 먼저 ```toMap```이 있는데 세 가지 형태의 메소드를 제공한다. 
- ```toMap(Function keyMapper, Function valueMapper)```
- ```toMap(Function keyMapper, Function valueMapper, BinaryOperator mergeFunction)```
- ```toMap(Function keyMapper, Function valueMapper, BinaryOperator mergeFunction, Supplier mapSupplier)```

원소에 대해 키 값과 값을 확인하는 함수를 두 개 받는다. 

``` java
 List<Person> pList = new ArrayList<>();

pList.add(new Person("Kim", 20));
pList.add(new Person("Na", 21));
pList.add(new Person("Park", 22));
pList.add(new Person("Lee", 23));

Map<String, Integer> pMap = 
			pList
			.stream()
			.collect(toMap(Person::getName, 
							Person::getAge));
```

키를 생성하는 함수와 값을 생성하는 함수 두 가지가 존재한다. 
하지만 이 형태의 메소드는 키 값이 중복된다면 ```IllegalStateException```이 발생되며 종료된다. 

``` java
List<Person> pList = new ArrayList<>();

pList.add(new Person("Kim", 20));
pList.add(new Person("Na", 21));
pList.add(new Person("Park", 22));
pList.add(new Person("Lee", 23));
pList.add(new Person("Lee", 30));

Map<String, Integer> pMap = 
			pList
			.stream()
			.collect(toMap(Person::getName, 
							Person::getAge, 
							(oldVal, newVal) -> Math.max(oldVal, newVal)));
```

두 번째 메소드는 병합 함수를 넘겨 충돌을 핸들링한다. 
큰 값을 취하도록 하여 ```pMap```의 "Lee" 키 값에는 30이 할당된다. 

``` java
List<Person> pList = new ArrayList<>();

pList.add(new Person("Kim", 20));
pList.add(new Person("Na", 21));
pList.add(new Person("Park", 22));
pList.add(new Person("Lee", 23));
pList.add(new Person("Lee", 30));

Map<String, Integer> pMap = 
			pList
			.stream()
			.collect(toMap(Person::getName, 
							Person::getAge, 
							(oldVal, newVal) -> Math.max(oldVal, newVal),
							HashMap::new));
```

마지막으로 mapSupplier은 ```HashMap```, ```TreeMap```, ```EnumMap``` 등 맵의 실제 구현체를 지정할 수 있다. 

<br/>

이번에는 ```groupingBy```를 알아보자. 이는 SQL의 ```GROUP BY``` 구절과 비슷하다. 
- ```groupingBy(Function classifier)```
- ```groupingBy(Function classifier, Collector downstream)```
- ```groupingBy(Function classifier, Supplier mapFactory, Collector downstream)```

``` java
List<Employee> eList = new ArrayList<>();

eList.add(new Employee("11111", "Marketing"));
eList.add(new Employee("22222", "Marketing"));
eList.add(new Employee("33333", "Accounting"));
eList.add(new Employee("44444", "Accounting"));
eList.add(new Employee("55555", "Marketing"));

Map<String, List<Employee>> eMap =
		eList.stream().collect(groupingBy(Employee::getDepartment));
```

가장 간단한 형태로는 ```classifier``` 함수만 넘기는 것이다. 
이는 부서명으로 그룹핑을하며 반환 값으로는 키가 부서명이고 값은 해당 부서에 속한 객체들이다.

``` java
List<Employee> eList = new ArrayList<>();

eList.add(new Employee("11111", "Marketing"));
eList.add(new Employee("22222", "Marketing"));
eList.add(new Employee("33333", "Accounting"));
eList.add(new Employee("44444", "Accounting"));
eList.add(new Employee("55555", "Marketing"));

Map<String, Long> eMap =
		eList.stream().collect(groupingBy(Employee::getDepartment,
											couting());
```

값을 객체 리스트가 아닌 다른 형태로 사용하기 위해서는 다운스트림을 전달하는 것이다. 
위 예에서는 ```counting()```을 전달하여 각 부서에 속한 직원의 수를 값으로 가지게 된다. 

``` java
List<Employee> eList = new ArrayList<>();

eList.add(new Employee("11111", "Marketing"));
eList.add(new Employee("22222", "Marketing"));
eList.add(new Employee("33333", "Accounting"));
eList.add(new Employee("44444", "Accounting"));
eList.add(new Employee("55555", "Marketing"));

Map<String, Long> eMap =
		eList.stream().collect(groupingBy(Employee::getDepartment,
											LinkedHashMap::new,
											counting()));
```

마지막으로는 ```toMap```과 같이 실질적인 구현체를 지정해주는 것이다. 

<br/>

또 다른 예시는 ```joining```이다. 
이 메서드는 ```CharSequence``` 인스턴스의 스트림에만 적용할 수 있다. 
- ```joinging()```
- ```joinging(CharSequence delimiter)```
- ```joinging(CharSequence delimiter, CharSequence prefix, CharSequence suffix)```

``` java
List<String> cList = new ArrayList<>();

cList.add("RED");
cList.add("GREEN");
cList.add("BLUE");

String result = cList.stream().collect(joining());
System.out.println(result);
```

파라미터가 없는 첫 번째 함수는 그냥 단순히 문자열을 이어 붙인 결과인 "REDGREENBLUE"를 반환한다.

``` java
List<String> cList = new ArrayList<>();

cList.add("RED");
cList.add("GREEN");
cList.add("BLUE");

String result = cList.stream().collect(joining(", "));
System.out.println(result);
```

구분자인 ```delimiter```을 주면 그에 따라 "RED, GREEN, BLUE"를 반환한다.

``` java
List<String> cList = new ArrayList<>();

cList.add("RED");
cList.add("GREEN");
cList.add("BLUE");

String result = cList.stream().collect(joining(", ", "[", "]"));
System.out.println(result);
```

그리고 마지막으로 ```prefix```와 ```suffix```를 지정하여 앞뒤에 추가적인 처리를 할 수 있다. 이는 "[RED, GREEN, BLUE]"를 반환한다.