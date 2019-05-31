在程序清单 5.2中，继承的`WebMvcConfigurerAdapter`已过时，需要通过实现`WebMvcConfigurer`接口已完成替换。
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