# Javac的用法
- `javac -classpath`：
   - 指定依赖项(.class)的位置，如使用Tomcat的Servlet-api.jar文件，以及用户已编译好的.class文件
   - 对于.jar文件，需要在地址中标明全称：`文件名.jar`，若要使用全部的jar文件，使用`*.jar`
   -  对于需要使用到packet，无论是.java/.class，可以直接使用packet所在的相对根目录，会扫描对应目录的全部文件
- `javac -sourcepath`：
   - 指定将要编译的文件.java中需要用到的其他类所在的未编译.java文件
- `-d`：
   - 指定输出.class文件位置，缺省值为与.java文件相同的根目录下
- 在windows下使用多个路径使，用";"分隔，在linux下使用":"分隔。

# Java的用法
- 使用`java`时，需注意调用的class文件的名称应为：`full package name.class name` -> example: `com.test.ThreadTest`.
  attention: 无文件后缀.class，且运行时直接在package的相对根目录运行。如上"Javac"解释。
  example `javac com.test.ThreadTest`

### Pic of test RESULT

