# JavaNet

## `InetAddress`

`InetAddress`类的方法，此类可理解为IP对象：
- `InetAddress.getByName(String host);`，static修饰/类方法，通过参数URL获得对应的主机IP地址。可以通过`InetAddress.getByName(null);`将参数设置为`null`获得"localhost"的IP地址。
- 获得`InetAddress`对象的常用且仅可使用的`static`方法：
  - `getByName(String host)`
  - `getAllByName(String host)`，`return InetAddress[]`
  - `getLocalHost()`
  **CAUTION**：这几个方法均会`throws`错误，注意在方法中声明或者使用`try{}catch{}`。

## `ServerSocket`

- 