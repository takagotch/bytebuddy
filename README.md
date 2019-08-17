### bytebuddy
---
http://bytebuddy.net/#/

https://github.com/raphw/byte-buddy

```java
Class<?> dynamicType = new ByteBuddy()
  .subclass(Object.class)
  .method(ElementMatchers.named("toString"))
  .intercept(FixedValue.value("Hello World!"))
  .make()
  .load(getClass().getClassLoader())
  .getLoaded();
assertThat(dynamicType.newInstance().toString(), is("Hello World"));

public class GreetingInterceptor {
  public Object greet(Object argument) {
    return "Hello from " + argument;
  }
}

class<? extends java.utils.function.Function> dynamicType = new ByteBuddy()
  .subclass(java.util.function.Function.class)
  .method(ElementMatchers.named("apply"))
  .intercept(MethodDelegation.to(new GreetingInterceptor()))
  .make()
  .load(getClass().getClassLoader())
  .getLoaded();
assertThat((String) dynamicType.newInstance().apply("Byte Buddy"), is("Hello from Byte Buddy"));

public class GeneralInterceptor {
  @RuntimeType
  public Object intercept(@AllArguments Object[] allArguments,
      @Origin Method method) {
    //  
  }
}

public class TimingInterceptor {
  @RuntimeType
  public static Object intercept(@Origin Method method,
      @SuperCall Callable<?> callable) {
    long start = System.currentTimeMillis();
    try {
      return callable.call();
    } finally {
      System.out.println(method + " took " + (System.currentTimeMillis() - start));
    }
  }
}

public class TimerAgent {
  public static void premain(String arguments,
      Instrumentation instrumentation) {
    new AgentBuilder.Default()
      .type(ElementMatchers.nameEndsWith("Time"))
      .transform((builder, type, classLoader, module) ->
        builder.method(ElementMatchers.any())
          .intercept(MethodDelegation.to(TimingInterceptor.class))
      ).installOn(instrumentation);
  }
}
```

```sh
mvn package
```

```
```
