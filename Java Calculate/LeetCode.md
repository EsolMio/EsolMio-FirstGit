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
给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 一位 数字。
Example：
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

Answer:
1. 明确，不要被示例迷惑，两数位数不一定相同
2. 明确，计算过程中的具体数据，如进位，符号，位数相加后的总和等等，都要有具体的对象代表其。


```
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummyHead = new ListNode(0);
    ListNode p = l1, q = l2, curr = dummyHead;
    int carry = 0;// 进位值
    while (p != null || q != null) {
        //Tips3
        int x = (p != null) ? p.val : 0;
        int y = (q != null) ? q.val : 0;
        int sum = carry + x + y;
        
        carry = sum / 10;// Tips1
        
        curr.next = new ListNode(sum % 10);// Tips2
        curr = curr.next;
        if (p != null) p = p.next;
        if (q != null) q = q.next;
    }
    if (carry > 0) {
        curr.next = new ListNode(carry);
    }
    return dummyHead.next;
}

// 作者：LeetCode
// 链接：https://leetcode-cn.com/problems/two-// sum/solution/liang-shu-xiang-jia-by-leetcode/
// 来源：力扣（LeetCode）
// 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```
- Tips1，使用“除”特性：1. 非整数结果仅保留整数部分；2. 将值缩小，如十位变个位
- Tips2，使用“求余”特性，对于不同进制数，求余进制值（如十进制：10），所得数为个位数
- Tips3，三目运算符在判断后对变量赋值上非常有用

### 复杂度分析

- 时间复杂度：$O(max(m, n))$，假设 mm 和 nn 分别表示 l1l1 和 l2l2 的长度，上面的算法最多重复 $max(m,n)$ 次。


