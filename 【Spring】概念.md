# 【Spring】笔记



[toc]



相关视频：
[黑马程序员2022新版SSM框架教程_Spring+SpringMVC+Maven高级+SpringBoot+MyBatisPlus企业实用开发技术](https://www.bilibili.com/video/BV1Fi4y1S7ix/)

相关网页：
[Spring](https://spring.io/)



简化开发：
	IoC
	AOP
	→ 事务处理

框架整合：
	MyBatis
	MyBatis-Plus
	Struts
	Struts2
	Hibernate
	······

Spring全家桶：
	Spring Framwork
	Spring Boot
	Spring Cloud
	······

2004年：Spring 1.0
2006年：Spring 2.0
2009年：Spring 3.0
2013年：Spring 4.0
2017年：Spring 5.0



## Spring Framework

Spring Framework系统架构：
	Core Container：核心容器
	AOP：面向切面编程
	Aspects：AOP思想实现
	Instrumentation
	Messaging
	Data Access/Integration：数据访问、数据集成
		JDBC
		ORM
		JMS
		Transaction
	Web：Web开发
		WebSocket
		Servlet
		Web
		Portlet
	Test：单元测试、集成测试



## IoC

IoC（Inversion of Control）：控制反转
	对象的创建控制权 → 外部 → 解耦

IoC容器 / Spring容器：外部
	创建、初始化、管理对象（Bean）
	依赖注入（Dependency Injection，DI）：对有依赖关系的Bean进行关系绑定



## 入门案例

将被管理的对象告知IoC容器：配置
获取IoC容器：接口
获取Bean：接口方法
导入坐标：pom.xml



### 项目文件

```
project
│  pom.xml
│
├─.idea
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─test
│  │  │          │  Demo.java
│  │  │          │
│  │  │          ├─dao
│  │  │          │  │  TestDao.java
│  │  │          │  │
│  │  │          │  └─impl
│  │  │          │          TestDaoImpl.java
│  │  │          │
│  │  │          └─service
│  │  │              │  TestService.java
│  │  │              │
│  │  │              └─impl
│  │  │                      TestServiceImpl.java
│  │  │
│  │  └─resources
│  │          applicationContext.xml
│  │
│  └─test
└─target
```



### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.test</groupId>
    <artifactId>project</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```



### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl"></bean>
    <bean id="testService" class="com.test.service.impl.TestServiceImpl">
        <property name="testDao" ref="testDao"></property>
    </bean>
</beans>
```



### TestDao.java

```java
package com.test.dao;

public interface TestDao {
    void print();
}
```



### TestDaoImpl.java

```java
package com.test.dao.impl;

import com.test.dao.TestDao;

public class TestDaoImpl implements TestDao {
    public void print() {
        System.out.println("TestDaoImpl: print");
    }
}
```



### TestService.java

```java
package com.test.service;

public interface TestService {
    void print();
}
```



### TestServiceImpl.java

```java
package com.test.service.impl;

import com.test.dao.TestDao;
import com.test.service.TestService;

public class TestServiceImpl implements TestService {
    private TestDao testDao;

    public void print() {
        testDao.print();
        System.out.println("TestServiceImpl: print");
    }

    public void setTestDao(TestDao testDao) {
        this.testDao = testDao;
    }
}
```



### Demo.java

```java
package com.test;

import com.test.dao.TestDao;
import com.test.service.TestService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Demo {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestDao testDao = (TestDao) context.getBean("testDao");
        TestService testService = (TestService) context.getBean("testService");
        testDao.print();
        testService.print();
    }
}
```



### 运行结果

```
TestDaoImpl: print
TestDaoImpl: print
TestServiceImpl: print
```



## bean标签

属性：
	id
	name：定义bean的别名（可用逗号、分号、空格分隔）
	class
	scope：
		singleton（单例，默认）：表现层对象、业务层对象、数据层对象、工具对象（适合交给容器管理）
		prototype（非单例）：封装实体的域对象（不适合交给容器管理）
	factory-method：静态工厂方法、实例工厂方法
	factory-bean：实例工厂
	init-method：初始化方法
	destory-method：销毁方法

bean的实例化：
	构造方法（无参）
	静态工厂
	实例工厂
	→ FactoryBean

依赖注入：
	setter注入
	构造器注入

自动装配：
	引用数据类型：√
	基本数据类型：×

集合注入：数组、List、Set、Map、Properties



### 示例：静态工厂

#### 项目文件

```
project
│  pom.xml
│
├─.idea
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─test
│  │  │          │  Demo.java
│  │  │          │
│  │  │          ├─dao
│  │  │          │  │  TestDao.java
│  │  │          │  │
│  │  │          │  └─impl
│  │  │          │          TestDaoImpl.java
│  │  │          │
│  │  │          └─factory
│  │  │                  TestDaoFactory.java
│  │  │
│  │  └─resources
│  │          applicationContext.xml
│  │
│  └─test
└─target
```



#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.factory.TestDaoFactory" factory-method="getTestDao"></bean>
</beans>
```



#### TestDaoFactory.java

```java
package com.test.factory;

import com.test.dao.TestDao;
import com.test.dao.impl.TestDaoImpl;

public class TestDaoFactory {
    public static TestDao getTestDao() {
        return new TestDaoImpl();
    }
}
```



#### Demo.java

```java
package com.test;

import com.test.dao.TestDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Demo {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestDao testDao = (TestDao) context.getBean("testDao");
        testDao.print();
    }
}
```



#### 运行结果

```
TestDaoImpl: print
```



### 示例：实例工厂

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDaoFactory" class="com.test.factory.TestDaoFactory" ></bean>
    <bean id="testDao" factory-method="getTestDao" factory-bean="testDaoFactory"></bean>
</beans>
```



#### TestDaoFactory.java

```java
package com.test.factory;

import com.test.dao.TestDao;
import com.test.dao.impl.TestDaoImpl;

public class TestDaoFactory {
    public TestDao getTestDao() {
        return new TestDaoImpl();
    }
}
```



#### 运行结果

```
TestDaoImpl: print
```



### 示例：FactoryBean

#### 项目文件

```
project
│  pom.xml
│
├─.idea
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─test
│  │  │          │  Demo.java
│  │  │          │
│  │  │          ├─dao
│  │  │          │  │  TestDao.java
│  │  │          │  │
│  │  │          │  └─impl
│  │  │          │          TestDaoImpl.java
│  │  │          │
│  │  │          └─factory
│  │  │                  TestDaoFactoryBean.java
│  │  │
│  │  └─resources
│  │          applicationContext.xml
│  │
│  └─test
└─target
```



#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.factory.TestDaoFactoryBean"></bean>
</beans>
```



#### TestDaoFactoryBean.java

```java
package com.test.factory;

import com.test.dao.TestDao;
import com.test.dao.impl.TestDaoImpl;
import org.springframework.beans.factory.FactoryBean;

public class TestDaoFactoryBean implements FactoryBean<TestDao> {
    public TestDao getObject() throws Exception {
        return new TestDaoImpl();
    }

    public Class<?> getObjectType() {
        return TestDao.class;
    }

    public boolean isSingleton() {
        return false;
    }
}
```



#### Demo.java

```java
package com.test;

import com.test.dao.TestDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Demo {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestDao testDao1 = (TestDao) context.getBean("testDao");
        TestDao testDao2 = (TestDao) context.getBean("testDao");
        testDao1.print();
        testDao2.print();
        System.out.println(testDao1);
        System.out.println(testDao2);
    }
}
```



#### 运行结果

```
TestDaoImpl: print
TestDaoImpl: print
com.test.dao.impl.TestDaoImpl@31ef45e3
com.test.dao.impl.TestDaoImpl@598067a5
```



### 示例：setter注入

#### 项目文件

```
project
│  pom.xml
│
├─.idea
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─test
│  │  │          │  Demo.java
│  │  │          │
│  │  │          ├─dao
│  │  │          │  │  TestDao.java
│  │  │          │  │
│  │  │          │  └─impl
│  │  │          │          TestDaoImpl.java
│  │  │          │
│  │  │          └─service
│  │  │              │  TestService.java
│  │  │              │
│  │  │              └─impl
│  │  │                      TestServiceImpl.java
│  │  │
│  │  └─resources
│  │          applicationContext.xml
│  │
│  └─test
└─target
```



#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl"></bean>
    <bean id="testService" class="com.test.service.impl.TestServiceImpl">
        <property name="i" value="1"></property>
        <property name="testDao" ref="testDao"></property>
    </bean>
</beans>
```



