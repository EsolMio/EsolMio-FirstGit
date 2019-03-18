# JSP

## JSP元素
1. Scriptlet: `<% ... %>`
   Example: `<% InputStream in=req.getInputStram(); %>`

2. 指令: `<%@ ... %>`
   Example: `<%@ page import="java.util.*, test.example.*"`

3. 表达式: `<%= ... %>`
   Example: `<%= Counter.getCount() %>`
   Attention: 最后没有结束分号";"，因为内部的值
