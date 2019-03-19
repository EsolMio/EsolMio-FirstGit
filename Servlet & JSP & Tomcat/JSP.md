# JSP
JSP本质为servlet，由容器转换。

## JSP元素
1. Scriptlet: `<% ... %>`，用于存放普通的Java代码。
   Example: `<% InputStream in=req.getInputStram(); %>`

2. 指令: `<%@ ... %>`，用于向容器Container传递特殊指令。
   Example: `<%@ page import="java.util.*, test.example.*"`
   `import`的包通过属性形式输入。

3. 表达式: `<%= ... %>`，用于输出特定的值。
   Example: `<%= Counter.getCount() %>`
   Attention: 
    1. 最后没有结束分号";"，因为内部的值将作为参数传导至`out.write();`中。
    2. 内部的参数必须有返回值，且和`out.println();`可输出参数相对应。若返回值为`void`则掷出错误。

4. 声明: `<%! ... %>`，用于声明成员变量和成员方法。
   Example: `<% int count = 0; %>`


