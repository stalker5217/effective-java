## 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라

자바에서는 문제 상황을 알리는 throwable을 아래와 같이 세 개로 정의할 수 있다.
- Checked Exception(검사 예외)
- Runtime Exception(런타임 예외)
- Error(에러)

![throwable](/images/throwable.png)  

호출하는 쪽에서 복구하는 경우라면 Checked Exception을 사용한다. 
Checked Exception은 흔히 IDE에서 컴파일 이전에 잡히는 예외를 의미하며, 
이러한 예외는 호출자가 try-catch로 잡아 처리하거나 더 바깥으로 throw 하는 방식으로 처리된다.   

Unchecked throwable은 RuntimeException과 Error가 있다. 
이들은 통상적으로는 처리를 하지 않는다. 
이놈들이 발생했다는 것은 프로그램적으로 복구가 불가능하거나 처리하여 계속 실행해봤자 득보다 실이 많으며 발생 시 프로그램은 중단된다.  

Error 같은 경우에는 JVM에서의 자원 부족, 불변식 깨짐 등으로 더 이상 프로그램을 수행할 수 없을 때 발생하며, 
프로그래밍 오류를 나타날 때는 RuntimeException을 사용한다. ```ArrayIndexOutOfBoundsException``` 같은 것이 이에 해당한다.  

근데 사실 좀 애매한 부분이 있다. 
위에서는 이러한 예외를 통상적으로는 처리하지 않고 프로그램이 죽도록 내버려두라고 했는데 꼭 그래야만 하냐는 것이다.  
예를 들어, 자원 부족이 발생하는 경우에는 프로그래밍 실수로 터무니 없는 메모리를 요구하는 경우 발생할 수도 있고 
진짜로 트래픽이 갑자기 몰려 아주 일시적으로 발생할 수도 있다. 
어떤 경우에는 분명히 Checked Excpetion 처럼 처리가 가능하며, 이는 전적으로 프로그래머의 판단에 따른다.

> Error class는 프로그래머가 상속하여 새로운 에러를 정의하는 영역이 아니다
> 프로그래머가 구현하는 throwable은 모두 RuntimeException의 하위 클래스여야 한다. 
> Error를 상속한다거나 Throwable만을 상속해서 구현하는 짓은 하지 말도록 하자.