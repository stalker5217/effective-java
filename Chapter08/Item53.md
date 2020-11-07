## 가변인수는 신중히 사용하라  

먼저 가변인수(varargs)란 명시한 타입의 인수를 0개 이상 받을 수 있는 기법을 말한다. 
가변인수가 포함된 메소드를 호출하면 인수의 길이만큼 배열을 만들어 메소드에 전달해 준다.

``` java
class Test{
	public static void main(String[] args) {
		System.out.println(sum(1, 2, 3));
	}

	public static int sum(int... args){
		int sum = 0;
		for(int arg : args) sum += arg;

		return sum;
	}
}
```

그런데 메소드의 동작에 따라 제약이 있을 수 있다. 
예를 들면 최소 값을 반환하는 메소드 같은 경우에는 최소한 하나 이상의 파라미터가 있어야 한다.

``` java
public static int min(int... args){
	if(args.length == 0) throw new IllegalArgumentException("인수가 1개 이상 필요합니다");

	int min = args[0];
	for(int i = 1 ; i < args.length ; i++){
		min = Math.min(min, args[i]);
	}

	return min;
}
```

위 소스의 문제점은 변수를 하나도 포함하지 않고 호출했을 때 컴파일 타임이 아닌 런타임에 에러가 잡힌다는 것이고, 
딱 봐도 코드가 좀 더러워보인다는 것이다. 이러한 부분은 일반 파라미터로 최소 하나 이상을 받도록 하면 된다.  

``` java
public static int min(int first, int... args){
	if(args.length == 0) throw new IllegalArgumentException("인수가 1개 이상 필요합니다");

	int min = first;
	for(int arg : args) min = Math.min(min, arg);

	return min;
}
``` 

그리고 가변인수는 호출 시 정의한 파라미터를 배열로 만든다고 했다. 
이러한 부분은 불필요한 오버헤드가 발생할 수 있다. 제거할 수 있다면 제거하는 것이 좋다.  

만약 가변 인수로 정의된 메소드가 95%는 3개 이하의 파라미터만 넘어오고, 나머지 5%만이 그보다 많은 파라미터를 갖는다고 하자.
그러면 3개의 파라미터까지는 일반 변수를 통해서 받도록 오버로딩을 통해 최적화 할 수 있다.

``` java
public void foo() {}
public void foo(int a) {}
public void foo(int a, int b) {}
public void foo(int a, int b, int c) {}
public void foo(int a, int b, int c, int... rest) {}
```