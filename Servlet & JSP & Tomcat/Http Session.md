# Http Session
### Session的使用
- 若使用了`request.getSeesion()`：
  - 若没有
  - 若有匹配上的cookieID则直接返回匹配上的Session对象。
  - 第一次调用`request.getSession()`时容器会自动在response中发送回一个Cookie。
- `session.isNew()`：判断会话是刚刚创建还是已存在，返回`boolean`。
- `request.getSession(boolean create)`：来判断是否需要在无匹配cookie对话时创建一个新的HttpSession对象，`true`为创建并返回一新HttpSession对象，`false`为返回null。
- 当容器发现servlet对请求创建一个新的会话对象（同过调用`request.getSession()`），则会创建一个新cookieID，对响应设置"Set-Cookie"的同时，向页面中存在的URL添加会话ID（cookieID的本质），双重保险。（切记前提为调用了`response.encodeURL()`）。
- `response.encodeRedirectURL(String URL)`：对URL编码的同时将请求重定向到另外一个URL。
### HttpSession对象的方法（常用）
- `getCreationTime()`：返回创建此会话对象的时间，`return long`类型，自GMT1970/1/1开始以毫秒为单位。
- `getLastAccessedTime()`：返回容器最后一次收到此会话用户请求后过去的时间，`return long`，以毫秒为单位。
- `setMaxInactiveTime(int interval)`：设置此会话客户请求的最长间隔时间（即session的生存时间），以秒为单位。
- `getMaxInactiveInterval()`：获得此会话客户请求的最大间隔时间，`return int`，以秒为单位。
- `invalidate()`：结束会话，将原本的会话ID无效化。
- **CAUTION!!!**：在"Head First servlet & jsp"p249中**有误**，其称使用`session.setMaxInactiveInterval(0)`时会话对象会立刻超时，而在servlet doc中的说明与其相反：
  > A zero or negative time indicates that the session should never timeout.
0/负数表示会话永不超时

### 设置会话Session的配置
设置会话配置需要在DD中配置，在主标签`<web-app></web-app>`下使用`<session-config></session-config>`中配置申明
- 配置Session超时值：`<session-timeout>timeout</session-timeout>`
