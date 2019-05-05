# 小技巧

## 计算两整数的最大公因数
最大公因数：gcd(m, n)-文字表达方式。
```
public static long gcd(long m, long n){
    while(n!=0){
        long rem = m % n;
        m = n;
        n = rem;
    }
    return m;
}
```
若 $m>n$，则 $m$ mod $n<m/2$ 

## 幂运算
对 $x^n$ 的化简，使用递归算法会更合适：
若 $n$ 是偶数，则 $x^n=x^o*x^o$，其中 $o=n/2$
若 $n$ 是奇数，则 $x^n=x^t*x^t*x$，其中 $t=(n-1)/2$

## 递归往while的转换
再使用while循环时有一特性即会出现“将新的值赋予自己”这种情况，此时的“自己”为循环所围绕的“根”。
故在转换（亦或者使用递归/循环）时，要明确