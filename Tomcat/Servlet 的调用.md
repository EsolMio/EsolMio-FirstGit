# Servlet的调用
1. User->`click URL`指向Servlet->容器获得客户发送的POST/GET请求
2. 容器->创建两对象`HttpServletReponse` `HttpServletRequest`
3. 容器 according to -> URL -> serach CORRECT Servlet -> 创建或分配线程 -> 调用Servlet的`service()`方法