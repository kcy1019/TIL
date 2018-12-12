# `ALLOW_ENCODED_SLASH`

URL의 파라미터에 slash가 들어가는 것을 허용하는 Tomcat의 설정 플래그.
Jenkins에 reverse proxy를 붙이는 중에 알게 되었다. (`역방향 프록시 설정이 잘못된 것으로 파악되었습니다.` 에러)
아마 directory traversal 공격과 같은 문제를 막기 위해 기본적으로 꺼 둔 것 같다.
Tomcat에 국한된 문제는 아니고, 다른 프레임웍도 이런 경우가 많으니 주의! (e.g. Apache HTTPD)

### Tomcat 에서 전역으로 설정하는 방법

`catalina.properties`에 `org.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH=true` 한 줄을 추가

### Spring Boot App에서 설정하는 방법

```java
// SpringApplication.run() 호출 위에 한 줄을 추가
System.setProperty("org.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH", "true");
```
