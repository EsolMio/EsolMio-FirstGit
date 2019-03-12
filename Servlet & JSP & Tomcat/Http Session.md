# Http Session
- 会话中会经常使用到cookie，对cookie的处理均由容器统一处理，容器会将cookie封装在一个与cookie有关的对象中，将前者封装进`HttpSession`对象中。
- 若需要在servlet中使用cookie需要用到`HttpServletRequest`对象的`getSession()`方法，**无论是从请求Session中获得一个cookieID亦或者在响应response中发送一个cookie**。
  - 其实本质上并不是因调用`getSession()`触发才导致一下过程发生，具体过程为：若请求request中的Session无cookieID/cookID无法与数据库中的匹配上，