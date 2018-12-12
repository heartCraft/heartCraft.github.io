# Java类加载机制

### class声明周期

分为七个阶段：**加载、验证、准备、解析、初始化、使用、卸载**

​	加载：把字节码转换为二进制字节流，加载到内存中；

​	验证：大致可以分为：JVM规范校验，代码逻辑校验...

 	**准备**：JVM为类变量分配内存并初始化，这里的初始化是指该类型的默认值，而非代码中的初始化值。

​		特例：final修饰的类变量赋值为字面量时，会在准备阶段就。

​	解析：将其常量池中的符号引用替换为内存中的直接饮用

​	**初始化**：类初始化（类加载时只执行一次）。

​		类初始化顺序：先父类初始化，再按代码中顺序，类变量赋值>静态代码块

​	使用：初始化完成后，该类可以使用。当创建新对象时再进行对象初始化。

​		对象初始化：先父类对象初始化，再按代码中顺序，类成员变量>普通代码块>构造函数

​	卸载：JVM销毁创建的Class对象

### ClassLoader（将字节码转换为内存中的Class对象）

每个class对象的内部都有一个classLoader字段，标识自己是由哪个classLoader加载的。



**JVM 中内置了三个重要的 ClassLoader**：

* BootstrapClassLoader 负责加载 JVM 运行时核心类，这些类位于 JAVA_HOME/lib/rt.jar 文件中，我们常用内置库 java.xxx.* 都在里面，比如 java.util.*、java.io.*、java.nio.*、java.lang.* 等等。这个 ClassLoader 比较特殊，它是由 C 代码实现的，我们将它称之为「根加载器」。

* ExtensionClassLoader 负责加载 JVM 扩展类，比如 swing 系列、内置的 js 引擎、xml 解析器 等等，这些库名通常以 javax 开头，它们的 jar 包位于 JAVA_HOME/lib/ext/*.jar 中，有很多 jar 包。

* AppClassLoader 才是直接面向我们用户的加载器，它会加载 Classpath 环境变量里定义的路径中的 jar 包和目录。我们自己编写的代码以及使用的第三方 jar 包通常都是由它来加载的。

可以用URLClassLoader实现自己的类加载器，ExtensionClassLoader 和 AppClassLoader都是URLClassLoader的子类。

ClassLoader.getSystemClassLoader()可以获取AppClassLoader。



**ClassLoader传递性**：当需要加载一个类时，JVM默认会使用（当前需要使用此类的）调用者Class对象的ClassLoader加载此类。



**双亲委派**：AppClassLoader 只负责加载Classpath下的类库，遇到未加载的系统类库时，加载工作会交给BootstrapClassLoader 和 ExtensionClassLoader 。

* AppClassLoader 在加载一个未知的类名时，它并不是立即去搜寻 Classpath，它会首先将这个类名称交给 ExtensionClassLoader 来加载，如果 ExtensionClassLoader 可以加载，那么 AppClassLoader 就不用麻烦了。否则它就会搜索 Classpath 。

* 而 ExtensionClassLoader 在加载一个未知的类名时，它也并不是立即搜寻 ext 路径，它会首先将类名称交给 BootstrapClassLoader 来加载，如果 BootstrapClassLoader 可以加载，那么 ExtensionClassLoader 也就不用麻烦了。否则它就会搜索 ext 路径下的 jar 包。

* 这三个 ClassLoader 之间形成了级联的父子关系，每个 ClassLoader 都很懒，尽量把工作交给父亲做，父亲干不了了自己才会干。每个 ClassLoader 对象内部都会有一个 parent 属性指向它的父加载器。值得注意的是图中的 ExtensionClassLoader 的 parent 的值是 null，当 parent 字段是 null 时就表示它的父加载器是「根加载器」。如果某个 Class 对象的 classLoader 属性值是 null，那么就表示这个类也是「根加载器」加载的。



**Class.forName**：此方法多参数版本支持指定ClassLoader。此方法可以传入自定义的类加载器。



**自定义类加载器**：三个重要方法，loadClass()、findClass()、defineClass()。使用自定义类加载器会产生钻石依赖问题。



**ClassLoader的意义**：不同classLoader加载的同一字节码也是不同的类，所以能起到类隔离的作用。而parent属性和双亲委派的机制，又实现了共享的可能。

​	e.g. Thread类的contextClassLoader属性，通过设置该属性，可以隔离不同线程中使用的类。









