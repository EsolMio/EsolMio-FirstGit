# JSP
JSP本质为servlet，由容器转换。

## JSP元素
1. Scriptlet: `<% ... %>`，用于存放普通的Java代码。不需要代码完整，即可以中途截断，但后面需要将其补充完整。
   Example: 
   ```
   <% while(it.hasNext()){ %>
   <%= it.next() %><%-- 被此标签块切断了，但下方的补充使其完整 -->
   <% } %>
   ```

2. 指令: `<%@ ... %>`，用于向容器Container传递特殊指令。
   Example: `<%@ page import="java.util.*, test.example.*"`
   `import`的包通过属性形式输入。
	1. page指令：设定页面的属性，其中包括了：
		1. `contextType`-设置页面类型；
		2. `import`-设置jsp中要包含的package（包）；
		3. `language`-当前使用scriptlet的语言（如"java"）；
		4. `isErrorPage`-鉴定当前jsp是否时另一个jsp的错误页面，默认为`false`。若为`true`（即）则此jsp可访问被隐藏的实现`Throwable`的对象。 
	2. taglib指令：定义JSP可使用指定的标记库。
	3. include指令：将指定的jsp/html内的内同融合进本jsp，即允许建立可从用的块。
		- 看上去和jsp动作`<jsp:include>`相似，实际实现方法不同。



3. 表达式: `<%= ... %>`，用于输出特定的值，只要返回值为`String`类型均可使用。
   Example: `<%= Counter.getCount() %>`
   Attention: 
    1. 表达式内部的参数会自动调用`toString()`。
    2. 最后没有结束分号";"，因为内部的值将作为参数传导至`out.write();`中。
    3. 内部的参数必须有返回值。若返回值为`void`则掷出错误。

4. 声明: `<%! ... %>`，用于声明/覆盖成员变量和成员方法。
   Example: `<% int count = 0; %>`

5. 隐式对象：JSP本质也为servlet，由container容器自动生成，故也需要一些对象以获得数据：
	1. JspWriter -- out //与从response获得的PrintWriter不用，多了缓冲功能。
	2. HttpServletRequest -- request
	3. HttpServletResponse -- response
	4. HttpSession -- session
	5. ServletContext -- application
	6. ServletConfig -- config
	7. Throwable -- exception
	8. PageContext -- pageContext
	9. Object -- page

## 初始化JSP(转换和编译只发生一次)
1. 使用配置文件DD：
	1. 需要在`<servlet>`标签内声明。
	2. Example:

           ```
           <servlet>
               <servlet-name>JspInit</servlet-name>
               <jsp-file>/TestInit.jsp</jsp-file>
           </servlet>
           ```
      3. Tips: `<jsp-file>`中为jsp文件所在的目录。


2. 初始化，覆盖`jspInit()`
   - 如何覆盖？ 使用`<%! ... %>`声明标签，在其中申明自定义的`jspInit()`方法。容器默认先调用自定义的`jspInit()`。

3. 执行，容器调用`_jspService()`

## jsp中的`PageContext`对象
- `PageContext`，JSP文件中的上下文对象，作用域在由jsp转换成的servlet中。
- `PageContext`拥有获得四个作用域- `application`, `pageContext`, `request`, `session`属性（Attribute）的能力
	- 使用`getAttribute(String attributeName, int scope)`，`scope`可为`PageContext`中的四个静态变量：`APPLICATION_SCOPE`, `PAGE_SCOPE`, `REQUEST_SCOPE`, `SESSION_SCOPE`。
	- 使用`setAttribute(String attributeName, Object value, int scope)`往指定的`scope`添加值。
	- p.s. 在使用无`int scope`参数的`setAttribute()`, `getAttribute()`时均默认为`pageContext`。

## 


