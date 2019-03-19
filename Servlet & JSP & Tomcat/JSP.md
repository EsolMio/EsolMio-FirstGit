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

3. 表达式: `<%= ... %>`，用于输出特定的值，只要返回值为`String`类型均可使用。
   Example: `<%= Counter.getCount() %>`
   Attention: 
    1. 最后没有结束分号";"，因为内部的值将作为参数传导至`out.write();`中。
    2. 内部的参数必须有返回值，且和`out.println();`可输出参数相对应。若返回值为`void`则掷出错误。

4. 声明: `<%! ... %>`，用于声明成员变量和成员方法。
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

