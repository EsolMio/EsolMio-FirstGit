# Hibernate
## 配置
*pdf-page 60*

- 在创建Configuration对象时，会加载classpath根路径下的，使用的`configure()`方法会加载classpath根目录下的`hibernate.properties` java属性文件（此文件并非绝对必要）；
- 在随后调用的`configure()`方法时会使用会自动查找classpath根路径下的配置文件`hibernate.cfg.xml`

