# Checked Exception & UnChecked Exception
## 检查型异常 Checked Exception
- 指非`RuntimeException`派生出来的Exception，此类Exception均成为检查型异常。
- 若在定义方法逻辑时有产生此类错误的可能性时，必须要捕捉此类错误并针对处理(`try{...}catch(Throwable e){...}`)/在方法中使用`throws`声明掷出Exception，或者调用可能产生此错误的方法时也需要使用try获得错误并处理。对检查型错误的处理是**强制性**的。

## 非检查型错误 UnChecked Exception
- 派生自`RuntimeException`的Exception均为非检查型错误，无强制要求对此错误进行捕捉与处理。
- 如常见的`NullPointException`, `ClassCastException`等等。
- 此类错误发生时将由JVM托管处理此类错误。
- 