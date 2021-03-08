# Spring Bean 生命周期

## 1、Spring Bean 元信息配置阶段

## 2、Spring Bean 元信息解析阶段

- 面向资源：XML配置、Properties资源配置 XmlBeanDefinitionReader、PropertiesBeanDefinitionReader 传统的BeanDefinition读取器，BeanDefinitionReader 的实现类
- 面向注解：AnnotatedBeanDefinitionReader，这是一个独立的BeanDefinition读取器，没有超类。
- 面向API：

## 3、Spring Bean 注册阶段

BeanDefinitionRegistry

## 4、Spring Bean Definition 合并阶段

继承配置，复用配置，减少重复性的配置工作，很多框架都支持这种特性



RootBeanDefinition 不需要合并
GenericBeanDefinition

## 5、Spring Bean Class加载阶段

6、Spring Bean 实例化前阶段

7、Spring Bean 实例化阶段

8、Spring Bean 实例化后阶段

9、Spring Bean 属性赋值阶段

10、Spring Bean Aware接口回调阶段

11、Spring Bean 初始化前阶段

12、Spring Bean 初始化阶段

13、Spring Bean 初始化后阶段

14、Spring Bean 初始化完成阶段

15、Spring Bean 销毁前阶段

16、Spring Bean 销毁阶段

17、Spring Bean 垃圾收集

