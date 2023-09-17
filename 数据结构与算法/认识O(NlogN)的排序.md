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

# 2  归并排序
```c
void merge(int arr[],int L,int R,int M);
//递归
void process(int arr[],int L,int R){
    if(L==R){
        return;
    }
    int mid=L+((R-L)>>1);
    process(arr,L,mid);
    process(arr,mid+1,R);
    merge(arr,L,R,mid);
}

//归并
void merge(int arr[],int L,int R,int M){
    int* help=(int*)malloc((R-L+1)*sizeof(int));
    int i=0;
    int p1=L,p2=M+1;
    while(p1<=M && p2<=R){
        help[i++]=arr[p1]<=arr[p2]?arr[p1++]:arr[p2++];
    }
    while(p1<=M){
        help[i++]=arr[p1++];
    }
    while(p2<=R){
        help[i++]=arr[p2++];
    }
    for(int j=0;j<(R-L+1);j++){
        arr[L+j]=help[j];
    }
    return;
}
```

## 2.1  归并排序的扩展
### 2.1.1  小和问题和逆序对问题
#### 2.1.1.1  小和问题
在一个数组中，每一个数左边比当前数小的数累加起来，叫做这个数组的小和。求一个数组的小和。
例子:[1,3,4,2,5]
1左边比1小的数，没有;
3左边比3小的数，1;
4左边比4小的数，1、3;
2左边比2小的数，1;
5左边比5小的数，1、3、4、2;
所以小和为1+1+3+1+1+3+4+2=16
```c
#include <stdio.h>
#include <stdlib.h>

void merge(int arr[], int L, int R, int M, int* smallSum);
// 递归
void process(int arr[], int L, int R, int* smallSum) {
    if (L == R) {
        return;
    }
    int mid = L + ((R - L) >> 1);
    process(arr, L, mid, smallSum);
    process(arr, mid + 1, R, smallSum);
    merge(arr, L, R, mid, smallSum);
}

// 归并并计算小和
void merge(int arr[], int L, int R, int M, int* smallSum) {
    int* help = (int*)malloc((R - L + 1) * sizeof(int));
    int i = 0;
    int p1 = L, p2 = M + 1;
    while (p1 <= M && p2 <= R) {
        if (arr[p1] < arr[p2]) {
            help[i] = arr[p1];
            *smallSum += (R - p2 + 1) * arr[p1];
            i++;
            p1++;
        } else {
            help[i++] = arr[p2++];
        }
    }
    while (p1 <= M) {
        help[i++] = arr[p1++];
    }
    while (p2 <= R) {
        help[i++] = arr[p2++];
    }
    for (int j = 0; j < (R - L + 1); j++) {
        arr[L + j] = help[j];
    }
    free(help);
    return;
}

int main() {
    int smallSum = 0;
    int arr[] = {1, 3, 4, 2, 5};
    size_t length = sizeof(arr) / sizeof(arr[0]);
    process(arr, 0, length - 1, &smallSum);
    printf("\n%d", smallSum);
    return 0;
}
```
#### 2.1.1.2  逆序对问题
在一个数组中，左边的数如果比右边的数大，则这两个数构成一个逆序对，请打印所有逆序对的数量。
```c
// 归并并计算逆序对
int merge(int arr[], int L, int R, int M) {
    int* help = (int*)malloc((R - L + 1) * sizeof(int));
    int i = 0, result = 0;
    int p1 = L, p2 = M + 1;
    while (p1 <= M && p2 <= R) {
        if (arr[p1] > arr[p2]) {
            help[i] = arr[p1];
            result += (R - p2 + 1);
            i++;
            p1++;
        } else {
            help[i++] = arr[p2++];
        }
    }
    while (p1 <= M) {
        help[i++] = arr[p1++];
    }
    while (p2 <= R) {
        help[i++] = arr[p2++];
    }
    for (int j = 0; j < (R - L + 1); j++) {
        arr[L + j] = help[j];
    }
    free(help);
    return result;
}

int process(int arr[], int L, int R) {
    if (L == R) {
        return 0;
    }
    int mid = L + ((R - L) >> 1);
    return process(arr, L, mid) + process(arr, mid + 1, R) +
           merge(arr, L, R, mid);
}
```

# 3  快速排序
## 3.1  荷兰国旗问题
1. 给定一个数组arr，和一个数num，请把小于等于num的数放在数组的左边，大于num的数放在数组的右边。要求额外空间复杂度0(1)，时间复杂度0(N)
```c
 void partitionArray(int* arr,size_t length,int num){
    int l=0,r=0;
    int temp;
    for(;r<length;r++){
        if(arr[r]<=num){
            temp=arr[l];
            arr[l]=arr[r];
            arr[r]=temp;
            l++;
        }
    }
    return;
 }
```

2. 给定一个数组arr，和一个数num，请把小于num的数放在数组的左边，等于num的数放在数组的中间，大于num的数放在数组的右边。要求额外空间复杂度0(1)，时间复杂度0(N)
```c
void partitionArray(int* arr, size_t length, int num) {
    int l = 0, r = length - 1, i = 0;
    int temp;
    while (i <= r) {
        if (arr[i] < num) {
            temp = arr[l];
            arr[l] = arr[i];
            arr[i] = temp;
            i++;
            l++;
        } else if (arr[i] == num) {
            i++;
        } else {
            temp = arr[r];
            arr[r] = arr[i];
            arr[i] = temp;
            r--;
        }
    }
    return;
}
```

## 3.2  快速排序
### 3.2.1  快速排序1.0
```c
void quickSort1(int* arr,int L,int R){
    if(L>=R){
        return;
    }
    int l=L,r=L,temp;
    for(;r<R;r++){
        if(arr[r]<=arr[R]){
            temp=arr[l];
            arr[l]=arr[r];
            arr[r]=temp;
            l++;
        }
    }
    temp=arr[l];
    arr[l]=arr[R];
    arr[R]=temp;
    quickSort1(arr,L,l-1);
    quickSort1(arr,l+1,R);
    return;
}
```

### 3.2.2  快速排序2.0
```c
void quickSort2(int* arr,int L,int R){
    if(L>=R){
        return;
    }
    int l=L,r=R-1,i=L,temp;
    while(i<=r){
        if(arr[i]<arr[R]){
            temp=arr[l];
            arr[l]=arr[i];
            arr[i]=temp;
            i++;
            l++;
        }else if(arr[i]==arr[R]){ 
            i++;
        }else{
            temp=arr[r];
            arr[r]=arr[i];
            arr[i]=temp;
            r--;
        }
    }
    temp=arr[i];
    arr[i]=arr[R];
    arr[R]=temp;
    quickSort2(arr,L,l-1);
    quickSort2(arr,i+1,R);
    return;
}
```


