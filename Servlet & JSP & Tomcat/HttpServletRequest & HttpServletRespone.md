# HttpServletRequest & HttpServletRespone
### HttpServletRequest
- 包含的几个主要使用的方法（**Remember**）：
  -  `getParameter() return String `, `getParameterValues() return String[] `, `getParameterNames() return Enumeration `
     这些方法都是返回从HTTP方法GET/POST中获得的参数
     example：
     ```
     //GET:
     GET /select/selcetBeer.jsp?color=dark&taste=malty HTTP/1.1
     //'?'之后均为参数（Parameter），使用'&'分隔多个参数

     //POST
     POST /advisor/selectBeer.do HTTP1.1
     //Header...
     
     color=dark&taste=malty
     //
     ```