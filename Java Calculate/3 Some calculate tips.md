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

## 