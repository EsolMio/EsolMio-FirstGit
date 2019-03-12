# Http Session
- 会话中会经常使用到cookie，对cookie的处理均由容器统一处理，容器会将cookie封装在一个与cookie有关的对象中。
- 若需要在servlet中使用cookie需要用到`HttpServletRequest`对象的`get`