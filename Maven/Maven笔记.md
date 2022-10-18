## 1.Maven简介
> ### 1.Maven本质是一个项目管理工具，将项目开发和管理过程抽象成一个项目对象模型
> ### 2.Maven的作用
> > #### 1.项目构建：提供标准的、跨平台的自动化项目构建方式
> > #### 2.依赖管理：方便快捷的管理项目依赖的资源（jar包），避免资源间版本冲突
 >> #### 3.统一开发结构：提供标准的、项目结构![[maven基本工作流程.png]]
## 2.Maven下载与配置
> #### 1.去官网下载安装
> #### 2.配置环境
>>![[Maven环境配置.png]]
## 3.Maven基本概念
### 1.仓库：用于存储资源（jar包）
#### 1.仓库分类
##### 1.本地仓库：自己电脑上用于存储资源的仓库
##### 2.远程仓库：非自己电脑上，用于提供资源的
1.中央仓库：maven团队维护
1.私服：公司或部门范围内存储的仓库，从中央仓库获取资源 
## 2.坐标
1.用于标注仓库中资源的位置
#### 2.Maven坐标主要组成
1.groupId：定义当前Maven项目隶属组织名称（通常域名反写，org.mybatis）
2.artifactId：定义当前Maven项目名称
3.version：定义当前项目版本号
4.packaging：定义当前项目的打包方式
## 3.仓库配置
1.在setting.conf文件中将本地仓库位置修改到指定位置
```xml:
<localRepository>F:\maven\repository</localRepository>
```
2.配置国内镜像
```xml:
	<mirror>
      <id>aliyun</id>
      <mirrorOf>*</mirrorOf>
      <name>aliyun</name>
      <url>https://maven.aliyun.com/repository/public</url>
   </mirror> 
```
## 4.第一个maven项目
## 1.maven项目构建命令
1.mvn compile：编译
2.mvn clean：删除编译文件
3.mvn test：测试
4.mvn package：打包
5.mvn install：安装到本地仓库
## 5.安装插件
1.在pom.xml中配置
	例如：
``` xml:
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.tomcat.maven<groupId>
			<artifactId>tomcat7-maven-plugin</artifactId>
			<version>2.1</version>
			<configuration></configuration>
		</plugin>
	</plugins>
</build>
```

## 6.依赖管理
#### 1.依赖配置，pom.xml
	例如：
