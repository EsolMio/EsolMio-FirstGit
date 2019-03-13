# `InetAddress`类
`InetAddress`类的方法：
- `InetAddress.getByName(String URL);`，static修饰/类方法，通过参数URL获得对应的主机IP地址。可以通过`InetAddress.getByName(null);`将参数设置为`null`获得"localhost"的IP地址。