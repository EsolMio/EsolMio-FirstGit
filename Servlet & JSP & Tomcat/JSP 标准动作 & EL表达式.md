# JSP 标准动作 & EL表达式

## JSP 标准动作
JSP有以下几个标准动作（常用）：
1. `<jsp:useBean id="" class="" scope="" [type=""] />`
2. `<jsp:setProperty name="" property="" value="" />`，需要和1.连用，在连用时`<jsp:useBean>`表示为：`<jsp:useBean></jsp:useBean>`(封装体/包含体)