# Http Session
### Session的使用

- 若使用了`request.getSeesion()`：
  - 若没有请求中没有匹配的cookie/压根就没有cookie，容器会自动创建一个新的`Cookie`对象，同时创建一个新的`HttpSession`对象，两者之间有匹配关系。
  - 若有匹配上的cookie则使用原本的`Cookie`对象，并返回一个匹配上的`HttpSession`对象。
  - 换句话收，当容器知道你需要一个会话时，会为你准备一个`HTTP Session`对象和`Cookie`对象。
- `session.isNew()`：判断会话是刚刚创建还是已存在，返回`boolean`。
- `request.getSession(boolean create)`：来判断是否需要在无匹配cookie对话时创建一个新的HttpSession对象，`true`为创建并返回一新HttpSession对象，`false`为返回null。
- 当容器发现servlet对请求创建一个新的会话对象（同过调用`request.getSession()`），则会创建一个新cookieID，对响应设置"Set-Cookie"的同时，向页面中存在的URL添加会话ID（cookieID的本质），双重保险。（切记前提为调用了`response.encodeURL()`）。
- `encodeURL(String URL);`，在指定URL后编码上sessionID，sessionID即可通过`session.getID()`获得的值，并非cookie。
- `response.encodeRedirectURL(String URL)`：对URL编码的同时将请求重定向到另外一个URL。


### HttpSession对象的方法（常用）

- `getCreationTime()`：返回创建此会话对象的时间，`return long`类型，自GMT1970/1/1开始以毫秒为单位。
- `getLastAccessedTime()`：返回容器最后一次收到此会话用户请求后过去的时间，`return long`，以毫秒为单位。
- `setMaxInactiveTime(int interval)`：设置此会话客户请求的最长间隔时间（即session的生存时间），以秒为单位。
  - 也可以在DD中配置超时/生存时间：`<session-config><sessuion-timeout>分钟</session-timeout></session-config>`
- `getMaxInactiveInterval()`：获得此会话客户请求的最大间隔时间，`return int`，以秒为单位。
- `invalidate()`：结束会话，将原本的会话ID无效化。
- **CAUTION!!!**：在"Head First servlet & jsp"p249中**有误**，其称使用`session.setMaxInactiveInterval(0)`时会话对象会立刻超时，而在servlet doc中的说明与其相反：
  > A zero or negative time indicates that the session should never timeout.
0/负数表示会话永不超时

### 设置会话Session的配置

设置会话配置需要在DD中配置，在主标签`<web-app></web-app>`下使用`<session-config></session-config>`中配置申明
- 配置Session超时值：`<session-timeout>timeout</session-timeout>`

### `Session`与`Cookie`的关系
- 在使用`request.getSession()`以及容器认为需要创建新session时，容器会根据sessionID设置Cookie的name和value（`new Cookie(String name, String value)`）。
- 在使用`request.getSession()`以及容器接收到request中有cookie，则根据cookie查找对应的session后传递给servlet（返回值）。
- 使用`encodeURL(String URL)`时，倘若在其之前没有声明使用`request.getSession();`则此方法将会失效，不会对指定的URL重编码。正常使用`encodeURL()`时将会在指定的URL后加上与创建新Cookie对象时相同的参数值。

### 与会话相关的监听者（接口）
Tips 1：
属性类为，调用`setAttribute()`等方法时传递的类。
其他类为，独立设立（如设立一个Listener package以存放）的类。
Tips 2：
一下监听器均由Container容器触发。
Tips 3：
是否需要在DD中声明有待验证。

- `HttpSessionListener`，由其他类实现。
  - 用于：监听session的创建`sessionCreated(HttpSessionEvent 
  se)`；删除`sessionDestoryed(HttpSessionEvent se)`。
  - **需要在DD中声明此监听器**。
- `HttpSessionActivationListener`，由属性类或其他类实现（亦可
  两者结合）。
  - 用于：监听会话的迁移/序列化：1. 在迁移/序列化时执行自己制定的动作`sessionWillPassivate(HttpSessionEvent event)`（如可用于将非Serializable字段进行转换以便于迁移）；2. 在反序列化/抵达的时候执行自己制定的动作`sessionDidActivate(HttpSessionEvent event)`（如将前述括号中的过程反执行，转回原本的状态）。
  - **不需要在DD中声明**。
- `HttpSessionBindingListener`，由属性类实现。
    - container容器会先检查添加/移除/重置的对象是否实现此监听器以确定是否要触发此监听器。
    - 用于：1. 当此属性被session绑定/被用于`session.setAttribute(String name, Object value)`时会触发`valueBound(HrrpSessionBindingEvent event)`；2. 当session解绑此属性时/`session.removeAttribute(String name)`时会触发`valueUnbound(HttpSessionBindingEvent event)`。
    - **不需要在DD中声明**
- `HttpSessionAttributeListener`。由其他类实现。
    - 用于：1. 当执行`session.setAttribute(String name, Object value)`以添加属性时触发`attributeAdded(HttpSessionEvent event)`；2. 当执行`session.reomveAttribute(String name)`删除对应属性时触发`attributeRemoved(HttpSessionBindingEvent event)`；3. 当执行`session.setAttribute(String name, Object value)`以更改对应属性名的值时触发`attributeReplaced(HttpSessionBindingEvent event)`。
    - **注意**：container容器会先触发`HttpSessionBindingListener`（如果有）然后触发本监听器。
    -  **是否需要在DD中声明有待验证**

 

 

