# 基础篇

#### 1、Spring里使用了那些设计模式？

`单例模式`：Spring中的Bean默认情况下都是单例的

`工厂模式`：工厂模式主要通过BeanFactory和ApplicationContext来生产Bean对象

`代理模式`:最常见的AOP的实现方式就是通过代理模式来实现的，Spring主要是使用JDK动态代理和CGLIB代理

`模板方法模式`：主要是一些对数据库操作的类用到，比如JdbcTemplate、JpaTemplate，因为查询数据库的建立连接、执行查询、关闭连接几个过程，非常适用于模板方法。

#### 2、IOC和AOP的理解及实现原理





#### 3、JDK动态代理和CGLIB代理有什么区别



#### 4、Spring AOP和AspectJ AOP有什么区别？



#### 5、FactoryBean和BeanFactory有什么区别？

BeanFactory是Bean的工厂，ApplicationContext的父类，IOC容器的核心，负责生产和管理Bean对象

FactoryBean是Bean，可以通过实现FactoryBean接口定制实例化Bean的逻辑，通过代理一个Bean对象，对方法前后做一些操作

#### 6、SpringBean的生命周期

SpringBean的生命周期简单概括为4个阶段：

1、实例化，创建一个Bean对象

2、填充属性，为属性赋值

3、初始化

- 如果实现了`xxxAware`接口，通过不同类型的`Aware`接口拿到Spring容器的资源
- 如果实现了`BeanPostProcessor`接口，则会回调该接口的`postProcessBeForeInitialzation`和`postProcessAfterInitialization`方法
- 如果配置了`init-method`方法，则会执行`init-method`配置的方法

4、销毁

- 容器关闭后，如果Bean实现了`DisposableBean`接口，则会回调该接口的`destory`方法
- 如果配置了`destory-method`方法，则会执行`destory-method`配置的方法

#### 7、Spring如何解决循环依赖问题





#### 8、为何要三级缓存？使用二级缓存不可以？





#### 9、Spring事务传播机制分类？





#### 10、SpringBoot的启动流程

1、准备环境，根据不同的唤醒创建不同的Environment

2、准备、加载上下文，为不同的环境选择不同的SpringContext，然后加载资源，配置Bean

3、初始化，这个阶段刷新SpringContext，启动应用

4、结束流程

![img](https://raw.githubusercontent.com/Jullory/pic-memory/master/img/forTyproa/SpringBoot%E6%B5%81%E7%A8%8B%E5%9B%BE.png)