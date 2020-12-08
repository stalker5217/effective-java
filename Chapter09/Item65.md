## 리플렉션보다는 인터페이스를 사용하라  

리플렉션을 사용하면 런타임에 임의의 클래스의 생성자, 메서드, 필드 등의 정보에 접근할 수 있으며, 
나아가 이들을 조작할 수도 있다.  

하지만 리플렉션에는 치명적인 단점들이 존재한다. 
일반적인 애플리케이션 개발에는 사용할 일이 거의 없으며 아래와 같은 단점을 가진다.

- 컴파일타임 타입 검사가 주는 이점을 하나도 누릴 수 없으며 런타임 에러에 노출된다.
- 코드가 지저분해지며 장황해진다.
- 성능이 많이 떨어진다.

코드 분석 도구(IDE, 테스트 도구 등)과 DI를 하는 프레임워크에서는 리플렉션은 사용하고 있지만, 
리플렉션의 단점이 명확하기 때문에 사용을 줄이고 있는 추세이다. 

리플렉션은 아주 제한된 형태로만 사용해야 그 단점을 피하고 이 점을 취할 수 있다. 
만약, 컴파일 타임 때 결정할 수 없는 클래스를 사용해야 하는 경우 
리플렉션은 인스턴스 생성에만 쓰고, 이렇게 만든 인스턴스는 인터페이스나 상위 클래스로 참조하여 사용한다. 

아래는 Set의 구현 클래스를 ```args```에서 받아 인스턴스를 생성하는 코드이며 리플렉션의 단점을 보여준다. 
런타임에 발생할 수 있는 수 많은 예외를 처리해야 하며, 인스턴스를 하나 생성하는 것 치고는 코드가 너무 난잡하다.

``` java
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.Arrays;
import java.util.Set;

class ReflectionTest{
    public static void main(String[] args){
        Class<? extends Set<String>> cl = null;
        try{
            cl = (Class<? extends Set<String>>) Class.forName(args[0]);
        }
        catch(ClassNotFoundException e){
            fatalError("클래스를 찾을 수 없음");
        }

        Constructor<? extends Set<String>> cons = null;
        try{
            cons = cl.getDeclaredConstructor();
        }
        catch(NoSuchMethodException e){
            fatalError("생성자를 찾을 수 없음");
        }

        Set<String> s = null;
        try{
            s = cons.newInstance();
        }
        catch (IllegalAccessException e) {
            fatalError("생성자에 접근할 수 없음");
        }
        catch (InstantiationException e) {
            fatalError("클래스를 인스턴스화 할 수 없음");
        }
        catch (InvocationTargetException e) {
            fatalError("생성자에서 예외 발생");
        }
        catch(ClassCastException e){
            fatalError("Set을 구현하지 않는 클래스임");
        }

        s.addAll(Arrays.asList(args).subList(1, args.length));
        System.out.println(s);
    }

    private static void fatalError(String msg){
        System.err.println(msg);
        System.exit(1);
    }
}
```