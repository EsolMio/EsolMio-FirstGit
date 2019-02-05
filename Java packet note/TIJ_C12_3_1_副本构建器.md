# 副本构建器 Duplicate constructorx
## Example Code in TIJ_C12_3_1
***
**!!!ATTENTION!!!**
**这个方法并不是十分使用于Java，故在Java中不要使用**
因为在有继承时，使用上溯造型的方法传递句柄至copy constructor时，会有类型信息丢失的情况(.getClass().getName(),返回父类类型信息)，详见"Think in java"
***
```
class FruitQualities {
    private int weight;
    private int color;
    private int firmness;
    private int ripeness;
    private int smell;
    //etc...
    //default constructor
    FruitQualities(){
        //design by self...
    }
    //Copy constructor
    //In fact, just send a same handle into copy constructor 
    //to send the orignal object message to the dupliacte object
    FruitQualities(FruitQualities f){
        weight=f.weight;
        color=f.color;
        firmness=f.firmnesslanguage;
        ripeness=f.ripeness;
        smell=f.smell;    
    }
}
```
