## 일반적으로 통용되는 명명 규칙을 따르라  

철자 규칙

|타입|특징|예|
|:---|:---|:---|
|Package & Module|dot(.)으로 구분하여 계층적으로 적는다. 또한 도메인의 역순으로 패키지를 표현한다.|org.junit.jupiter.api, com.google|
|Class & Interface|대문자로 시작한다|Stream, FutureTask, HttpClient|
|Method & Field|소문자로 시작한다|remove, groupingBy, getCrc|
|Constant Field|대문자로 구성한다|MIN_VALUE, NEGATIVE_INFINITY|
|Local Variable|문맥상에서 유추되는 경우에는 약어를 사용해도 된다|i, denom, houseNum|
|Type parameter|한 문자로 표현한다|T, E, K, V, X, R, ...|

<br/>

문법 규칙

|타입|특징|예|
|:---|:---|:---|
|Class|명사로 명명한다|Thread, Queue|
|Method|동사로 명명한다|append, draw|
|속성 반환|명사, 명사구, get으로 시작(boolean 제외)하는 동사로 명명한다|size, getTime|
|다른 타입 객체 반환|toType 형태로 짓는다|toString, toArray|