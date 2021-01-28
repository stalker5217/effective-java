## 비트 필드 대신 EnumSet을 사용하라  

어떤 enum 값들을 집합으로 사용할 때 흔히 비트를 사용해 많이 사용한다. 

``` java
public class Text{
	public static final int STYLE_BOLD = 1 << 0;
	public static final int STYLE_ITALIC = 1 << 1;
	public static final int STYLE_UNDERLINE = 1 << 2;
	public static final int STYLE_STRIKETHROUGH = 1 << 3;

	public int styles;

	public void applyStyles(int styles){
		this.styles |= styles;
	}
}
```

``` java
text.applyStyles(STYLE_BOLD | STYLE_ITALIC);
```

근데 비트를 사용하는 것은 단점이 있다. 
먼저 단순히 비트 값으로는 해석하기 어렵고 순회하는 것도 어렵다. 
또한 ```int```나 ```long```에 종속되어 필드 값이 한정되어 있다. 

이를 해결해줄 ```java.util.EnumSet```을 제공한다. 
```Set``` 인터페이스를 구현하며 내부적으로는 비트 벡터를 사용하여 구현하여 이미 제공하고 있다. 

``` java
public class Text{
	public static enum Style{
		BOLD, ITALIC, UNDERLINE, STRIKETHROUGH
	}

	public void applyStyles(Set<Style> styles){
		this.styles.addAll(styles);
	}
}
```

``` java
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```