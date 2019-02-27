# 使用Enumeration接口接收多值
一般若需要同时接收来自一个对象多个值，再对象内定义的方法可行下，可使用`Enumeration`接收
`Enumeration`包含两个方法：
`hasMoreElements() return boolean`,返回值说明枚举中是否还有元素
`nextElements() return E`,返回值为