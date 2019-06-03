在程序清单 5.2中，继承的`WebMvcConfigurerAdapter`已过时，需要通过实现`WebMvcConfigurer`接口已完成替换。

WebConfigurer接口提供的`default`方法：
1. 使用获得参数`registry`的方法`jsp(String, String):UrlBasedViewResolverRegistration`配置`InternalResourceViewResolver`实例：
   ```
   public UrlBasedViewResolverRegistration jsp(String prefix, String suffix) {
		InternalResourceViewResolver resolver = new InternalResourceViewResolver();
		resolver.setPrefix(prefix);
		resolver.setSuffix(suffix);
		this.viewResolvers.add(resolver);
		return new UrlBasedViewResolverRegistration(resolver);
	}
   ```
2. `addResourceHandler(ResourceHandlerRegistry registry):void`，此方法用于添加一个资源处理器以处理来自web应用程序根目录下指定的文件（图像，css，js）以及类路径（classpath，适用于第三方的jar）
	1. `ResourceHandlerRegistry`实例提供了`addResourceHandler（`