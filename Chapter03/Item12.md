## toString을 항상 재정의하라  

``` java
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix   = rangeCheck(prefix,   999, "프리픽스");
        this.lineNum  = rangeCheck(lineNum, 9999, "가입자 번호");
    }

    public static void main(String[] args) {
        PhoneNumber jenny = new PhoneNumber(707, 867, 5309);
        System.out.println("제니의 번호: " + jenny);
    }
}
```  

모든 클래스는 ```Obejct``` 클래스를 상속하고 ```Object``` 클래스에는 ```toString``` 메소드가 있다. 
하지만 작성한 클래스의 ```toString```를 호출했을 때 적합한 문자열을 반환하는 경우는 거의 없다. 
위 코드의 경우 전화번호를 반환하기를 기대하지만 실제 출력되는 것은 '클래스_이름@16진수_해시코드'를 반환할 뿐이다. 

```toString```을 항상 재정의하자. 
```println```, 문자열 연결 연산, assert 등 ```toString```이 호출되는 형태는 직접 사용하지 않더라도 매우 많다. 
이는 간결하면서도 사람이 읽기 쉬운 형태의 유익한 정보를 반환해야하며, 
그 객체가 가지고 있는 주요 정보들을 모두 반환하는 것이 좋다. 

``` java
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix   = rangeCheck(prefix,   999, "프리픽스");
        this.lineNum  = rangeCheck(lineNum, 9999, "가입자 번호");
    }

	/**
     * 이 전화번호의 문자열 표현을 반환한다.
     * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
     * XXX는 지역 코드, YYY는 프리픽스, ZZZZ는 가입자 번호다.
     * 각각의 대문자는 10진수 숫자 하나를 나타낸다.
     *
     * 전화번호의 각 부분의 값이 너무 작아서 자릿수를 채울 수 없다면,
     * 앞에서부터 0으로 채워나간다. 예컨대 가입자 번호가 123이라면
     * 전화번호의 마지막 네 문자는 "0123"이 된다.
     */
   @Override public String toString() {
       return String.format("%03d-%03d-%04d", areaCode, prefix, lineNum);
   }

    public static void main(String[] args) {
        PhoneNumber jenny = new PhoneNumber(707, 867, 5309);
        System.out.println("제니의 번호: " + jenny);
    }
}
```  