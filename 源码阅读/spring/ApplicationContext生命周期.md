

@ComponentScan  开启扫描

子类实例化时，如果没有制定调用父类的构造器方法，将自动调用默认的无参构造器

AnnotationConfigUtils  工具类

AnnotationConfigUtils.attributesFor(amd, type);



ClassUtils 工具类



spring bean 默认类名组合拳：

- ClassUtils.getShortName(beanClassName); 全类名的简写

- Introspector.decapitalize(shortClassName); 首字母小写



EnvironmentAware、EmbeddedValueResolverAware、ResourceLoaderAware、ApplicationEventPublisherAware、MessageSourceAware、ApplicationContextAware

BeanNameAware、BeanFactoryAware、BeanClassLoaderAware



