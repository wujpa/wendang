##spring配置文件


    <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    </beans>

-常用依赖
-
    <dependencies>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.0</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    </dependencies>

注解
-
- 使用注解要加上
  
        xmlns:context="http://www.springframework.org/schema/context"
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd"
-   开启注解
    
        <context:annotation-config/>
  
##@Autowired  
- @Autowired(required=false),对象可以为null；true，对象必须存对象不能为null
- @Autowired根据类型自动装配，加上@Qualifier则可以根据byName的方式自动装配
- @Qualifier不能单独使用
##@Resourse
- @Resource如有指定的name属性，先按该属性进行byName方式查找装配；
- 其次在进行默认的byName方式进行装配；
- 如果以上都不成功，则按byType的方式自动装配
- 都不成功，则报异常

##两者都可以用来装配bean。都可以写在字段上，或卸载setter方法。@Resource先byName.@Autowired先byType

        <!--指定注解扫描包-->
        <context:component-scan base-package="com.wujq" />
##@Component:组件。放在类上，说明这个类被spring管理了，就是bean!
为了更好的分层，spring可以使用其他三个注解，功能一样，目前使用哪一个功能都一样
- @Repository 在dao层使用
- @Controller 在controller层使用
- @Service 在service层使用

#作用域
@scope
- singleton：默认的，Spring会采用单例模式创建这个对象。关闭工厂所有对象都会销毁。
- prototype：多例模式。关闭工厂，所有的对象不会被销毁。内部的垃圾回收机制会回收。

#javaconfig
        */ //这个也会被spring容器托管，注册到容器中，因为他本来就是一个@Component
        @Configuration
        @ComponentScan("com.wujq.pojo")
        @Import(Wuconfig2.class)
        public class WuConfig {
        
            //注册一个bean,就相当于我们之前写的一个bean标签
            //方法的名字，就相当于bean标签中的id值
            //这个方法的返回值，就相当于bean标签中的class属性
        
            @Bean
            public User getUser(){
                //就是返回要注入到bean的对象！
                return new User();
            }
        }
##AOP
我们在不改变原来的代码的情况下，实现了对原有功能的增强，这是AOP中最核心的思想

【重点】使用AOP织入，需要导入一个依赖包

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>

方式一：使用spring的API接口【主要由SpringAPI接口实现】
方式二：自定义来实现AOP【主要是切面定义】
方式三：使用注解增强
            <!--开始注解支持-->
            <aop:aspectj-autoproxy/>