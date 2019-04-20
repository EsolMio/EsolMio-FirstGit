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
	- "param"非必须，设置此参数可直接从request中获得nane相匹配的值，e.g.  HTML: `<input type="text" name="userName">`, JSP: `<jsp:setProperty param="userName">`
		- 若request（只针对request）的请求参数名称与`<jsp:setProperty>`指定JavaBean的property相同，则value直接配对。
		- 在上条条件下，可将"property"设置为"*"，意味着与JavaBean中的property相匹配的请求参数均自动对应上。
		- 若请求参数为纯数字，则`<jsp:setProperty>`会自动将其从String转译为int(`Integer.parseInt(String)`)，但若在"value"中使用scriptlet则不会自动转译(`value="<%=rquest.getParameter(name) %>"`)
3. `<jsp:forward page="" />`，将页面转发给指定的jsp
4. `<jsp:include page="" />`，功能上与`<%@ include file="" %>`相同，将指定页面安插在本页面中。两者的区别：
	1. `<jsp:include page="" />`jsp动作在运行时“插入”被插入页面的响应，即将本jsp处理的响应传递到指定jsp中（使用`RequestDispatcher`），对动作在代码中使用的位置不敏感，
	2. `<%@ include file="" %>`include指令则将指定的jsp在编译器之间将其与本jsp合并，编译后为单一的servlet，对动作在代码中使用的位置敏感。

Tips: 可在属性中使用Scriptlet`<%...%>`，但这不推荐。

## EL表达式
- 作用域为`${...}`
- 可以使用`.`或`[]`操作，
- `.`可处理`Map`和JavaBean，
- `[]`可处理`Map`，JavaBean，`List`，数组类型；其内可使用`""`抑或不用，使用时则针对键名/property名/数组索引处理，不使用时则从隐式对象/各作用域的Attribute中查找。
- EL中的第一个名称从隐式对象/各作用域的Attribute中查找。
- EL中的隐式对象
	- 作用域属性的Map类型句柄：
		- "pageScope"
		- "rquestScope"
		- "sessionScope"
		- "applicationScope"
	- 请求参数的Map:
		- "param"，此泛型为`Map<String, String>`，一对一
		- "paramValues"，此泛型为`Map<String, String[]>`，一对多
	- 请求首部的Map：
		- "header"
		- "headerValues"
	- "cookie"，Map，曲奇
	- "initParam"，Map，上下文Context初始化参数（于web.xml中定义）
	- 唯一的真引用：
		- "pageContext"，真正的对象引用（JavaBean），非Map，与jsp生成的servlet中的`pageContext`相对应。
- EL中的运算符号：
	- 算数："+", "-", "*", "/ div"(Tips: 除以0不会出错，而是返回infinity), "% mod"
	- 逻辑："&& and", "|| or", "! not"
	- 关系："== eq", "!= ne", "< lt", "> gt", "<= le", ">= ge"。
- 倘若EL中出现了null值，不会打印任何东西
	- 在算数表达式中，EL将null看作为0
	- 逻辑表达式，EL将null看作为false
	- 但在求余"%"时使用了null对象，则会抛出异常0



