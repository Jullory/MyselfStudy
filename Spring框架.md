# 基础篇

#### 1、Spring里使用了那些设计模式？

`单例模式`：Spring中的Bean默认情况下都是单例的

`工厂模式`：工厂模式主要通过BeanFactory和ApplicationContext来生产Bean对象

`代理模式`:最常见的AOP的实现方式就是通过代理模式来实现的，Spring主要是使用JDK动态代理和CGLIB代理

`模板方法模式`：主要是一些对数据库操作的类用到，比如JdbcTemplate、JpaTemplate，因为查询数据库的建立连接、执行查询、关闭连接几个过程，非常适用于模板方法。

#### 2、IOC和AOP的理解及实现原理

`IOC`叫做控制反转，指的是通过Spring来管理对象的创建、配置和生命周期，这样相当于把控制权交给了Spring，不需要人工来管理对象之间复杂的依赖关系，这样做的好处就是解耦。在Spring里面，主要提供了BeanFactory和ApplicationContext两种IOC容器，通过他们来实现对Bean的管理。

`AOP`叫做面向切面编程，他是一个编程范式，目的就是提高代码的模块性。SpringAOP基于动态代理的方式实现，如果是实现了接口的话就会使用JDK动态代理，反之则使用CGLIB代理，Spring中AOP的应用主要体现在代码的前后做一些增强处理，可以实现对业务逻辑的隔离，提高代码的模块化能力，同时也是解耦。Spring主要提供了Aspect切面、JoinPoint连接点、PointCut切入点、Advice增强等实现方式。

#### 3、JDK动态代理和CGLIB代理有什么区别

JDK动态代理主要是针对类实现了某个接口，AOP则会使用JDK动态代理。他基于反射的机制实现，生成一个实现同样接口的一个代理类，然后通过重写方法的方式，实现对代码的增强。

而如果某个类没有实现接口，AOP则会使用CGLIB代理。他的底层原理是基于asm第三方框架，通过修改字节码生成一个子类，然后重写父类的方法，实现对代码的增强。

#### 4、Spring AOP和AspectJ AOP有什么区别？

SpringAOP基于动态代理实现，属于运行时增强

AspectJ则属于编译时增强，主要有3种方式：

- 编织时织入：指的是增强的代码和源代码 我们都有，直接使用AspectJ编译器编译就行了，编译之后生成一个新的类，他也会作为一个正常的Java类装载到JVM

- 编织后织入：指的是代码已经被编译成class文件或者打成jar包，这时候要增强的话，就是编织后织入。比如你依赖了第三方的类库，又想对他增强的话，就可以通过这种方式

  ```xml
  <configuration>
  	<weaveDependencies>
      	<weaveDependency>
        
          </weaveDependency>
          <weaveDependency>
          
          </weaveDependency>
      </weaveDependencies>
  </configuration>
  ```
  
- 加载时织入：指的是在JVM加载类的时候进行织入。
总结：SpringAOP只能在运行时织入，不需要单独编译，性能相比AspectJ编译织入的方式慢，而AspectJ只支持编译前后和类加载时织入，性能更好，功能更强大。
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

Spring解决循环依赖有两个前提条件：

​	1.不全是构造器方式的循环依赖

​	2.必须是单例

我们知道Bean的生命周期，本质上解决循环依赖的问题就是三级缓存，通过三级缓存提前拿到未初始化的对象。

第一级缓存：用来保存实例化、初始化都完成的对象

第二级缓存:用来保存实例化完成，但是未初始化完成的对象

第三级缓存：用来保存一个对象工厂，提供一个匿名内部类，用于创建二级缓存中的对象

#### 8、为何要三级缓存？使用二级缓存不可以？





#### 9、Spring事务传播机制分类？

1、PROPAGATION_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，这也是通常我们的默认选择。

2、PROPAGATION_REQUIRES_NEW:创建新事务，无论当前存不存在事务，都创建新事务。

3、PROPAGATION_NESTED:如果挡墙存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行

4、PROPAGATION_NOT_SUPPORTED:以非事务方式执行操作，如果当前存在事务，就把当前事务挂起

5、PROPAGATION_NEVER:以非事务方式执行，如果当前存在事务，则抛出异常

6、PROPAGATION_MANDATORY:支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

7、PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。

#### 10、SpringBoot的启动流程

1、准备环境，根据不同的唤醒创建不同的Environment

2、准备、加载上下文，为不同的环境选择不同的SpringContext，然后加载资源，配置Bean

3、初始化，这个阶段刷新SpringContext，启动应用

4、结束流程

![img](https://raw.githubusercontent.com/Jullory/pic-memory/master/img/forTyproa/SpringBoot%E6%B5%81%E7%A8%8B%E5%9B%BE.png)