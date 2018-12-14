# Java String

자바에서 다음 코드를 실행하면 결과로 [각각 4와 3이 나온다][repl-java].

```java
System.out.println("🇰🇷".length());
System.out.println("태극기".length());
```

```python
>>> print repr(u'🇰🇷'), repr(u'태극기')
u'\U0001f1f0\U0001f1f7' u'\ud0dc\uadf9\uae30'
>>> print len(u'🇰🇷'), len(u'태극기')
2 3
```

UTF-8 기준으로는 이렇게 2와 3이 나올텐데, 도대체 4와 3은 어떻게 계산해야 나오는 값일까?
[Java 매뉴얼][java-unicode]를 살펴보면 답을 찾을 수 있는데,
> The Java platform uses the UTF-16 representation in char arrays and in the String and StringBuffer classes.

놀랍게도 Java의 `char(char[])`와 `String`, 그리고 `StringBuffer`가 내부적으로 UTF-16을 사용하기 때문이라고 한다.
Java에서 String을 UTF-8로 인코딩하기 위해서는 `StandardCharsets.UTF_8.encode()`를 이용하면 된다(`ByteBuffer`가 리턴된다; [StandardCharsets][java-stdcset], [Charset][java-charset] 참고).

[repl-java]: https://repl.it/repls/TealRustyUnit
[java-unicode]: https://docs.oracle.com/javase/10/docs/api/java/lang/Character.html#unicode
[java-stdcset]: https://docs.oracle.com/javase/10/docs/api/java/nio/charset/StandardCharsets.html#field.summary
[java-charset]: https://docs.oracle.com/javase/10/docs/api/java/nio/charset/Charset.html#encode%28java.nio.CharBuffer%29
