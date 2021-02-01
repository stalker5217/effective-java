## 비검사 경고를 제거하라  

제네릭 사용에는 많은 Warning이 발생할 수 있다. 
물론 정말 잘못된 구문에 의해 발생할 수도 있지만 안전한 구문이라면 그 이유를 주석으로 남기고 경고를 제거해야 한다. 

예를 들어, 타입이 안전하다고 확신할 수 있다면 ```@SuppressWarnings("uncheked")``` 로 경고를 숨길 수 있다. 
그리고 이 애노테이션은 변수의 선언, 메소드, 클래스 단위로 적용할 수 있는데 가능한 좁은 범위로 적용시키는 것이 좋다. 

``` java
public <T> T[] toArray(T[] a){
	if(a.length < size) {
		@SuppressWarnings("unchecked") T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
		return result;
	}

	System.arraycopy(elements, 0, a, 0, size);
	
	if(a.length > size) a[size] = null;
	return a;
}
```