SpringBoot其设计目的是为了<mark style="background: #BBFABBA6;color:white">简化</mark>Spring应用的<mark style="background: #BBFABBA6;color:white">初始搭建</mark>和<mark style="background: #BBFABBA6;color:white">开发过程</mark>
## 1.基础篇
###### 1.parent：所有是SpringBoot项目需要继承的项目，定义了若干个坐标版本号（依赖管理），以<mark style="background: #BBFABBA6;">减少依赖冲突</mark>
###### 2.starter：SpringBoot中常见的项目名称，定义了当前项目使用的所有依赖坐标，以<mark style="background: #BBFABBA6;">减少依赖配置</mark>
###### 3.引导类
```java
package com.xiongfeng;  
  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
@SpringBootApplication  
public class Springboot0101QuickstartApplication {  
  
    public static void main(String[] args) {  
	    //返回Spring容器
        SpringApplication.run(Springboot0101QuickstartApplication.class, args);  
    }  
  
}
```
###### 4.REST：表现形式状态转换，按照访问资源时使用的<mark style="background: #BBFABBA6;">行为动作</mark>区分进行何种操作
 优点：书写简便，无法通过地址得知行为
###### 5.配置文件
1.properties
<mark style="background: #BBFABBA6;">2.yml(主流)</mark>
3.yaml
yaml语法规则
![[yaml语法规则.png]]
4.优先级：properties > yml > yaml
## 2.开发实用篇 
#### 1.热部署
<mark style="background: #BBFABBA6;">重启不重载</mark>
重启：自定义开发代码，加载位置restart类加载器
重载：jar包，加载位置base类加载器
1.导入坐标
```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-devtools</artifactId>  
</dependency>
```
2.build project
3.自动构建，setting和register中设置
#### 2.配置高级
###### 1.bean属性绑定
1@configurationProperties(prfix="上一级属性名")
2.@enableConfigurationProperties({配置类.class})：把使用了@ConfigurationPropertie的类进行注入
###### 2.常用计量单位
```java
@DurationUnit(ChronoUnit.HOURS)  
private Duration serverTimeOut;  //时间
@DataSizeUnit(DataUnit.GIGABYTES)  
private DataSize dataSize; //大小
```
###### 3.数据校验
导入坐标
```xml
<!--导入JSE303规范，用于数据校验-->  
<dependency>  
	<groupId>javax.validation</groupId>  
	<artifactId>validation-api</artifactId>  
</dependency>
<!--hibernate校验器实现类-->  
<dependency>  
	<groupId>org.hibernate.validator</groupId>  
	<artifactId>hibernate-validator</artifactId>  
</dependency>
```
实体类
```java
@Validated  
public class ServerConfig {  
    @Max(value = 8888,message = "port不能超过8888")  
    private int port;  
}
```
###### 4.web环境模拟测试
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)  
//开启虚拟mvc调用
@AutoConfigureMockMvc  
public class BookMapperTest {  
    @Test  
    void test1(@Autowired MockMvc mvc) throws Exception {
	    //  创建虚拟请求
        MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");  
        //执行请求
        mvc.perform(builder);  
    }  
}
```