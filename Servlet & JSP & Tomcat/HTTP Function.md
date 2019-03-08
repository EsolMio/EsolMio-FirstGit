# HTTP Function
### GET POST 的区别
- 传输参数时，GET没有体，直接出现在URL中（于URL之后，用'?'表示参数开始，用'&'分隔参数）。而POST将参数放置于体中，无'?' ，有'&'分隔。 详见"Head First servlet" p110。
- 在传输参数时，POST的安全性会相对高于GET（因POST将参数放置于体中）。
- 但是，**POST**为**非幂等**，**GET**为**幂等**，即意味着多个POST方法会产生多个不同的结果，POST体所提交的数据可能会用于**无法逆转的事务**，而多个GET方法只会产生一种结果。Tips：幂等，即多次的结果和一次相同。
- 对于servlet而言，POST方法并不是默认的，在html中若没有指明method参数，则默认使用GET方法。
- **Attention：** 若在servlet中没有对应HTTP方法（GET,POST）的处理方法（doGet(),doPost()）,容器会报错。