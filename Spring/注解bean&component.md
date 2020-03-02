# `@Bean` & `@Compoenet` 区别
- `@Component`告知Spring 容器此注解所注解的**类**将会是bean，`@Autowired`自动注入的bean实例**将通过默认构造器构造**。
- `@Bean`告知Spring 容器此注解所注解的**方法**将返回一个实例，此实例将会注册到Spring Context中成为一个bean。方法体中包含bean生成的逻辑，**而非单纯使用默认构造器**。`@Bean`需要在`@Configuration`注解的配置类中声明。