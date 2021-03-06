# HttpServletRequest & HttpServletRespone
## HttpServletRequest
- 包含的几个主要使用的方法，包括继承下来的（**Remember**）：
  -  `getParameter(String name) return String `, `getParameterValues(String name) return String[] ` **Tips**:此方法用于返回**一个参数**带有**多个值**的情况, `getParameterNames() return Enumeration `
     这些方法都是返回从HTTP方法**GET/POST**中获得的参数，即客户在网页上做出操作后返回的参数。
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
  **值**。没有`addAttribute()`方法。
  - `removeAttribute(String name)`, 移除request中的对应属性
   （对应String name）。
  - `getRequestDispatcher(String path)`, path对应需要接收
    request请求的文件（如JSP，other servlet等），返回一个
    `RequestDispatcher`对象，此对象可用于传递本servlet接收到的请求和准备发送的响应，具体实现需调用RequestDispatcher `view.forward(request,response);`
  - `getInputStream()`, 获得一个IO输入流，返回
    `ServletInputStream`对象。
  - `getReader()`, 获得一个字符输入流，返回一个`BufferReader`对象。
  - `getServerPort()`, `getLocalPort()`, `getRemotePort()`的区别：
     - `getRemotePort()`, 获得远程的端口，"远程"是相对容器而言的远程端，即客户，转言即远程=客户。
     - `getLocalPort()`, `getServerPort()`, 前者指"请求最后送到哪个端口"，后者指"请求原本发送到哪个端口"。二者之所以有区别在于对于服务器而言，每个请求都发送到**同一个**端口（即服务器监听的端口），但为了满足多请求处理（多线程），服务器会为每个请求分派**不同**的**本地接口**

  - 与HTTP有关的方法，详见"Head First servlet" p122
  - 待补充...
- 待补充...

## HttpServletResponse
- 包含的几个重要方法（Remember）：
  - `getWriter()`, 获得一个字符输出流，专门用于处理Unicode系字符，返回一个`PrintWriter`对象。
  - `getOutputStream()`, 获得一个输出流，返回一个`ServletOutputStream`对象。
  - `sendRedirect(String path)`, 重定向一个URL，将此URL返回至客户。
  - `setContentType(String type)`, 设置返回客户响应的文件类型，type使用MIME类型，常见的MIME类型有：text/html, application.pdf, image/jpeg...
  - `setHeader(String name, String value)`, `addHeader(String name,String value)`, `setIntHeader(String name, int num)`, set方法用于往HTTP头部对应的参数名设置参数，**若无对应则添加新参数**；add方法用于往HTTP头部对应的参数值**添加**属性，**若无则添加新参数**。
  - `encodeURL(String URL)`，此方法会在指定参数`URL`后加上名为`jsessionid`的参数值，此参数值代表Session对象的ID，而非Cookie。
  - 待补充...

## RequestDispatcher
`RequestDispatcher`的实例只能通过`request`获得，当获得一个`RequestDispatcher`的实例后调用了它的`forward()`方法


## 为什么会有GenericServlet，ServletRequest，ServletResponse类/接口？不是只有Http系的类/接口就可以了吗？
提供了灵活性，以适应需要使用其他协议的servlet。




