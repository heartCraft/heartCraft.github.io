# Spring

## 依赖注入（dependency injection,DI）,又名控制反转（Inversion of Control,IOC） 
要解决的问题：**解耦**

核心：Spring容器
* 如何发现待注入类，如何将容器中的类注入
* bean的声明周期

##DI的装配方式：
* 自动化装配
  * 组件扫描:
    * `@Component`：value属性可指定beanID，默认为类名首字母小写
    * `@ComponentScan`：默认只扫描此注解同package下的类，value属性可指定扫描package，另有basePackages（可配置多个扫描package）和basePackageClasses（可配置多个类，这些类所在的package下所有类均会被扫描）
  * 自动装配:`@Autowired`

[a](StarBlogs.md)