#### TestService.java

```java
package com.test.service;

public interface TestService {
    void print();
}
```



#### TestServiceImpl.java

```java
package com.test.service.impl;

import com.test.dao.TestDao;
import com.test.service.TestService;

public class TestServiceImpl implements TestService {
    private int i;
    private TestDao testDao;

    public void print() {
        testDao.print();
        System.out.println("TestServiceImpl: print");
        System.out.println(i);
    }

    public void setI(int i) {
        this.i = i;
    }

    public void setTestDao(TestDao testDao) {
        this.testDao = testDao;
    }
}
```



#### Demo.java

```java
package com.test;

import com.test.dao.TestDao;
import com.test.service.TestService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Demo {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestDao testDao = (TestDao) context.getBean("testDao");
        TestService testService = (TestService) context.getBean("testService");
        testDao.print();
        testService.print();
    }
}
```



#### 运行结果

```
TestDaoImpl: print
TestDaoImpl: print
TestServiceImpl: print
1
```



### 示例：构造器注入

#### applicationContext.xml

写法一：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl"></bean>
    <bean id="testService" class="com.test.service.impl.TestServiceImpl">
        <constructor-arg name="i" value="1"></constructor-arg>
        <constructor-arg name="testDao" ref="testDao"></constructor-arg>
    </bean>
