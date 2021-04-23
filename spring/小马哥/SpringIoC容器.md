# Spring注解

## spring注解编程模型

#### 模式注解

@Repository	数据仓储模式注解

@Component	通用组件模式注解

@Service	服务模式注解

@Controller	web控制器模式注解

@Configuration	配置类模式注解

#### 装配注解

@ImportResource	替换xml元素<import>

@Import	导入COnfiguration类

@ComponentScan	扫描指定package下的标注spring模式注解的类

#### 依赖注入注解

@Autowired	Bean依赖注入，支持多种依赖查找方式

@Qualifier	细粒度的@Autowired依赖查找

#### 编程模型

元注解：标注在注解上的注解，可以使载体注解更加立体，但遗憾是元注解标注上去就是硬编码，写死了，载体注解投放到代码各处使用时，按照传统的规范是没哟办法对元注解做二次定义的。spring对这种情况作了扩展。

- java.lang.annotation.Target
- java.lang.annotation.Retention
- java.lang.annotation.Inherited
- java.lang.annotation.Documented
- java.lang.annotation.Repeatable



模式注解：Component 注解的一系列派生

Spring组合注解：

Spring注解属性别名和覆盖

