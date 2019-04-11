# JSP 标准动作 & EL表达式

## JSP 标准动作
JSP有以下几个标准动作（常用）：
1. `<jsp:useBean id="" class="" scope="" [type=""] />`，获得JavaBean。
	- "id"为必须，对应java代码中指向JavaBean的句柄名，
	- "class"为必须，对应java代码中JavaBean的申明类型，以及生成(`new`)新JavaBean的必须值，
	- "scope"非必须，默认值为`pageContext`。
	- "type"非必须，在需要使用上溯造型JavaBean时使用，指定上溯的类型，此时"class"必须存在。
2. `<jsp:setProperty name="" property="" value="" />`，需要和1.连用，在连用时`<jsp:useBean>`表示为：`<jsp:useBean></jsp:useBean>`(封装体/包含体)