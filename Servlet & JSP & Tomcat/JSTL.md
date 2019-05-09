# JSTL
JSTL分为简单标记和传统标记
- 简单标记：使用SimpleTag接口
- 传统标记：使用Tag接口

详细区分见下

## 准备tld文件和预先配置
使用JSTL之前，需要先将JSTL的.tld文件放置于WEB-INF文件夹中，容器会顺着WEB-INF文件夹向下查找（JSP 2.0），无需额外声明：
- 在WEB-INF的子文件夹查找
- 在WEB-INF/lib下的jar文件中的META-INF文件夹及其子目录中查找。

~~旧版本（JSP 2.0）之前需要在DD中声明`<jsp-config><taglib></taglib></jsp-config>`~~

## 1. 编写Tag对应的类
### i. 编写简单Tag
1. 类需要继承SimpleTagSupport


