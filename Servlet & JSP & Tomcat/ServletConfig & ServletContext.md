# ServletConfig & ServletContext
### ServletConfig
- `ServletConfig`作用于单独的一个servlet，对servlet的配置参数的申明与配置需在DD（web.xml）中`<servlet></servlet>`中使用`<init-param></init-param>`标签包裹，使用`<param-name></param-name>`确定参数名称，使用`<param-value></param-value>`确定参数值。
- 在ServletConfig中参数名和参数值均是以String对象存储的。
- servlet在容器调用`init()`时获得了`ServletConfig`对象。
- **Attention:** 一个`ServletConfig`对象仅作用于唯一指定的servlet。

### ServletContext
- `ServletContext`作用于全部web应用（servlet & JSP）。在DD中配置Context，**不嵌套在`<servlet></servlet>`中**，使用`<context-param></context-param>`包裹，`<param-name></param-name>`确定参数名称,`<param-value></param-value>`确定参数值。

### ServletContextListener
- 用于监听ServletContext的生命周期，即在**创建新的**ServletContext对象后会调用此监听
- 使用listenner



### 图示ServletConfig和ServletContext的差距



