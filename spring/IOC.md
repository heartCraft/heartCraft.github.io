# 控制反转（Inversion of Control,IOC） 

### 分为依赖注入（dependency injection,DI），依赖查找（depandency lookup,DL）两部分
* DI：被动接受其依赖的其他组件被IOC容器注入
* DL：主动在IOC容器中查找其需要的组件

### IOC容器完成两部分功能：收集与注册，分析与组装
* 收集与注册（bean发现）：将bean收集到IOC容器
* 分析与组装：分析bean的依赖关系，依次组装

### 实现方式
* JavaConfig
* XML
* 基于JavaConfig或XML的自动化扫描与装配

## DI的装配方式：
* 自动化装配
  * 组件扫描:
    * `@Component`：value属性可指定beanID，默认为类名首字母小写
    * `@ComponentScan`：默认只扫描此注解同package下的类，value属性可指定扫描package，另有basePackages（可配置多个扫描package）和basePackageClasses（可配置多个类，这些类所在的package下所有类均会被扫描）（配置类需指定@Configuration）
  * 自动装配:
    * `@Autowired`：required属性；自动装配的歧义性
* java代码显式装配
  * `@Configuration`：声明配置类
  * `@Bean`：声明bean对象，name属性指定beanId，默认方法名
    
   
核心：Spring容器
* 如何发现待注入类，如何将容器中的类注入
* bean的声明周期