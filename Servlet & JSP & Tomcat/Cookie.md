# Cookie
虽然可以通过使用`request.getSession()`的方式触发：
- 在request中无匹配/本身就无cookie下创建一个新Cookie对象
- request中有匹配上的cookie下获得一个对应的Cookie对象

但我们也可以自行获得一个`Cookie`对象：
- 通过`new Cookie(String cookieName, String cookieValue)`创建新cookie对象，对`cookieName`, `cookieValue`的自定义获得符合自己要求的Cookie对象。
- 通过`request.getCookies() return Cookie[]`的方式获得`Cookie[]`对象，在使用遍历寻找对应的cookie(Tips: use `cookie[i].getName.equals(String name)`)。

在对Cookie更改后需要记得将Cookie重新传回响应response中，使用`response.addCookie(Cookie cookie);`
