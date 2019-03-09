# ServletConfig & ServletContext & Listener
### ServletConfig
- `ServletConfig`作用于单独的一个servlet，对servlet的配置参数的申明与配置需在DD（web.xml）中`<servlet></servlet>`中使用`<init-param></init-param>`标签包裹，使用`<param-name></param-name>`确定参数名称，使用`<param-value></param-value>`确定参数值。
- 在ServletConfig中参数名和参数值均是以String对象存储的。
- servlet在容器调用`init()`时获得了`ServletConfig`对象。
- **Attention:** 一个`ServletConfig`对象仅作用于唯一指定的servlet。

### ServletContext
- `ServletContext`作用于全部web应用（servlet & JSP）。在DD中配置Context，**不嵌套在`<servlet></servlet>`中**，使用`<context-param></context-param>`包裹，`<param-name></param-name>`确定参数名称,`<param-value></param-value>`确定参数值。

### ServletContextListener
- 用于监听ServletContext的生命周期，即在**创建新的**ServletContext对象后会调用此监听，在**销毁**ServletContext也会调用此监听。
- 使用Listener需要三步：
    1. 创建一个监听类，实现 ( implements ) ServletContextListener接口，其中要实现`contextInitialized(ServletContextEvent)` 和 `contextDestroyed(ServletContextEvent)`。
    2. 将监听类放置于WEB-INF/classes下。
    3. 在DD（部署描述文件）中放置`<listener></listener>`，其中包括了`<listener-class></listener-class>`标签，用于申明使用了哪个监听类，如：`<listener-class> com.example.MyListener </listener-class>`
- 监听类中的方法可使用自变量参数获得相应的监听对象，如`contextInitialized(ServletContextEvent event)`中获得了`ServletContextEvent` event对象，通过`event.getServletContext()`获得一个ServletContext对象。

### Other Listener
- 属性监听者：
    - `ServletRequestAttributelistener`
    - `ServletContecxtAttrubuteListener`
    - `HttpSessi`





### 图示ServletConfig和ServletContext的差距



