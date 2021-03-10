## 为什么需要学习spring？

1. 最大程度的简化了开发

   spring是一个非常优秀的java框架，其目标是为了简化java企业级开发，spring出来已经十几年了，这期间也一直围绕着这个目标在进行，像后面需要学习的springmvc、springboot、springcloud，这些技术也都是围绕着简化开发的目标在努力，到目前为止已经做的足够好了，可以说spring除了不能帮助我们实现业务逻辑代码之外，其他的事情spring都尽量去帮我们简化了，使用spring可以帮助我们节约大量开发时间。

   不使用spring的情况下，开发一个项目可能需要2个月，用了spring可能1个月都不需要，你说这样的技术你想学么？

2. 大量公司使用

   目前99%的公司使用了spring，可以去各大招聘网站看一下，spring算是必备技能，所以一定要掌握。

3. 顶尖的源代码

   spring框架源码设计非常优秀，在java开源项目中可以说是顶级的，目前为止还未发现比spring更优秀的开源项目，所以想提升代码能力的，强烈建议多看看spring的源码；关于提升代码能力的，还可以去看一下jdk的源码，也是非常棒的，里面有很多大师的杰作。

## 什么是spring？

spring是一个简化java企业级开发的一个框架，内部包含了很多技术，比如：控制反转&依赖注入、面向切面编程、spring事务管理、通过spring集成其他框架、springmvc、springboot、springcloud等等，这些都是围绕简化开发展开的技术，后面会对每个知识点详细介绍。

## 感受一下spring的强大

先让大家感受一下spring的强大，来激发大家学习的兴趣。

我们需要为移动端提供api接口，接口数据以json格式输出，使用java技术，实现过程大家应该比较熟悉，如下：

1. 使用idea创建一个web项目
2. 创建servlet，servlet中写业务代码，组装需要数据，将数据以json格式输出
3. 在web.xml中配置servlet信息
4. 将项目部署到tomcat中

原生的web项目开发过程都是上面这样，和业务相关的其实只有第2步，而其他几个步骤是每个web项目都需要的，像比较大的项目，会有很多web模块，每个模块都需要重复这些1/3/4操作步骤，还是相当繁琐的，也会耗费大量时间。

如果使用springboot来实现，过程会简化很多，来感受一下：

打开idea，点击`File->New->Project`，如下：

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xsuEFCkQv72NM3ZYAtW0icke2RDeNfcicupJ5v6YcibKfkdVvyY7vkL8cQ/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:70%;" />

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xsuEFCkQv72NM3ZYAtW0icke2RDeNfcicupJ5v6YcibKfkdVvyY7vkL8cQ/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:70%;" />

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xt6yUIhqMXia5ZjAZ7CcMiagWj9aEPNtstepQibicm7JuMyTjVZgfnrC8Jw/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:50%;" />

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xI4E8N5KQqmpqLlToAb3cvibhGWORQftTPcyg88gibZm0ScY5Mgu9kf5A/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:50%;" />

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xHx52aO3gMouVYY0NkAq9os6jCy0zl81St8Gq8GVpyMmowbTfCb9t0A/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:50%;" />![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xEXZQDqATyp2Nvq5aJZ9micTNTK7md484zfRHVPNLyTL9J2WWIc7zicvw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/xicEJhWlK06C6ovoYa42IB7kPW09mk58xEXZQDqATyp2Nvq5aJZ9micTNTK7md484zfRHVPNLyTL9J2WWIc7zicvw/640?wx_fmt=png&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="图片" style="zoom:50%;" />

目前为止我们还未写一行代码，下面我们创建一个类`HelloWorldController`，代码如下：

```java
package com.javacode2018;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/helloWorld")
public class HelloWorldController {
  @RequestMapping("/index")
  public String index() {
    return "hello world!";
  }
}
```

大家先不用关注项目中代码什么意思，主要关注一下效果。

下面我们来启动项目。

注意项目中有一个自动生成的类：`com.javacode2018.SpringDemo1Application`，这个类包含了main方法，说明是可以运行的，我们运行一下，打开浏览器，访问下面地址: http://localhost:8080/helloWorld/index

此时我们需要的功能已经实现了，然而到目前为止，只有`HelloWorldController`代码是我们写的，而我们没有去创建servlet、将返回的结果以json格式输出、配置web.xml，没有将项目发布到tomcat，没有去启动tomcat，而只启动了一个main方法，是不是非常神奇。创建servlet、通过response将数据以json格式输出、配置web.xml、发布项目到tomcat、启动tomcat等这些操作并不是说不需要，而是springboot内部已经帮我们实现了，对于开发者来说这些都被隐藏了，springboot让我们更关注业务代码的实现，从而最大限度的帮我们提升开发效率，简化项目的开发过程。

## 总结

这篇文章主要是让大家了解一下spring是什么，总的来说spring是一个非常优秀的java框架，这个框架的目标是为了简化企业级开发中需要用到的各种技术而存在的，后面的文章我们将一一介绍spring是如何做到简化项目开发的，如何让我们的项目更容易维护和扩展的，敬请期待！