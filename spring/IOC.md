# 控制反转（IOC：Inversion of Control） 

### 控制反转实为依赖注入（DI：dependency injection），依赖查找（DL：depandency lookup）
* DI：被动接受其依赖的其他组件被IOC容器注入
* DL：主动在IOC容器中查找其需要的组件

## 实现方式
* JavaConfig
* XML
* 基于JavaConfig或XML的自动化扫描与装配

### JavaConfig
* `@Configuration`：声明配置类
* `@Bean`：声明bean对象，name属性指定beanId，默认方法名
* `@Import`:引入其他javaConfig文件
* `@ImportResources`:引入其他XML配置的bean
```java
@Configuration
public class Config {
    @Bean
    public Patient patient() {
        return new Patient("stomachache!");
    }

    @Bean
    public Doctor doctor() {
        Doctor docter = new Doctor();
        docter.setPatient(patient());
        return docter;
    }
    // 相同的效果，不同的实现（在获取Docter实体时，会自动装配patient对象到此参数）
    /*@Bean
    public Doctor docter(Patient patient) {
        Doctor docter = new Doctor();
        docter.setPatient(patient);
        return docter;
    }*/
}
```

### XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:Context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="patient" class="com.xml.Patient">
        <!--替代方案：c命名空间-->
        <constructor-arg value="stomachache!"/>
    </bean>

    <bean id="doctor" class="com.xml.Doctor">
        <!--替代方案：p命名空间-->
        <property name="patient" ref="patient"/>
    </bean>

    <!--进阶：如何装配集合，util命名空间-->

    <!--引用javaConfig-->
    <!--<bean class=""/>-->

    <!--引用其他bean xml文件-->
    <!--<import resource=""/>-->
</beans>
```

### 基于JavaConfig或XML的自动化扫描与装配
* `@Component`：value属性可指定beanID，默认为类名首字母小写
* `@ComponentScan`：默认只扫描此注解同package下的类，value属性可指定扫描package，另有basePackages（可配置多个扫描package）和basePackageClasses（可配置多个类，这些类所在的package下所有类均会被扫描）（配置类需指定@Configuration）
* `@Autowired`：required属性；自动装配的歧义性
```java
@Component
public class Doctor {
    private Patient patient;

    public Patient getPatient() {
        return patient;
    }

    @Autowired
    public void setPatient(Patient patient) {
        this.patient = patient;
    }
}
```
```java
@Component
public class Patient {
    // 症状
    private String symptom;

    @Autowired
    public Patient(String symptom) {
        this.symptom = symptom;
    }

    public String getSymptom() {
        return symptom;
    }

    public void setSymptom(String symptom) {
        this.symptom = symptom;
    }
}
```
打开扫描功能方式：
* 在JavaConfig文件上标注@ComponentScan注解
* 在XML文件中使用<context:component-scan base-package="base package name"/>
    
## 核心：Spring容器
* 如何发现待注入类，如何将容器中的类注入
* bean的声明周期