## Spring bean定义覆盖问题

先说下springbean覆盖的现象，然后再说下项目遇到的问题。讨论的前提条件是单例。

我们都知道spring 容器中的相同name的bean只会存在一份，在扫面注册BeanDefinition的时候就已经体现了。

`org.springframework.beans.factory.support.DefaultListableBeanFactory`

`Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);`

### 补充个知识点: bean的默认名称：

- Xml配置：com.shenge.study.beanLifecycle.User#0

- @Component：user
- @Bean：

下面通过用例分析下覆盖规则：

#### 案例1:同一个xml文件

```xml
<bean id="user" class="com.shenge.study.beanLifecycle.User"></bean>
<bean id="user" class="com.shenge.study.beanLifecycle.UserChild"></bean>
```

这种写法无法正确解析Xml文件，抛出下面的异常：

org.springframework.beans.factory.parsing.BeanDefinitionParsingException: 

Configuration problem: Bean name 'user' is already used in this <beans> element

Offending resource: class path resource [META-INF/spring-bean.xml]

#### 案例2:两个xml文件，通过import链接

```xml
<!-- 第一个xml -->
<bean id="user" class="com.shenge.study.beanLifecycle.User"></bean>
<import resource="spring-bean-spec.xml"/>

<!-- 第二个xml -->
<bean id="user" class="com.shenge.study.beanLifecycle.UserChild"></bean>
```

正常解析，import是后置导入，第二个会覆盖第一个的配置，

#### 案例3:两个xml文件，通过import链接

```xml
<!-- 第一个xml -->
<import resource="spring-bean-spec.xml"/>
<bean id="user" class="com.shenge.study.beanLifecycle.User"></bean>

<!-- 第二个xml -->
<bean id="user" class="com.shenge.study.beanLifecycle.UserChild"></bean>
```

正常解析，import是前置导入，第一个会覆盖第二个的配置，也就是说真实顺序先解析了xml2，然后继续解析xml1

#### 案例4:两个xml文件，没有关联

```java
ClassPathXmlApplicationContext context =
      new ClassPathXmlApplicationContext(
            "META-INF/spring-bean1.xml",
  		"META-INF/spring-bean2.xml");
```

正常解析，第二个会覆盖第一个的配置，

### 结论：

**处在不同的xml配置文件，靠后的BeanDefinition会覆盖前面的BeanDefinition，最终实例化、初始化的就是最后面的bean。**

### 工作中遇到的问题场景

框架中的mvc-dispatcher-servlet.xml，mvc-dispatcher-servlet-spec.xml是应用开发中可以自定义的配置，通过import关联。

```xml
<import resource="mvc-dispatcher-servlet-spec.xml"/>
<beans profile="local,test">
    <bean class="suishen.webx.swagger.config.BaseSwaggerConfig" id="swaggerConfig"/>
</beans>
```

这种情况，mvc-dispatcher-servlet-spec.xml中如果期望配置id=“swaggerConfig”类是无法生效的，最终都会被覆盖掉。

### 解决方案

mvc-dispatcher-servlet-spec.xml作为定制化的配置文件，应该放在最后import，提高自定义配置的优先级