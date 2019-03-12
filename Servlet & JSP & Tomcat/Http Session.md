# Http Session
- 会话中会经常使用到cookie，对cookie的处理均由容器统一处理，容器会将cookie封装在一个与cookie有关的对象中，将前者封装进`HttpSession`对象中。
- 若需要在servlet中使用cookie，则需要用到`HttpServletRequest`对象的`getSession()`方法，**无论是从请求Session中获得一个cookieID亦或者在响应response中发送一个cookie**。
  - 具体过程为：若从客户传递过来的请求中无cookieID/cookieID不匹配，则在调用`getSession()`的过程会创建一个新cookieID传入**新Session**对象中。（即：当容器知道你需要一个会话，便通过cookie确认是新建一个Session亦或者调用原先的Session）
  - 若有匹配上的cookieID则直接返回匹配上的Session对象。
- `session.isNew()`：判断会话是刚刚创建还是已存在，返回`boolean`。
- `request.getSession(boolean create)`：来判断是否需要在无匹配cookie对话时创建一个新的HttpSession对象，`true`为创建并返回一新HttpSession对象，`false`为返回null。
- 当容器发现servlet对请求创建一个新的会话对象（同过调用`request.getSession()`），则会创建一个新cookieID，对响应设置"Set-Cookie"的同时，向页面中存在的URL添加会话ID（cookieID的本质），双重保险。（切记前提为调用了`response.encodeURL()`）。
- `response.encodeRedirectURL(String URL)`：对URL编码的同时将请求重定向到另外一个URL。