# String 常用的方法
1. `substring(int beginIndex, int endIndex)`，返回指定的索引index范围内的字符串，从"beginIndex"开始，至"endIndex-1"，`return String`。
	1. `substring(int beginIndex)`功能相同方法。
2. `concat(String str)`，将指定的str添加至本String对象的后方。
3. `indexOf(int ch)`，返回第一次出现ch的索引
	1. `indexOf(String str, int fromIndex)`功能相同，但从指定的fromIndex开始查找。
	2. `lastIndexOf(int ch)`，返回最后一次出现ch的索引。
		1. `lastIndexOf(int ch, int fromIndex)`功能相同，但范围锁定在fromIndex之前。
4. `trim()`，消除掉本String对象前后的whitespace。
5. `split(String regex)`，通过参数regex将String对象分割，regex参数不保留在新字串中。
6. 
