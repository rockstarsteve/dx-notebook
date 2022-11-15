# validation



## 依赖

```
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>


<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.2.0.Final</version>
</dependency>
```
## 封装工具类（要根据不同情况优化！）
```
import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import java.util.*;

/**
 * Description:
 *
 * @author rockstarsteve
 * @version 1.0
 * @copyright Copyright (c) 电信
 * @since 2022/11/14
 */
public class BeanValidator {

    private static ValidatorFactory validatorFactory = Validation.buildDefaultValidatorFactory();

    /**
     * 分组校验，并返回错误map
     *
     * @param t      校验的对象
     * @param groups 分组对象
     * @return
     */
    public static <T> Map<String, String> validate(T t, Class... groups) {
        Validator validator = validatorFactory.getValidator();
        Set validateResult = validator.validate(t, groups);
        //如果为空
        if (validateResult.isEmpty()) {
            return Collections.emptyMap();
        } else {
            //不为空时表示有错误
            LinkedHashMap errors = new LinkedHashMap();
            //遍历
            Iterator iterator = validateResult.iterator();
            while (iterator.hasNext()) {
                ConstraintViolation violation = (ConstraintViolation) iterator.next();
                errors.put(violation.getPropertyPath().toString(), violation.getMessage());
            }
            return errors;
        }
    }

    /**
     * 分组校验并抛出异常
     *
     * @param t
     * @throws RuntimeException
     */
    public static <T> void checkObject(T t, Class... groups) throws RuntimeException {
        Map<String, String> map = BeanValidator.validate(t, groups);
        //如果错误集合map不为空则抛出异常
        if (map != null) {
            throw new RuntimeException(map.toString());
        }
    }

}
```



## 常见注解

```
基础的：
@Null 被注释的元素必须为 null
@NotNull 被注释的元素必须不为 null
@AssertTrue 被注释的元素必须为 true
@AssertFalse 被注释的元素必须为 false
@Min(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@Max(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@DecimalMin(value) 被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@DecimalMax(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@Size(max=, min=) 被注释的元素的大小必须在指定的范围内
@Digits (integer, fraction) 被注释的元素必须是一个数字，其值必须在可接受的范围内
@Past 被注释的元素必须是一个过去的日期
@Future 被注释的元素必须是一个将来的日期
@Pattern(regex=,flag=) 被注释的元素必须符合指定的正则表达式
 
Hibernate Validator提供的校验注解：
@NotBlank(message =) 验证字符串非null，且长度必须大于0
@Email 被注释的元素必须是电子邮箱地址
@Length(min=,max=) 被注释的字符串的大小必须在指定的范围内
@NotEmpty 被注释的字符串的必须非空
@Range(min=,max=,message=) 被注释的元素必须在合适的范围内
```



## 建议





## 知识点

> validation-api是一套标准，hibernate-validator实现了此标准

JSR-303 是Java EE 6 中的一项子规范，叫做BeanValidation，官方参考实现是hibernate-validator。

hibernate-validator实现了JSR-303规范

```
@Validated org.springframework.validation.annotation.Validated jar包：spring-context
@Valid javax.validation.Valid jar包：javax.validation
```

> Spring Validation验证框架对参数的验证机制提供了@Validated（Spring's JSR-303规范，是标准JSR-303的一个变种），javax提供了@Valid（标准JSR-303规范），配合BindingResult可以直接提供参数验证结果。其中对于字段的特定验证注解比如@NotNull等网上到处都有，这里不详述

在检验Controller的入参是否符合规范时，使用@Validated或者@Valid在基本验证功能上没有太多区别。但是在分组、注解地方、嵌套验证等功能上两个有所不同：

1. 分组

@Validated：提供了一个分组功能，可以在入参验证时，根据不同的分组采用不同的验证机制，这个网上也有资料，不详述。**@Valid：作为标准JSR-303规范，还没有吸收分组的功能。**

1. 注解地方

@Validated：可以用在类型、方法和方法参数上。但是不能用在成员属性（字段）上

@Valid：可以用在方法、构造函数、方法参数和成员属性（字段）上

两者是否能用于成员属性（字段）上直接影响能否提供嵌套验证的功能。

1. 嵌套验证

@Valid可以加在子类上，进行嵌套验证









