## 1. Spring Ioc的基本概念

IOC:  控制反转  Spring框架技术的核心  解耦

DI： 依赖注入 IoC的另外一种说法 从不同的角度描述相同的概念



具体情景：

一个Java对象A需要调用另外一个Java对象B，传统的编程方式会采用new的方式，这种方式会增加调用者和被调用者之间的耦合性，不利于后期代码的升级与维护

Spring中，对象的实例不再由调用者来创建，而是由Spring容器来创建 Spring容器会控制程序之间的关系，而不是由调用者控制，这样控制权由调用者转移到了Spring容器，控制器发生了反转，即控制反转

从Spring容器的角度出发，Spring容器负责将依赖对象赋值给调用者的成员变量，相当于为调用者注入它所依赖的实例，这就是依赖注入

Spring中实现控制反转的是IoC容器，具体实现方法是依赖注入



## 2. Spring的常用注解

1. 声明bean的注解

   - @Component

     该注解是一个泛化的概念，仅仅表示一个组件对象，可以作用在任何层次上，没有明确的角色

   - @Repository

     用于将数据访问层（Dao）的类标示为bean

   - @Service

     用于将一个业务逻辑组件类(Service)标示为bean

   - @Component

     用于将一个控制器组件类(Controller)标示为bean

2. 注入bean的注解

   - @Autowired

     默认按Bean的类型进行装配

   - @Resources

     默认按Bean的名称进行装配，找不到与名称匹配的bean然后再按类型来装配注入(不加name也不加type)

     @Resources有两个属性name(按名称匹配) type(按类型匹配)

   - @Qualifier

     和@Autowired一起使用 指定名称（因为同一类型的bean可能有多个）

3. 基于注解的依赖注入



##  3. 基于注解的依赖注入

1. 导入相应的包：

   <img src="../../../MyNotesImgs/Spring Ioc.assets/image-20211011201433788.png" alt="image-20211011201433788" style="zoom:50%;" />

2. 创建cn.edu.xd.dao包,建立如下文件

   ```java
   package cn.edu.xd.dao;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:51
    */
   public interface TestDao {
       public void save();
   }
   
   ```

   ```java
   package cn.edu.xd.dao;
   
   import org.springframework.stereotype.Repository;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:52
    */
   @Repository //这里不加("XXX") 默认bean名称是testDaoImpl
   public class TestDaoImpl implements TestDao {
       @Override
       public void save() {
           System.out.println("testDao save");
       }
   }
   
   ```

3. 创建cn.edu.xd.service包,建立如下文件

   ```java
   package cn.edu.xd.service;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:53
    */
   public interface TestService {
       public void save();
   }
   
   ```

   ```java
   package cn.edu.xd.service;
   
   import cn.edu.xd.dao.TestDao;
   import org.springframework.stereotype.Service;
   
   import javax.annotation.Resource;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:53
    */
   @Service// //这里不加("XXX") 默认bean名称是testServiceImpl
   public class TestServiceImpl implements TestService {
       @Resource(name = "testDaoImpl")
       private TestDao testDao;
       @Override
       public void save() {
           testDao.save();
       }
   }
   
   ```

4. 创建cn.edu.xd.controller包,建立如下文件

   ```java
   package cn.edu.xd.controller;
   
   import cn.edu.xd.service.TestService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:54
    */
   @Controller
   public class TestController {
       @Autowired
       private TestService testService;
       public void save()
       {
           testService.save();
       }
   }
   
   ```

5. 创建cn.edu.xd.config包,建立如下文件

   ```java
   package cn.edu.xd.config;
   
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:56
    */
   @Configuration//声明当前类是一个配置类，相当于一个Spring的XML配置文件。
   //自动扫描cn.edu.xd包下使用的注解，并注册为Bean。
   //相当于在Spring的XML配置文件使用<context:component-scan base-package="Bean所在的包路径"/>语句功能一样。
   @ComponentScan("cn.edu.xd")
   public class ConfigAnnotation {
   }
   
   ```