```xml:
<dependencies>  
    <dependency>
        <groupId>junit</groupId>  
        <artifactId>junit</artifactId>  
        <version>4.12</version>  
        <scope>test</scope>  
	</dependency>
</dependencies>
```
1.直接依赖：在当前项目中通过依赖配置建立的依赖关系
2.间接依赖：被资源的资源如果依赖其他资源，则当前项目间接依赖其他资源
3.依赖具有传递性
#### 2.以来传递冲突问题
1.路径优先：当依赖中出现相同资源，层级越深，优先级越低
2.声明优先：当相同依赖处于同层，配置顺序靠前的覆盖配置顺序靠后的
3.特殊优先：当相同依赖处于同层，版本不同时，配置顺序靠后的覆盖配置顺序靠前的
#### 3.可选依赖：使依赖对外呈现不透明的效果
在配置中添加
```xml:
<optional>true</optional>
```
#### 4.排除依赖：主动断开依赖（不需要的依赖）
例如：
```xml:
<dependency>  
    <groupId>org.example</groupId>  
    <artifactId>firstDemo</artifactId>  
    <version>1.0-SNAPSHOT</version>  
    <exclusions>        
	    <exclusion>
	        <groupId>log4j</groupId>  
            <artifactId>log4j</artifactId>  
        </exclusion>
    </exclusions>
</dependency>
```
#### 5.依赖范围：依赖的jar包默认在任何位置都能使用，通过配置可指定其只能在特定范围使用
例如：
```xml:
<dependencys>
	<dependency>
		<groupId>log4j</groupId>
		<artifactId>log4j</artifactId>
		<scope>test</scope>
	</denpendency>
</dependencys>
```
1.测试程序范围有效（test文件夹）
2.主程序范围有效（main文件夹）
3.是否参与打包（package指令有效）
![[作用范围.png]]
## 7.生命周期和插件
1.插件和生命周期内的阶段绑定，在执行到对应的生命周期执行对应的插件功能
## 8.分模块开发与设计
1.将项目分层拆分，该层只保留该层的东西其他东西全部去掉
## 9.聚合
1.创建一个module只留pom.xml
2.定义该工程用于构建管理，只提供pom文件
```xml:
<packaging>pom</packaging>
```
3.管理的module列表
```xml:
<modules>  
    <module>../ssm_pojo</module>  
    <module>../ssm_dao</module>  
    <module>../ssm_service</module>  
    <module>../ssm_controller</module>  
</modules>
```
## 10.继承
#### 1.在构建多个模块的项目时，往往多个模块有相同的依赖，为减少依赖冲突，统一依赖版本。可将一个项目分为父子工程，父工程进行依赖的版本管理，子工程引入依赖时可不必配置版本，延用父工程配置的版本
#### 2.父工程
```xml:
<dependencyManagement>
	<denpendencies>
		...
		依赖
		...
	</denpendencies>
</dependencyManagement>

<build>  
    <pluginManagement>
	    ...
	    插件
		...
    </pluginManagement>  
</build>
```
#### 3.子工程
```xml:
<parent>  
    <groupId>com.xiongfeng</groupId>  
    <artifactId>ssm</artifactId>  
    <version>1.0-SNAPSHOT</version>
    <!--相对路径，为父工程的pom.xml-->  
    <relativePath>../ssm/pom.xml</relativePath>  
</parent>
```
## 11.属性
```xml:
<properties>  
    <spring.version>5.3.23</spring.version>  
</properties>

<dependencyManagement>  
    <dependencies>    
	    <dependency>
		    <groupId>org.springframework</groupId>  
	        <artifactId>spring-context</artifactId>  
	        <version>${spring.version}</version>  
	    </dependency>
    </dependencies>
</dependencyManagement>
```
## 12.资源配置
1.父工程pom.xml
```xml:
<properties>  
    <maven.compiler.source>8</maven.compiler.source>  
    <maven.compiler.target>8</maven.compiler.target>  
    <spring.version>5.3.23</spring.version>  
    <jdbc.driver>com.mysql.jdbc.Driver</jdbc.driver>  
    <jdbc.url>jdbc:mysql://127.0.0.1:3306/test01</jdbc.url>  
    <jdbc.username>root</jdbc.username>  
    <jdbc.password>123456</jdbc.password>  
</properties>

<build>  
    <!--配置资源文件对应信息-->  
    <resources>  
        <resource>            
        <!--配置文件对应的位置目录-->  
	        <directory>${project.basedir}/src/main/resources</directory>  
            <!-- 开启配置文件资源加载过滤-->  
            <filtering>true</filtering>  
        </resource>    
    </resources>    
    <!--配置test文件资源对应信息-->  
    <testResources>  
        <testResource>            
		    <directory>${project.basedir}/src/main/resources</directory>  
	        <filtering>true</filtering>  
        </testResource>    
    </testResources>
</build>
```
2.子文件资源配置文件
```properties:
jdbc.driver=${jdbc.driver}  
jdbc.url=${jdbc.url}  
jdbc.username=${jdbc.username}  
jdbc.password=${jdbc.password}
```
## 13.多环境配置
```xml:
  <!--配置多环境-->  
    <profiles>  
        <!--开发环境-->  
        <profile>  
            <id>dev_env</id>  
            <properties>                <jdbc.driver>com.mysql.jdbc.Driver</jdbc.driver>  
                <jdbc.url>jdbc:mysql://127.0.0.1:3306/test01</jdbc.url>  
                <jdbc.username>root</jdbc.username>  
                <jdbc.password>123456</jdbc.password>  
            </properties><!--            &lt;!&ndash;激活&ndash;&gt;-->  
<!--            <activation>-->  
<!--                &lt;!&ndash;配置默认激活环境&ndash;&gt;-->  
<!--                <activeByDefault>true</activeByDefault>-->  
<!--            </activation>-->  
        </profile>  
        <!--生产环境-->  
        <profile>  
            <id>pro_env</id>  
            <properties>                <jdbc.driver>com.mysql.jdbc.Driver</jdbc.driver>  
                <jdbc.url>jdbc:mysql://192.168.2.129:3306/test01</jdbc.url>  
                <jdbc.username>root</jdbc.username>  
                <jdbc.password>123456</jdbc.password>  
            </properties>            <!--激活-->  
            <activation>  
                <!--配置默认激活环境-->  
                <activeByDefault>true</activeByDefault>  
            </activation>        </profile>    </profiles>
```
