## 지역변수의 범위를 최소화하라  

지역변수의 유효 범위를 최소로 줄이면 가독성과 유지보수가 쉬워지고 오류 가능성이 줄어든다. 

스코프를 줄이는 방법
1. 가장 처음 쓸 때 선언한다.
2. 선언과 동시에 초기화를 한다. 초기화에 필요한 정보가 충분하지 않다면 선언을 미룬다.
3. 메소드 크기를 가능한 작게 유지하고 한 가지 기능에 집중한다.
4. 반복문 내에서만 사용하는 변수라면 while보다는 for문을 사용한다.

``` java
// Iterator로 작업을 한다고 가정한다.

Iterator<Element> i = c.iterator();
while(i.hasNext()){
	doSomething(i.next());
}

Iterator<Element> i2 = c.iterator();
while(i.hasNext()){
	doSomething(i2.next());
}
```  

반복문 바깥에서 선언했기 때문에 반복문이 끝나도 변수가 살아있다. 
두 번째 반복문에서 실수로 잘못 접근을 한다고 하면, 이는 컴파일러가 잡아낼 수 없다. 
하지만 for문을 사용한다면 내부에서만 존재하기에 컴파일러 선에서 구별 가능하다.

``` java
for(Iterator<Element> i = c.iterator() ; i.hasNext() ; ){
	doSomethin(i.next());
}

for(Iterator<Element> i2 = c.iterator() ; i.hasNext() ; ){
	doSomethin(i2.next());
}
```