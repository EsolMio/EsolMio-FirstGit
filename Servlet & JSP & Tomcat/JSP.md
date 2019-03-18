# JSP

## JSP元素
1. Scriptlet: `<% ... %>`
   Example: `<% InputStream in=req.getInputStram(); %>`

2. 指令: `<%@ ... %>`
   Example: `<%@ page import="java.util.*, test.example.*"`

3. 表达式: `<%= ... %>`
   Example: `<%= Counter.getCount() %>`
   Attention: 
    1. 最后没有结束分号";"，因为内部的值将作为参数传导至`out.println();`中。
    2. 内部的参数必须有返回值，且和`out.println();`可输出参数相对应。若返回值为`void`则掷出错误。
    3. 
