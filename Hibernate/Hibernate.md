# Hibernate
## 配置
*pdf-page 60*

- 在创建Configuration对象时，会加载classpath根路径下的，使用的`configure()`方法会加载classpath根目录下的`hibernate.properties` java属性文件（此文件并非绝对必要）；
- 在随后调用的`configure()`方法时会使用会自动查找classpath根路径下的配置文件`hibernate.cfg.xml` 文件（此文件绝对必要，且必须拥有全部的所需的配置信息，此文件的配置将会覆盖`hibernate.properties` 文件中的配置）。
	- 若配置文件不在classpath根目录下可以使用`configure()`方法的重载`configure(String path)`，指定配置文件所在的位置。
- 关于配置文件


