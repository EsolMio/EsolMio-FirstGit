# Servlet的调用
1. User -> `click URL`指向Servlet -> 容器获得客户发送的POST/GET请求
2. 容器 -> 创建两对象`HttpServletReponse` `HttpServletRequest`
3. 容器 according to -> URL -> serach CORRECT Servlet -> 创建或分配线程 -> 调用Servlet的`service()`方法
4. `service()`根据User的HTTP方法(GET/POST) -> 调用相应的Servlet方法(doGET/doPOST)
5. Servlet -> `HttpServletReponse` -> User
6. `service()`结束，对象回收