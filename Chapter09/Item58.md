## 전통적인 for문보다는 for-each문을 사용하라  

'Enhanced for statement', for-each문은 반복자와 인덱스를 사용하지 않으니 코드가 깔끔해지고 오류가 날 일이 없다. 
혹시 성능 상의 이슈에 대해 걱정이 있다면 Collection가 됐든 Array가 됐든 최적화과 되어있기 때문에 문제 없다.

``` java
for(Element e : elements){
	doSomething(e);
}
```

하지만 제한적인 상황에서 for-each문을 사용할 수 없는 경우도 존재한다.
- Destructive filtering : 원소를 제거하기 위해서는 반복자의 ```remove```나 인덱스를 통해 삭제해야 한다.
- Transforming : 원소의 값을 수정할 때는 인덱스나 반복자를 통한 접근이 필요하다.
- Parallel iteration : 병렬적으로 순회해야 한다면 각각 반복문에서 인덱스나 반복자를 통해 엄격하게 제어해야 한다.