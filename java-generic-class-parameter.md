# Extract generic parameters from class in Java

Java, 특히 Spring을 처음 쓰면서 DI 병이라도 걸렸나,
아직 적은 양이긴 하지만 코드를 DI 스럽게 짜려고 약간 집착하고 있다.

오늘은 클래스의 generic 파라미터까지 알아내서 이용하고 싶어서
방법이 없을까.. 하고 찾아보다가 알게 된 것들을 살짝 정리해 보았다.

이런 인터페이스와 이걸 구현한 클래스가 있다고 하자.

```java
public interface GenericInterface<? extends Object,? extends Object> { /* ... */ }
public class GenericImpl1 implements GenericInterface<Integer, Double> { /* ... */ }
public class GenericImpl2 implements GenericInterface<Float, Character> { /* ... */ }
```

이제 `GenericImpl1`과 `GenericImpl2`를 입력에 따라 자동으로 생성해주는 팩토리를 만들어 보자.

```java
public class GenericFactory {
    private List<Pair<Class, List<Class>>> impls;

    private List<Class> getGenericParams(Class clazz) {
        // ????
    }

    @Inject
    public void registerImpls(List<Class<? extends GenericInterface>> impls) {
        impls = new ArrayList<>();
        for (Class<? extends GenericInterface> clazz: impls) {
            impls.add(new Pair(clazz, getGenericParams((Class) clazz)));
        }
    }

    private boolean matchGenericParams(List<Class> classes1, List<Class> classes2) {
        for (int i = 0; i < classes1.size(); i++) {
            if (!classes1.get(i).equals(classes2.get(i)))
                return false;
        }
        return classes1.size() == classes2.size();
    }

    private Class findImplByTypes(List<Class> classes) throws ClassNotFoundException {
        for (Pair p: impls) {
            if (matchGenericParams((List<Class>) p.getValue(), classes))
                return (Class) p.getKey();
        }
        throw new ClassNotFoundException();
    }

    public GenericInterface createGenericImpl(Object[] params, Class[] classes) throws ClassNotFoundException {
        return findImplByTypes(classes).getDeclaredConstructor(classes).newInstance(params);
    }
}
```

다른 부분은 그래도 대충 알겠는데, `getGenericParams` 부분은 어떻게 작성해야 할까?
사실 Constructor를 얻는 것과 비슷하게 해결 가능한데, 처음에는 [ParameterizedType][]을 몰라서 헤맸었다.

```java
private List<Class> getGenericParams(Class clazz) {
    List<Class> genericParams = new ArrayList<>();
    for (Type T: clazz.getGenericInterfaces()) {
        if (T instanceof ParameterizedType) {
            for (Type A: ((ParameterizedType) T).getActualTypeArguments())
                genericParams.add((Class) A);
        }
    }
    return genericParams;
}
```

항상 `Type`으로 리턴되어 이걸 이름 잘라서 `Class.forName`이라도 불러야 하나.. 했는데,
알고 보니 이렇게 번듯한 서브클래스였다.
"이래서 사람이 공부를 해야 하는구나" 를 절실히 느꼈다 ㅜㅜ...

[ParameterizedType]: https://docs.oracle.com/javase/10/docs/api/java/lang/reflect/ParameterizedType.html
