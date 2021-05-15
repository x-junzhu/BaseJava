# 算法分类总结(20210515)

### 1. 排序算法

> 1.1 快速排序

```java
public static void quickSort(int[] a, int l, int r){
    if(l >= r) return;
    int i = l - 1, j = r + 1;
    int x = a[i + j >> 1];
    
    while(i < j){
        // 从前往后搜索大于中间值
        do i++; while(a[i] < x);
        // 从后往前搜索小于中间值
        do j--; while(a[j] > x);
        if(i < j){
            int t = q[j];
            q[j] = q[i];
            q[i] = t;
        }
    }
    quickSort(a, l, j);
    quickSort(a, j + 1, r);
}
```

延伸：

第k个数(https://www.acwing.com/problem/content/788/)

剑指offer 40: 最小的k个数(https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

> 1.2 归并排序

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] q = new int[N];
    static int[] tmp = new int[N];
    static int n;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        n = Integer.parseInt(br.readLine());
        
        String[] fLine = br.readLine().split(" ");
        
        for(int i = 0; i < n; i++) q[i] = Integer.parseInt(fLine[i]);
        
        mergeSort(q, 0, n - 1);
        
        for(int i = 0; i < n; i++) System.out.print(q[i] + " ");
    }
    
    public static void mergeSort(int[] a, int l, int r){
        if(l >= r) return;
        int mid = l + r >> 1;
        // 两端都排好序, 如(4,5,6,1,2,3)
        mergeSort(a, l, mid);
        mergeSort(a, mid + 1, r);
        
        // 重整两段有序的数组
        int k = 0, i = l, j = mid + 1;
        while(i <= mid && j <= r){
            if(a[i] <= a[j]) tmp[k++] = a[i++];
            else tmp[k++] = a[j++];
        }
        
        while(i <= mid) tmp[k++] = a[i++];
        while(j <= r) tmp[k++] = a[j++];
        
        for(i = l, k = 0; i <= r; i++, k++) a[i] = tmp[k];
    }
}
```

延伸：

逆序对个数(https://www.acwing.com/problem/content/790/)

剑指offer 51：数组中逆序对的个数(https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

```java
/*
tmp为临时变量
mergeSort返回的是[l, r]区间内逆序对的个数
*/
public static long mergeSort(int[] a, int l, int r){
    if(l >= r) return 0;
    int mid = l + r >> 1;
    // 左边区间[l, mid]中间包含逆序对的个数 + 右边区间[mid + 1, r]中间包含逆序对的个数
    long res = mergeSort(a, l, mid) + mergeSort(a, mid + 1, r);
    // 注：[l, mid]与[mid + 1, r]之间也可以形成逆序对
    int i = l, j = mid + 1, k = 0;
    while(i <= mid && j <= r){
        // 当a[i] <= a[j]两段区间之间不会形成逆序对
        if(a[i] <= a[j]) tmp[k++] = a[i++];
        else {
            // 当a[i] > a[j]时i~mid之间与[mid + 1, r]都会构成逆序对
            res += mid - i + 1;
            tmp[k++] = a[j++];
        }
    }
    
    while(i <= mid) tmp[k++] = a[i++];
    while(j <= r) tmp[k++] = a[j++];
    
    for(k = 0, i = l; i <= r; i++, k++) a[i] = tmp[k];
    return res;
}
```



### 2. 搜索算法

