# Spring

## 什么是Spring

Spring是一个包含了一系列功能的Java EE应用开发框架，其包含了快速开发的SpringBoot、构建微服务的Spring Cloud，支持认证与鉴权的Spring Security，Web框架Spring MVC等，其核心是IOC（控制反转）和AOP（面向切面编程）；

## IOC

- 控制反转：由Spring IOC容器来负责对象的生命周期，没有IOC的时候我们会在自己的对象中主动创建被依赖的对象，有了IOC后，所依赖的对象直接由IOC容器创建后注入到被注入的对象中，依赖的对象由之前的主动获取变为被动接受，所以是反转；
- 依赖注入：容器动态的将依赖关系注入到组件中，提升组件重用的频率，通过依赖注入机制，我们只需要通过简单的配置，就可以获取需要的对象，不需要关心具体的资源来自何处，由谁实现；

## AOP

面向切面编程，可以通过设置切入点，将切入点处的重复代码抽取出来，常用于权限管理、日志等；

### spring AOP如何实现

https://www.zhihu.com/question/23641679?sort=created

## Bean

### Bean的生命周期

1. Spring对Bean进行实例化；
2. Spring将值和Bean的引用注入到Bean对应的属性中；
3. 容器通过Aware接口把容器信息注入Bean；
4. BeanPostProcessor，进行进一步的构造，会在InitialzationBean前后执行对应方法，当前正在初始化的bean对象会被传递进来，我们就可以对这个bean做任何处理；
5. InitializingBean，在bean正式构造完前增加我们自定义的逻辑；
6. DisposableBean，bean将一直保留在应用上下文给应用使用，直到应用上下文被销毁；

### Bean的作用域

- singleton：单例模式，Spring IOC容器中只存在一个共享的Bean实例，无论有多少个Bean引用它，都始终指向同一对象；
- prototype：原型模式，每次通过Spring容器获取prototype定义的bean时，容器都将创建一个新的实例；
- request：在一个HTTP请求中，容器会返回该bean的同一实例，对不同的HTTP请求会产生新的Bean，bean仅在当前HTTP请求内有效；
- session：在一次HTTP session中，容器会返回该Bean的同一实例，对不同的Session创建新的实例，实例仅在当前Session内有效；
- global session：在一个全局的HTTP Session中，容器会返回该Bean的同一个实例，仅在使用portlet context时有效；

## 如何解决循环依赖

https://zhuanlan.zhihu.com/p/84267654

无参数构造器、字段注入；

## SpringMVC处理流程









