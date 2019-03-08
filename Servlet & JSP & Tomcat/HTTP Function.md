# HTTP Function
### GET POST 的区别
- 传输参数时，GET没有体，直接出现在URL中（于URL之后，用'?'表示参数开始，用'&'分隔参数）。而POST将参数放置于体中，无'?' ，有'&'分隔。 详见"Head First servlet" p110。
- 在传输参数时，POST的安全性会相对高于GET（因POST将参数放置于体中）。
- 但是，POST为非幂等，GET为幂等，即意味着多个POST方法会产生多个不同的结果，而多个GET方法只会产生一种结果。