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

+ 第k个数(https://www.acwing.com/problem/content/788/)

+ 剑指offer 40: 最小的k个数(https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

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

+ 逆序对个数(https://www.acwing.com/problem/content/790/)

+ 剑指offer 51：数组中逆序对的个数(https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

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

> 2.1 二分查找

二分查找：满足两段性质

+ 模板1

```java
// 数组q必须为有序数组
public int binarySearch(int[] q, int target){
    int l = 0, r = q.length - 1;
    while(l < r){
        int mid = l + r >> 1;
        if(q[mid] >= target) r = mid;
        else l = mid + 1;
    }
    // 此时l=r返回l或者r都可以
    return l;
}
```

+ 模板2

```java
public int binarySearch(int[] q, int target){
    int l = 0, r = q.length - 1;
    while(l < r){
        int mid = l + r + 1 >> 1;
        if(q[mid] <= target) l = mid;
        else r = mid - 1;
    }
    return r;
}
```

延伸：

+ 剑指offer 04：二维数组中的查找(https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix.length == 0 || matrix[0].length == 0) return false;
        int i = 0, j = matrix[0].length - 1;
        // 二维数组中右上角的位置, 从左往右递增, 从上往下递增(两段性)
        while(i < matrix.length && j >= 0){
            int x = matrix[i][j];
            if(x == target) return true;
            if(x > target) j--;
            else i++;
        }
        return false;
    }
}
```

+ 数的三次方根(https://www.acwing.com/problem/content/792/) 

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        double n = Double.parseDouble(br.readLine());
        double t = Math.abs(n);
        double l = 0L, r = t;
        while(r - l > 1e-8){
            double mid = (l + r) / 2.0;
            if(mid * mid * mid >= t) r = mid;
            else l = mid;
        }
        if(n >= 0) System.out.println(String.format("%.6f", l));
        else System.out.println("-" + String.format("%.6f", l));
    }
}
```

> 2.2 双指针算法

+ 最长连续不重复子序列(https://www.acwing.com/problem/content/801/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] a = new int[N];
    static int[] s = new int[N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        String[] fLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(fLine[i]);
        
        int res = 0;
        for(int i = 0, j = 0; i < n; i++){
            s[a[i]]++;
            while(s[a[i]] > 1){
                s[a[j]]--;
                j++;
            }
            res = Math.max(res, i - j + 1);
        }
        System.out.println(res);
    }
}
```

+ 剑指offer 22： 链表中倒数第k个节点(https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        if(head == null) return head;
        ListNode a = head;
        ListNode b = head;
        int t = k;
        while((t--) > 0) a = a.next;
        while(a != null){
            a = a.next;
            b = b.next;
        }
        return b;
    }
}
```

+ 数组元素的目标和(https://www.acwing.com/problem/content/802/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] a = new int[N];
    static int[] b = new int[N];
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        int t = Integer.parseInt(fLine[2]);
        
        String[] sLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(sLine[i]);
        
        String[] tLine = br.readLine().split(" ");
        for(int i = 0; i < m; i++) b[i] = Integer.parseInt(tLine[i]);
        
        int i = 0, j = m - 1;
        while(i < n && j >= 0){
            if(a[i] + b[j] < t) i++;
            else if(a[i] + b[j] > t) j--;
            else break;
        }
        System.out.println(i + " " + j);
    }
}
```

+ 判断子序列(https://www.acwing.com/problem/content/2818/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] a = new int[N];
    static int[] b = new int[N];
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        String[] sLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(sLine[i]);
        
        String[] tLine = br.readLine().split(" ");
        for(int i = 0; i < m; i++) b[i] = Integer.parseInt(tLine[i]);
        
        int i = 0, j = 0;
        while(i < n && j < m){
            if(a[i] == b[j]) i++;
            j++;
        }
        if(i == n) System.out.println("Yes");
        else System.out.println("No");
    }
}
```

剑指offer 52：两个链表的第一个公共节点(https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode a = headA;
        ListNode b = headB;
        // 计算headA和headB链表的长度
        int x = 0, y = 0;
        while(a != null) {
            x ++;
            a = a.next;
        }
        while(b != null) {
            y ++;
            b = b.next;
        }
        // 判断哪个链表长
        boolean flag = (x - y) > 0? true: false;
        int diff = Math.abs(x - y);
        a = headA;
        b = headB;
        // 去掉较长链表多余的部分
        if(flag){
            while((diff--) > 0) a = a.next;
        } else {
            while((diff--) > 0) b = b.next;
        }

        while(a != b){
            a = a.next;
            b = b.next;
        }
        return a;   
    }
}
```



### 3. 数据结构

+ 单链表(https://www.acwing.com/problem/content/828/)

```java
import java.io.*;

class Main{
    public static final int N = 100010;
    static int[] e = new int[N];
    static int[] ne = new int[N];
    static int idx;
    static int head;
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        idx = 0;
        head = -1;
        while((n--) > 0){
            String[] fLine = br.readLine().split(" ");
            if(fLine[0].equals("H")){
                int x = Integer.parseInt(fLine[1]);
                add_to_head(x);
            }else if(fLine[0].equals("D")){
                int k = Integer.parseInt(fLine[1]);
                if(k == 0) head = ne[head];
                else remove(k - 1);
            }else if(fLine[0].equals("I")){
                int k = Integer.parseInt(fLine[1]);
                int x = Integer.parseInt(fLine[2]);
                add(k - 1, x);
            }  
        }
        
        for(int i = head; i != -1; i = ne[i]) System.out.print(e[i] + " ");
    }
    
    public static void add(int k, int x){
        e[idx] = x;
        ne[idx] = ne[k];
        ne[k] = idx++;// 这里idx++ 是先赋值后自增
    }
    
    public static void add_to_head(int x){
        e[idx] = x;
        ne[idx] = head;
        head = idx++;
    }
    
    public static void remove(int k){
        ne[k] = ne[ne[k]];
    }
}
```

+ 剑指 offer 06：从尾到头打印链表(https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public int[] reversePrint(ListNode head) {
        Stack<Integer> stack = new Stack<>();
        ListNode t = head;
        while(t != null){
            stack.add(t.val);
            t = t.next;
        }

        int num = stack.size();
        int[] res = new int[num];
        for (int i = 0; i < num; i++){
            res[i] = stack.pop();
        }
        return res;

    }
}
```

+ 剑指 offer 24：反转链表(https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null) return head;
        ListNode a = head;
        ListNode b = head.next;
        while(b != null){
            ListNode c = b.next;
            b.next = a;
            a = b;
            b = c;
        }
        head.next = null;
        head = a;
        return head;
    }
}
```

+ 剑指 offer 18：删除链表的节点(https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if(head == null) return head;
        ListNode t = head;
        while(t.next != null){
            if(t.val == val) head = head.next;
            if(t.next.val == val) {
                t.next = t.next.next;
                break;
            }
            t = t.next;
        }
        return head;

    }
}
```

