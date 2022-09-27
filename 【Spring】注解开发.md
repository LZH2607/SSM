# 【Spring】注解开发



[toc]



## 纯注解开发

### 配置类的注解

定义配置类：@Configuration
扫描组件：@ComponentScan({"com.test.dao", "com.test.service"})
加载配置文件：@PropertySource("application.properties")



### 类的注解

定义bean（组件）：@Component
	定义表现层bean：@Controller
	定义业务层bean：@Service
	定义数据层bean：@Repository

单例：@Scope("singleton")
非单例：@Scope("prototype")



### 方法的注解

在构造方法后执行：@PostConstruct
在销毁方法前执行：@PreDestroy



### 依赖注入

引用类型注入：@Autowired
指定名称注入：@Qualifier（配合@Autowired使用）
简单类型注入：@Value



### 第三方bean的注解

配置第三方bean：@Bean



### 示例

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
│  │  │          ├─config
│  │  │          │      Config.java
│  │  │          │      JdbcConfig.java
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
│  │          application.properties
│  │
│  └─test
└─target
```



#### pom.xml

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
        <!-- druid -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.12</version>
        </dependency>
    </dependencies>
</project>
```



#### application.properties

```properties
s=abc

jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/db
jdbc.username=root
jdbc.password=1234
```



#### Config.java

```java
package com.test.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

@Configuration
@ComponentScan({"com.test.dao", "com.test.service", "com.test.config"})
@PropertySource({"application.properties"})
@Import({JdbcConfig.class})
public class Config {
}
```



#### JdbcConfig.java

```java
package com.test.config;

import com.alibaba.druid.pool.DruidDataSource;
import com.test.dao.TestDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;

import javax.sql.DataSource;

public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean("dataSource")
    public DataSource dataSource(TestDao testDao) {
        System.out.println(driver);
        System.out.println(url);
        System.out.println(username);
        System.out.println(password);
        testDao.print();
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driver);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }
}
```



#### TestDao.java

```java
package com.test.dao;

public interface TestDao {
    void print();
}
```



#### TestDaoImpl.java

```java
package com.test.dao.impl;

import com.test.dao.TestDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;

@Repository("testDao")
public class TestDaoImpl implements TestDao {
    @Value("${s}")
    private String s;

    public void print() {
        System.out.println("TestDaoImpl: print");
        System.out.println("s: " + s);
    }
}
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
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service("testService")
public class TestServiceImpl implements TestService {
    @Autowired
    private TestDao testDao;

    public void print() {
        testDao.print();
        System.out.println("TestServiceImpl: print");
    }
}
```



#### Demo.java

```java
package com.test;

import com.test.config.Config;
import com.test.dao.TestDao;
import com.test.service.TestService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import javax.sql.DataSource;

public class Demo {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
        TestDao testDao = (TestDao) context.getBean("testDao");
        TestService testService = (TestService) context.getBean("testService");
        DataSource dataSource = (DataSource) context.getBean("dataSource");
        testDao.print();
        testService.print();
        System.out.println(dataSource);
    }
}
```



#### 运行结果

```
com.mysql.jdbc.Driver
jdbc:mysql://127.0.0.1:3306/db
root
1234
TestDaoImpl: print
s: abc
TestDaoImpl: print
s: abc
TestDaoImpl: print
s: abc
TestServiceImpl: print
{
	CreateTime:"2022-09-27 23:37:03",
	ActiveCount:0,
	PoolingCount:0,
	CreateCount:0,
	DestroyCount:0,
	CloseCount:0,
	ConnectCount:0,
	Connections:[
	]
}
```

