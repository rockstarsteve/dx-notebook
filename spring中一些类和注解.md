# spring中的类和注解的解释

### ApplicationRunner和CommandLineRunner的区别
两者作用是一样的，区别在与前者run方法参数为**ApplicationArguments\**\**对象**，是对原始参数做了封装，而后者为原始**String数组**。注：这些参数都是传递给main方法的参数。

```java
-------------------ApplicationRunner----------------------------
package org.springframework.boot;
@FunctionalInterface
public interface ApplicationRunner {
    void run(ApplicationArguments args) throws Exception;
}
-------------------CommandLineRunner----------------------------
package org.springframework.boot;

@FunctionalInterface
public interface CommandLineRunner {
    void run(String... args) throws Exception;
}
```