</beans>
```



写法二：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl"></bean>
    <bean id="testService" class="com.test.service.impl.TestServiceImpl">
        <constructor-arg index="0" value="1"></constructor-arg>
        <constructor-arg index="1" ref="testDao"></constructor-arg>
    </bean>
</beans>
```



写法三：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl"></bean>
    <bean id="testService" class="com.test.service.impl.TestServiceImpl">
        <constructor-arg type="int" value="1"></constructor-arg>
        <constructor-arg type="com.test.dao.TestDao" ref="testDao"></constructor-arg>
    </bean>
</beans>
```



#### TestServiceImpl.java

```java
package com.test.service.impl;

import com.test.dao.TestDao;
import com.test.dao.impl.TestDaoImpl;
import com.test.service.TestService;

public class TestServiceImpl implements TestService {
    private int i;
    private TestDao testDao;

    public void print() {
        testDao.print();
        System.out.println("TestServiceImpl: print");
        System.out.println(i);
    }

    public TestServiceImpl(int i, TestDao testDao) {
        this.i = i;
        this.testDao = testDao;
    }
}
```



#### 运行结果

```
TestDaoImpl: print
TestDaoImpl: print
TestServiceImpl: print
1
```



### 示例：自动装配

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl"></bean>
    <bean id="testService" class="com.test.service.impl.TestServiceImpl" autowire="byType"></bean>
</beans>
```



#### TestServiceImpl.java

```java
package com.test.service.impl;

import com.test.dao.TestDao;
import com.test.service.TestService;

public class TestServiceImpl implements TestService {
    private int i;
    private TestDao testDao;

    public void print() {
        testDao.print();
        System.out.println("TestServiceImpl: print");
        System.out.println(i);
    }

    public void setI(int i) {
        this.i = i;
    }

    public void setTestDao(TestDao testDao) {
        this.testDao = testDao;
    }
}
```



#### 运行结果

```
TestDaoImpl: print
TestDaoImpl: print
TestServiceImpl: print
0
```



### 示例：集合注入

#### 项目文件

```
project
│  pom.xml
│
├─.idea
├─src
│  ├─main
│  │  ├─java
│  │  │  └─com
│  │  │      └─test
│  │  │          │  Demo.java
│  │  │          │
│  │  │          └─dao
│  │  │              │  TestDao.java
│  │  │              │
│  │  │              └─impl
│  │  │                      TestDaoImpl.java
│  │  │
│  │  └─resources
│  │          applicationContext.xml
│  │
│  └─test
└─target
```



#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="testDao" class="com.test.dao.impl.TestDaoImpl">
        <property name="array">
            <array>
                <value>abc</value>
                <value>def</value>
                <value>ghi</value>
            </array>
        </property>
        <property name="list">
            <list>
                <value>abc</value>
                <value>def</value>
                <value>ghi</value>
            </list>
        </property>
        <property name="set">
            <set>
                <value>abc</value>
                <value>def</value>
                <value>ghi</value>
            </set>
        </property>
        <property name="map">
            <map>
                <entry key="abc" value="1"></entry>
                <entry key="def" value="2"></entry>
                <entry key="ghi" value="3"></entry>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="abc">1</prop>
                <prop key="def">2</prop>
                <prop key="ghi">3</prop>
            </props>
        </property>
    </bean>
</beans>
```



#### TestDaoImpl.java

```java
package com.test.dao.impl;

import com.test.dao.TestDao;

import java.util.*;

public class TestDaoImpl implements TestDao {
    private String[] array;
    private List<String> list;
    private Set<String> set;
    private Map<String, Integer> map;
    private Properties properties;

    public void setArray(String[] array) {
        this.array = array;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    public void setSet(Set<String> set) {
        this.set = set;
    }

    public void setMap(Map<String, Integer> map) {
        this.map = map;
    }

    public void setProperties(Properties properties) {
        this.properties = properties;
    }

    public void print() {
        System.out.println("TestDaoImpl: print");
        System.out.println("Array: " + Arrays.toString(array));
        System.out.println("List: " + list);
        System.out.println("Set: " + set);
        System.out.println("Map: " + map);
        System.out.println("Properties: " + properties);
    }
}
```



#### Demo.java

```java
package com.test;

import com.test.dao.TestDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Demo {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestDao testDao = (TestDao) context.getBean("testDao");
        testDao.print();
    }
}
```



#### 运行结果

```
TestDaoImpl: print
Array: [abc, def, ghi]
List: [abc, def, ghi]
Set: [abc, def, ghi]
Map: {abc=1, def=2, ghi=3}
Properties: {abc=1, def=2, ghi=3}
```

