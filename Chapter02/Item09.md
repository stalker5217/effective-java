## try-finally 보다는 try-with-resources를 사용하라  

InputStream, OutputStream, java.sql.Connection 등 자바에는 ```close```로 직접 해제해줘야 하는 자원들이 많다. 
이러한 부분을 놓치다 보면 결국 예기치 못한 문제로 이어지는데, 이를 방지하기 위해 많이 try-finally 구문을 사용하고는 한다. 

전통적으로 자원의 완전한 해제를 위해 많이 사용하던 코드는 다음과 같다.

``` java
static String fistLineOfFile(String path) throws IOException{
	BufferReader br = new BufferReader(new FileReader(path));
	try{
		return br.readLine();
	}
	finally{
		br.close();
	}
}
```  

``` java
// 자칫하다가는 이렇게 흉한 코드가 되버린다.
static void copy(String src, String dst) throws IOException{
	InputStream in = new FileInputStream(src);

	try{
		OutputStream out = new FileOutputStream(dst);

		try{
			byte[] buf = new byte[BUFFER_SIZE];
			int n;
			while((n = in.read(buf)) >= 0) out.write(buf, 0, n);
		}
		finally{
			out.close();
		}
	}
	finally{
		in.close();
	}
}
```  

첫 번째 메소드 같은 경우에는 결점이 있다. 
만약 단순히 try 구문에서의 예외가 아니라 근본적인 문제가 발생해서 
```readLine``` 에서도 예외가 발생하고 finally의 ```close```에서도 예외가 발생한다고 가정하자. 
그러면 ```close```에서 발생한 두 번째 예외가 첫 번째 예외를 완전히 삼켜버린다. 
Stack Trace 상에 첫 번째 예외에 관한 에러 로그를 추적하기가 쉽지 않게 되고 이는 디버깅을 힘들게한다. 

Java 7에서 부터는 try-with-resources 구문을 지원하며 이러한 문제를 비롯해 가독성도 높일 수 있게 되었다. 
이 구조를 사용하기 위해서는 AutoCloseable Interface를 implement하여 ```close``` 메소드를 구현해야 하는데, 
만약 사용 후 자원을 반환해야하는 클래스를 작성한다면 이를 꼭 implement하도록 하자.

``` java
// 위 예시처럼 readLine, close 모두 예외가 발생해도 readLine이 예외로 잡혀 추적하기 쉽다.
static String fistLineOfFile(String path) throws IOException{
	try(BufferReader br = new BufferReader(new FileReader(path))){
		return br.readLine();
	}
}
``` 

``` java
static void copy(String src, String dst) throws IOException{
	try(InputStream in = new FileInputStream(src); OutputStream out = new FileOutputStream(dst);){
		byte[] buf = new byte[BUFFER_SIZE];
		int n;
		while((n = in.read(buf)) >= 0) out.write(buf, 0, n);
	}
}
```