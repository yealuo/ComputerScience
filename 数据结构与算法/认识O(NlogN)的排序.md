# 1  剖析递归行为和递归行为时间复杂度的估算
## 1.1  master公式的使用
$$T(N)=a*T(\frac{N}{b})+O(N^d)$$
 $T(N)$：问题规模为N时的时间复杂度
 $a$：问题分解的次数，表示问题被分解成了a个子问题
 $b$：每个子问题的规模相对于原问题的比例
 $O(N^d)$：分解和合并子问题所需的额外工作量，通常是一个多项式项
 Master定理的目标是通过a、b和d 的值来确定递归算法的时间复杂度

Master定理分为三种情况：
**Case 1**：$log_b~a>d$，那么时间复杂度是$O(N^{log_b~a})$
**Case 2**：$log_b~a<d$，那么时间复杂度是$O(N^d)$
**Case 3**：$log_b~a==d$，那么时间复杂度是$O(N^d*{log~N})$
**举例：**
```c
//求arr[L...R]范围上的最大值
int process(int arr[],int L,int R){
    if(L==R){
        return arr[L];
    }
    int mid=L+((R-L)>>1);
    int leftMax=process(arr,L,mid);
    int rightMax=process(arr,mid+1,R);
    return leftMax>rightMax?leftMax:rightMax;
}
```
该递归的复杂度为：
$T(N)=2*T(\frac{N}{2})+O(1)$
a=2    b=2    d=0
$log_b~a=1~>~d=0$
那么时间复杂度为：$O(N)$

