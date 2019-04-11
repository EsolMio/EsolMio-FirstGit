# JSP 标准动作 & EL表达式
Tips:
1. 在servlet中，大部分的对象都放置于属性Attribute中，故通过`setAttribute()`和`getAttribute()`设置，获得。
2. 一个JavaBean中可能有多个property，故property的名称和JavaBean的名称不统一。

## JSP 标准动作
JSP有以下几个标准动作（常用）：
1. `<jsp:useBean id="" class="" scope="" [type=""] />`，获得JavaBean。**Tips**：JavaBean必须拥有**无参数**的构造器。
	- "id"为必须，对应java代码中指向JavaBean的句柄名，以及指定`getAttribute()`中的名称name。
	- "class"为必须，对应java代码中JavaBean的申明/对象类型，以及生成(`new`)新JavaBean的必须值，
	- "scope"非必须，默认值为`pageContext`。
	- "type"非必须，在需要使用上溯造型JavaBean时使用，指定上溯的类型/引用类型，此时"class"必须存在。
2. `<jsp:setProperty name="" property="" value="" />`，需要和1.连用，在连用时`<jsp:useBean>`表示为：`<jsp:useBean></jsp:useBean>`(封装体/包含体)
	- "name"为必须，指定从`getAttribute()`中的名称name，
	- "property"为必须，指定从Attribute中获得的实例的property值（Tips：一个JavaBean中可能有多个property）
	- "param"非必须，设置此参数可直接从request中获得nane相匹配的值，
3. `<jsp:forward>`
4. `<jsp:include>`

Tips: 可在属性中使用Scriptlet`<%...%>`，但这不推荐。

## EL表达式
