

哈哈，题主掉到serializable lambda的坑里了。Java 8的lambda与method reference这坑埋得确实深。题主的问题可以用下面的代码来演示并讲解。其实主要差别不在于lambda与method reference，而是在于**捕获**与**非捕获**，而捕获的东西是否可以序列化：

` VoidFunction c1 = s -> System.out.println(s); // non-capturing lambda    

VoidFunction c2 = System.out::println;        // instance method reference    

PrintStream sysout = System.out; // PrintStream doesn't implement Serializable    

VoidFunction c3 = s -> sysout.println(s);     // capturing lambda 

这里我们创建了3个 [org.apache.spark.api.java.function.VoidFunction](https://link.zhihu.com/?target=https%3A//spark.apache.org/docs/2.0.0/api/java/org/apache/spark/api/java/function/VoidFunction.html) 实例，分别用Java 8的lambda与method reference来创建。

这个 VoidFunction<T> 接口继承了 java.io.Serializable 接口，所以它的实现类都会是可序列化的。
Java 8的可序列化lambda / method reference有些大坑，实现得颇不完美。下面就讲讲这些坑中题主遇到的具体问题。Java 8的lambda与method reference的创建位置是通过invokedynamic指令，提供若干静态参数给对应的bootstrap method来实现的。 <- 这些术语看不懂的话没关系。可以另外找资料来学习一下 invokedynamic 与 bootstrap method。

在Oracle JDK 8 / OpenJDK 8的实现中，javac在编译Java源码的时候会看看一个lambda表达式或method reference的目标SAM（Single Abstract Method）类型是否是Serializable的，并为这个invokedynamic指令选择相应的bootstrap method。
对普通的不可序列化SAM类型：选择 [java.lang.invoke.LambdaMetafactory.metafactory()](https://link.zhihu.com/?target=http%3A//hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/8282bb42fc96/src/share/classes/java/lang/invoke/LambdaMetafactory.java%23l291) 作为bootstrap method；对可序列化的SAM类型：选择 [java.lang.invoke.LambdaMetafactory.altMetafactory()](https://link.zhihu.com/?target=http%3A//hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/8282bb42fc96/src/share/classes/java/lang/invoke/LambdaMetafactory.java%23l426) 作为bootstrap method。所以这里我们要关注的是后者，LambdaMetafactory.altMetafactory()。可以看到其实两个版本背后的实现都是 [InnerClassLambdaMetafactory](https://link.zhihu.com/?target=http%3A//hg.openjdk.java.net/jdk8u/jdk8u/jdk/file/8282bb42fc96/src/share/classes/java/lang/invoke/InnerClassLambdaMetafactory.java)。就目前的JDK8实现而言，这个 InnerClassLambdaMetafactory 会在运行时生成出跟Java的内部类相似的类去实现SAM类型接口，然后我们在运行时得到的lambda或method reference的实例其实就是这些类的实例。

=========================================

第一种情况，s -> System.out.println(s) 是一个没有任何“自由变量”（free variable）的lambda表达式，不需要从外围环境中捕获任何变量。这种lambda表达式也叫做non-capturing lambda。它对应的由altMetafactory在运行时生成的类是这样的：
`import java.lang.invoke.LambdaForm; import java.lang.invoke.SerializedLambda; import org.apache.spark.api.java.function.VoidFunction; final class Demo$$Lambda$1 implements VoidFunction {  private Demo$$Lambda$1() {    // empty private constructor  }   @LambdaForm.Hidden  public void call(Object arg) {    Demo.lambda$main$28d50090$1((String) arg);  }   private final Object writeReplace() {    return new SerializedLambda(             Demo.class,             "org/apache/spark/api/java/function/VoidFunction",             "call",             "(Ljava/lang/Object;)V",             6,             "Demo",             "lambda$main$28d50090$1",             "(Ljava/lang/String;)V",             "(Ljava/lang/String;)V",             new Object[0]);  } }`

writeReplace()是序列化用的，我们先不管它。
但是这个 Demo.lambda$main$28d50090$1() 方法是个啥？
这就是 javac 为这个lambda表达式里的代码逻辑找的安放位置。它长这样：
`  private static void lambda$main$28d50090$1(String s) throws Exception {    return System.out.println(s);  }`可以看到它就是这个lambda表达式的方法体没错。留意到：这个类没有任何字段，不包含任何可变状态。JDK给它实现了一套序列化机制，只写出它的一些符号信息，用SerializedLambda对象来包装起这些符号信息。

=========================================

第二种情况，System.out::println ，这是一个实例方法的method reference，不但会指定要调用的方法是哪个（java.io.PrintStream.println()），还会**捕获**这个被调用的实例（由System.out静态变量所引用的实例）。那么来看看它对应的运行时生成的类是什么样子的：
`import java.lang.invoke.LambdaForm; import java.lang.invoke.SerializedLambda; import java.io.PrintStream; import org.apache.spark.api.java.function.VoidFunction; final class Demo$$Lambda$2 implements VoidFunction {  private final PrintStream arg$1;  private Demo$$Lambda$2(PrintStream arg) {    this.arg$1 = arg;  }   private static VoidFunction get$Lambda(PrintStream arg) {    return new Demo$$Lambda$2(arg);  }   @LambdaForm.Hidden  public void call(Object arg) {    this.arg$1.println((String) arg);  }   private final Object writeReplace() {    return new SerializedLambda(             Demo.class,             "org/apache/spark/api/java/function/VoidFunction",             "call",             "(Ljava/lang/Object;)V",             5,             "java/io/PrintStream",             "println",             "(Ljava/lang/String;)V",             "(Ljava/lang/String;)V",             new Object[] { this.arg$1 });  } } `

可以看到，Java 8里lambda与method reference对变量的“捕获”的实质是capture-by-value，把被捕获的变量拷贝一份存在闭包里。这个类里有一个实例变量 arg$1 就是用来保存被捕获的变量的值用的。
然后我们看到它的 call() 方法就是直接对被捕获的PrintStream引用调用其 println() 方法。

这method reference的实体就在此。

接下来就到题主遇到的问题的根源了：在用于序列化的 writeReplace() 方法中，SerializedLambda 对象不但包含了符号信息，还把这个method reference所捕获的引用也写进去了。可是 PrintStream 类并没有实现 Serializable 接口，于是Java序列化机制在看到它的时候就会报出 NotSerializableException 异常。悲催！这里举的例子是个实例方法的method reference。那么如果是静态方法的method reference呢？那它就不需要捕获被调用对象的引用，于是对应的运行时生成的类就会跟前面的non-capturing lambda类似，序列化就不会遇到问题。=========================================第三种情况，s -> sysout.println(s) 。这是一个带有一个自由变量“sysout”的lambda表达式。它需要从环境中捕获sysout局部变量的值才可以正确运行。所以这种lambda也叫做capturing lambda。想必有了上面两种情况的讲解，大家也可以猜到它对应的运行时生成的类是什么样子的了。具体分析就留作课后作业吧 ^_^