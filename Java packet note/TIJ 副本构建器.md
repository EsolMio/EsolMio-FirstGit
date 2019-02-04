# 副本构建器 Duplicate constructorx
## Example Code in TIJ_12_3_1
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
