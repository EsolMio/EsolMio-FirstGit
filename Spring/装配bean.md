# 装配bean
使用`@Component`申明bean的原件
使用`@Configuration`申明配置类
使用`@bean`申明bean（于配置类中）
使用`@Qualifier`指定要装配的bean(当与`@AutoWrite`注解一同使用时使用此功能)（可用于filed，method，parameter，annotation_type，type，不能使用于constructor中，但可用于constructor的parameter中）（出现于存在多个compoment实现了同一个接口，同时在自动注入的属性中使用了接口类型）。
	- `@Qualifier`还可用于设置限定符，当使用此功能时需和`@Component`注解连用，这将替代由`@Bean`设定的bean id(也可以和`@Bean`连用)。
	- 