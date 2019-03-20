#Servlet
# Servlet的一生
- web容器（服务器）加载类（load .class file）
  Tips：不同的容器会提供不同的加载类时间，有些会在容器启动时同时加载，有些会在任何时刻加载
- 调用servlet的构造函数（不需要自行编写构造函数），此时的servlet尚不完整，无法正常使用
- web容器调用servlet继承的`init()`方法，此方法正式初始化servlet（因在使用默认构造函数初始化servlet对象时，容器也在初始化其他对象（如ServletConfig，ServletContext等），此时环境尚未搭建好）。**只调用一次**
- web容器调用servlet继承的`service()`方法，servlet的一生都在这个方法中度过，此方法会调用servlet中的`doGet()` / `doPost()`方法。用于处理用户的请求
- 调用servlet的`destory()`方法，回收servlet。**只调用一次** 

参考"Head First servlet" p97

# Servlet的调用
1. User -> `click URL`指向Servlet -> 容器获得客户发送的POST/GET请求
2. 容器 -> 创建两对象`HttpServletReponse` `HttpServletRequest`
3. 容器 according to -> URL -> serach CORRECT Servlet -> 创建或分配线程 -> 调用Servlet的`service()`方法
4. `service()`根据User的HTTP方法(GET/POST) -> 调用相应的Servlet方法(doGET/doPOST)
5. Servlet -> `HttpServletReponse` -> User
6. `service()`结束，对象回收

# 调用servlet时容器创建了什么
- 容器创建了：`HttpServletRequest`对象，`HttpServletResponse`对象。
- 容器通过调用了servlet的`service(request，response)`方法将请求对象和响应对象传递至servlet

详情参考"Head First servlet" p95-96

# 请求与线程
- **每个请求都在一个线程中运行**
- 对于每个请求，无论是否来自于同一个用户，容器均会提供一个单独线程/栈以运行。

详情参考"Head First servlet" p101

#

