> IoC 好莱坞原则，不要打电话给我，我来找你
>
> IoC 控制反转，一种设计原则

## IoC 容器的职责

- **依赖处理：**依赖查找、依赖注入
- **生命周期管理：**容器、托管的资源(Java Beans 或其他资源，例如：事件、线程池) 
- **配置：**容器、外部化配置、托管的资源(Java Beans 或其他资源)

## IoC 容器的实现

- Java SE：
  - Java Beans、
  - Java ServiceLoader SPI、
  - JNDI(Java Naming and Directory Interface)

- Java EE：
  - EJB(Enterprise Java Beans)、
  - Servlet

- 开源：
  - Apache Avalon(http://avalon.apache.org/closed.html)
  - PicoContainer(http://picocontainer.com/)
  - Google Guice(https://github.com/google/guice)
  - Spring Framework(https://spring.io/projects/spring-framework)

## 传统IOC容器实现

Java Beans 作为 IoC 容器 

- 特性：依赖查找、生命周期管理、配置元信息、事件、自定义、资源管理、持久化

- 规范：
  - JavaBeans:https://www.oracle.com/technetwork/java/javase/tech/index-jsp-138795.html
  - BeanContext:https://docs.oracle.com/javase/8/docs/technotes/guides/beans/spec/beancontext.html





## 面试题

### 问：什么是IOC？

简单地说，IoC 是反转控制，类似于好莱坞原则，主要有依赖查找 和依赖注入实现

### 问：依赖查找和依赖注入的区别？

依赖查找是主动或手动的依赖查找方式，通常需要依赖容器或标准 API 实现。

而依赖注入则是手动或自动依赖绑定的方式，无需依赖特定的容器和 API

### 问：Spring 作为 IoC 容器有什么优势?

 典型的 IoC 管理，依赖查找和依赖注入 AOP 抽象
 事务抽象
 事件机制
 SPI 扩展
 强大的第三方整合
 易测试性
 更好的面向对象    模板方法模式、代理模式、适配器模式、责任链模式、观察者模式

