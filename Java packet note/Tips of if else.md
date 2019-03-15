# Tips of `if else`
在没有`{}`括住的情况下，编译器会自动匹配最近的两个`if else`进行组合。
example:
```
if(...)
    if(....)
else ...
```
这种情况下，最下面的`else`和