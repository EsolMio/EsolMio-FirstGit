# Hibernate
## 配置
*pdf-page 60*

- 在创建Configuration对象时，会加载classpath根路径下的，使用的`configure()`方法会加载classpath根目录下的`hibernate.properties` java属性文件（此文件并非绝对必要）；
- 在随后调用的`configure()`方法时会使用会自动查找classpath根路径下的配置文件`hibernate.cfg.xml` 文件（此文件绝对必要，且必须拥有全部的所需的配置信息，此文件的配置将会覆盖`hibernate.properties` 文件中的配置）。
	- 若配置文件不在classpath根目录下可以使用`configure()`方法的重载`configure(String path)`，指定配置文件所在的位置。
- 关于配置文件xml，需要在`<property>`标签的`name`属性中声明:
	- `binernate.connection.driver_class`：声明所使用的JDBC驱动
	- `hibernate.connection.url`：声明连接地址, 
	- `hibernate.connection.username`：声明登录数据库用户名, 
	- `hibernate.dialect`

## 配置连接池

### 使用连接池的原因：
1. 创建新的连接代价很大
2. 维护闲置连接耗费的成本很大
3. 创建预编译（新编译）的查询语句需要耗费的性能

Hibernate内置了c3p0连接池，当然也可以使用其他的连接池




