# ServletConfig & ServletContext
### ServletConfig
- `ServletConfig`作用于单独的一个servlet，对servlet的配置参数的申明与配置需在DD（web.xml）中`<servlet></servlet>`中使用`<init-param></init-param>`标签包裹，使用`<param-name></param-name>`确定参数名称，使用`<param-value></param-value>`确定参数值。
- 在ServletConfig中参数名和参数值均是以String对象存储的。
- servlet在容器调用`init()`时获得了`ServletConfig`对象。
- **Attention:** 一个`ServletConfig`