6. 测试及结果

   ```java
   import cn.edu.xd.config.ConfigAnnotation;
   import cn.edu.xd.controller.TestController;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.annotation.AnnotationConfigApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:57
    */
   public class TestAnnotation {
       public static void main(String[] args) {
           AnnotationConfigApplicationContext appCon=new AnnotationConfigApplicationContext(ConfigAnnotation.class);
           TestController tc=appCon.getBean(TestController.class);
           tc.save();
           appCon.close();
   
       }
   }
   
   ```

   <img src="../../../../MyNotesImgs/Spring Ioc.assets/image-20211011201804614.png" alt="image-20211011201804614" style="zoom:50%;" />

## 4.Java配置

Java配置是Spring4.x推荐的配置方式，通过使用@Configuration和@Bean注解来实现

@Configuration表明当前类是一个配置类，相当于一个Spring配置的xml文件

@Bean注解在方法上，声明当前方法的返回值是一个Bean

基于xml的依赖注入需要set方法  基于Java配置的依赖注入也需要set方法

1. 导入包：

   <img src="../../../MyNotesImgs/Spring Ioc.assets/image-20211011204152283.png" alt="image-20211011204152283" style="zoom:50%;" />

2. 创建cn.edu.xd.dao包，建立以下文件：

   ```java
   package cn.edu.xd.dao;
   
   import org.springframework.stereotype.Repository;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:52
    */
   //这里没有使用@Repository声明Bean
   public class TestDao{
       public void save() {
           System.out.println("testDao save");
       }
   }
   
   ```

3. 创建cn.edu.xd.service包，建立以下文件：

   ```java
   package cn.edu.xd.service;
   
   import cn.edu.xd.dao.TestDao;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:53
    */
   //这里没有使用@Repository声明Bean
   public class TestService {
      //这里没有注入testDao
       private TestDao testDao;
       public void setTestDao(TestDao testDao) {
           this.testDao = testDao;
       }
       public void save() {
           testDao.save();
       }
   }
   
   ```

4. 创建cn.edu.xd.controller包，建立以下文件：

   ```java
   package cn.edu.xd.controller;
   
   import cn.edu.xd.service.TestService;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:54
    */
   //这里没有使用@Controller声明Bean
   public class TestController {
       //这里没有使用@Autowired注入testService
       private TestService testService;
   
       public void setTestService(TestService testService) {
           this.testService = testService;
       }
   
       public void save()
       {
           testService.save();
       }
   }
   
   ```

5. 创建cn.edu.xd.config包，建立以下文件：

   ```java
   package cn.edu.xd.config;
   
   import cn.edu.xd.controller.TestController;
   import cn.edu.xd.dao.TestDao;
   import cn.edu.xd.service.TestService;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.ComponentScan;
   import org.springframework.context.annotation.Configuration;
   
   /**
    * @author guipengfei
    * @date 2021/10/11 19:56
    */
   @Configuration
   //不需要使用包扫描  因为所有的Bean都在这个类中定义了
   public class ConfigAnnotation {
       @Bean
       public TestDao getTestDao() {
           return new TestDao();
       }
       @Bean
       public TestService getTestService() {
           TestService ts = new TestService();
           //使用set方法注入testDao
           ts.setTestDao(getTestDao());
           return ts;
       }
       @Bean
       public TestController getTestController() {
           TestController tc = new TestController();
           //使用set方法注入testService
           tc.setTestService(getTestService());
           return tc;
       }
   }
   
   ```

6. 测试运行

   ```java
   import cn.edu.xd.config.ConfigAnnotation;
   import cn.edu.xd.controller.TestController;
   import org.springframework.context.annotation.AnnotationConfigApplicationContext;
   /**
    * @author guipengfei
    * @date 2021/10/11 19:57
    */
   public class TestAnnotation {
       public static void main(String[] args) {
           AnnotationConfigApplicationContext appCon=new AnnotationConfigApplicationContext(ConfigAnnotation.class);
           TestController tc=appCon.getBean(TestController.class);
           tc.save();
           appCon.close();
   
       }
   }
   
   ```

   