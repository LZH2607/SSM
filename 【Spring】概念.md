# 【Spring】笔记



[toc]



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



## 示例项目

### 
