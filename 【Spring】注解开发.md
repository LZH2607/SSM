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



### 示例

#### 项目文件

```
```

