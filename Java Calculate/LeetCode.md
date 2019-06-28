# Good Calculate

## 两数之和
题目：给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

use 10ms.

code：
```
class Solution {
    int j;
    private  static class Num{
     int num;
     int index;
    }   
     Num[] num;
    public int[] twoSum(int[] nums, int target) {
        num = new Num[nums.length];
        for(int q =0 ;q<nums.length;q++){
            num[q] = new Num();
            num[q].num = nums[q];
            num[q].index = q;
        }
        Arrays.sort(num, new Comparator<Num>(){
            public int compare(Num a, Num b){
                return a.num - b.num;
            }
        });
                    
        for(int i=0;i<nums.length;i++){
            if(search(i+1,target-num[i].num)){
                return new int[]{num[i].index,j};
            }
                
        }
        return new int[2];
    }
    
    boolean search(int l , int target){
        int r = num.length-1;
        int mid;
        while(l <= r){
            mid = (l+r)/2;
            if(num[mid].num < target){
                l = mid+1;
            }else if(num[mid].num > target){
                r = mid -1;
            }else{
                j = num[mid].index;
                return true;
            }
        }
        return false;
    }
}
```

## 两数相加
