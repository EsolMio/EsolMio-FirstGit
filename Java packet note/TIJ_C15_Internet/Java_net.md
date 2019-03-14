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

`ServerSocket`对象创建后JVM并不会自动清除网络套接字，为了保证：
- 正确清除`ServerSocket`对象
- 防止调用`accept()`后无法获得一个正确的`Socket`对象而导致中断退出

需要在将正式使用`ServerSocket`对象代码置于`try{}finally{}`代码块中。在`finally`中时使用：
```
finally{
    serverSocket.close();
}
```
`Socket`对象也是同理，需要在`finally`块中执行关闭`.close()`
