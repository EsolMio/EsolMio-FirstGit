# Build Spring MVC
搭建Spring MVC有两种方法：配置java，web.xml
## 使用java配置Spring MVC
### 1. 配置`DispatchServlet`
`DispatchServlet`为Spring MVC的核心组件，而`AbstractAnnotationConfigDispatcherServletInitializer`为其的具体实现之一。要进行配置，需先对`AbstractAnnotationConfigDispatcherServletInitializer`继承