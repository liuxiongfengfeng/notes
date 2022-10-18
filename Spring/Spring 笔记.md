# 1.Spring概述
1.Spring是一个开源的轻量级的javaEE框架
2.Spring 可以解决企业开发的复杂性
3.Spring有两个核心部分：IOC和AOP
# 2.IOC
#### 1.概念和原理
##### （1）什么是IOC
控制反转，将创建对象和对象之间的调用关系交给spring管理，目的是解耦
##### （2）底层原理
xml解析，工厂模式，反射，IOC思想基于IOC容器完成，IOC容器底层就是一个对象工厂
##### （3）spring提供IOC实现的两种方式
BeanFactory：IOC容器的一种最基本的实现方式，是spring内部实用的接口，开发时一般不使用，加载配置文件时不会创建容器中的对象，获取对象使才创建对象

ApplicationContext：BeanFactory的一个子接口，加载配置文件时创建容器中的对象

#### 2.Bean管理
（1）bean管理就是创建对象和注入属性
```
<bean id="user" class="com.xiongfeng.pojo.User"></bean>
```
默认执行的使无参构造
（2）依赖注入：set注入，有参构造注入
# 3.事务
1.提供了一个接口，PlatformTransactionManager接口争对不同的框架提供了不同的实现类