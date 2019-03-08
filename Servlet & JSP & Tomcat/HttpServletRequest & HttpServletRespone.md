# HttpServletRequest & HttpServletRespone
### HttpServletRequest
- 包含的几个主要使用的方法（**Remember**）：
  -  `getParameter() return String `, `getParameterValues() return String[] `, `getParameterNames() return Enumeration `
     这些方法都是返回从HTTP方法**GET/POST**中获得的参数
     - example：
     ```
     //GET:
     GET /select/selcetBeer.jsp?color=dark&taste=malty HTTP/1.1
     //'?'之后均为参数（Parameter），使用'&'分隔多个参数

     //POST
     POST /advisor/selectBeer.do HTTP1.1
     //Header...
     
     color=dark&taste=malty
     //信息体，或称为负载
     ```
     - Tips：GET和POST之间的区别
       GET和POST都能发送参数（Parameter），但POST将参数放置在
       信息体中，并不直接显示在URL地址栏中。
  - `getAttribute(String name)`， 用于获得request属性，返回
  为`Object`对象。
  - `setAttribute(String name, Object value)`, 
  **Attention:** 方法内参数有两个，对应Attribute的**名称**和
  **值**。
  - `removeAttribute(String name)`, 移除request中的对应属性
   （对应String name）。
  - `getRequestDispatcher(String path)`, path对应需要接收
    request请求的文件（如JSP，other servlet等），返回一个
    RequestDispatcher对象。
  - 待补充...
- 待补充...

### HttpServlet

