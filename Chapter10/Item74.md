## 메서드가 던지는 모든 예외를 문서화하라  

검사 예외는 모두 javadocs의 ```@throws``` 태그를 통해서 문서화하자. 
그리고 예외는 모두 구분해서 작성하자. 
예를 들어 그냥 ```Exception```으로 퉁쳐버리는 것은 지양하자. 
예외 발생 시 이는 개발자의 사용성을 크게 떨어뜨린다. 

만약 모든 메서드에서 공통으로 발생하는 ```NullPointerException``` 등은 클래스 단위로 문서를 작성할 수도 있다.