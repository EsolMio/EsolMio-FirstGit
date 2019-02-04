# Javac的用法
- `javac -classpath`：
   - 指定依赖项(.class)的位置，如使用Tomcat的Servlet-api.jar文件，以及用户已编译好的.class文件
   - 对于.jar文件，需要在地址中标明全称：`文件名.jar`，若要使用全部的jar文件，使用`*.jar`
   -  对于需要使用到packet，无论是.java/.class，可以直接使用packet所在的相对根目录，会扫描对应目录的全部文件
- `javac -sourcepath`：
   - 指定将要编译的