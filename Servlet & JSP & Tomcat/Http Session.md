# Http Session
- 会话中会经常使用到cookie，对cookie的处理均由容器统一处理，容器会将cookie封装在一个与cookie有关的对象中，将前者封装进`HttpSession`对象中。
- 若需要在servlet中使用cookie，则需要用到`HttpServletRequest`对象的`getSession()`方法，**无论是从请求Session中获得一个cookieID亦或者在响应response中发送一个cookie**。
  - 具体过程为：若从客户传递过来的请求中无cookieID/cookieID不匹配，则在调用`getSession()`的过程会创建一个新cookieID传入**新Session**对象中。（即：当容器知道你需要一个会话，便通过cookie确认是新建一个Session亦或者调用原先的Session）
  - 若有匹配上的cookieID则直接返回匹配上的Session对象。
- 通过使用HttpSession对象的`.isNew()`判断会话是刚刚创建还是已存在，``。
