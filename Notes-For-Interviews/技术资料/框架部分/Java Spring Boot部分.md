Spring IOC

关键词：超级大工厂，对象控制权，解耦对象间的依赖关系

- 超级大工厂，对象控制权由调用者移交给容器，使得调用者不必关心对象的创建和管理，专注于业务逻辑开发；
- 优秀的解耦方式，解耦对象间的依赖关系，避免通过硬编码的方式耦合在一起；
- 底层实现：反射机制；

Spring AOP

关键词：模块化、交叉关注点、横切性质的系统级业务

一种新的模块化方式，专门处理系统各模块中的交叉关注点问题，将具有横切性质的系统级业务提取到切面中，与核心业务逻辑分离(解耦)；

便于系统的扩展，符合开-闭原则；

动态AOP的实现，Java动态代理(接口代理)与cglib(类代理)，具体由Bean后处理器生成代理；

AOP理念实践：Spring AOP，Java Web Filter，Struts2 Interceptor， SpringMVC Interceptor，…

