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



+ 剑指offer 45: 把数组排成最小的数

```java
// 自定义排序
class Solution {
    public String minNumber(int[] nums) {
        int n = nums.length;
        String[] str = new String[n];
        for(int i = 0; i < n; i++)
            str[i] = String.valueOf(nums[i]);
        
        quick_sort(str, 0, n - 1);
        StringBuilder sb = new StringBuilder();
        for(String item: str){
            sb.append(item);
        }
        return sb.toString();
    }

    static void quick_sort(String[] str, int l, int r){
        if(l >= r) return;
        int i = l, j = r;
        String tmp = str[i];
        while(i < j){
            while((str[j] + str[l]).compareTo(str[l] + str[j]) >= 0 && i < j) j--;
            while((str[i] + str[l]).compareTo(str[l] + str[i]) <= 0 && i < j) i++;
            tmp = str[i];
            str[i] = str[j];
            str[j] = tmp;
        }
        str[i] = str[l];
        str[l] = tmp;
        quick_sort(str, l, i - 1);
        quick_sort(str, i + 1, r);
    }
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

+ 剑指offer 11. 旋转数组中的最小值

```java
class Solution {
    public int minArray(int[] numbers) {
        int n = numbers.length - 1;
        // 排除数组中分界点出相等的元素
        while(n > 0 && numbers[0] == numbers[n]) n--;
        if(numbers[n] >= numbers[0]) return numbers[0];
        int l = 0, r = n;
        while(l < r){
            int mid = l + r >> 1;
            if(numbers[mid] < numbers[0]) r = mid;
            else l = mid + 1;
        }
        return numbers[r];
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

> 2.3 深度优先搜索和广度优先搜索

+ 深度优先搜索(https://www.acwing.com/problem/content/844/)

```java
import java.io.*;

class Main{
    
    public static final int N = 10;
    static int[] path = new int[N];
    static boolean[] stk = new boolean[N];
    static int n;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        
        n = Integer.parseInt(br.readLine());
        
        dfs(0);
    }
    
    public static void dfs(int u){
        if(u == n){
            for(int i = 0; i < n; i++) System.out.print(path[i] + " ");
            System.out.println();
            return;
        }
        
        for(int i = 1; i <= n; i++){
            if(stk[i] == false){
                path[u] = i;
                stk[i] = true;
                dfs(u + 1);
                stk[i] = false;
            }
        }
    }
}
```

+ 广度优先遍历(https://www.acwing.com/problem/content/846/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 110;
    static Pair[] q = new Pair[N * N];
    static int[][] g = new int[N][N];
    static int[][] d = new int[N][N];
    
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        for(int i = 0; i < n; i++){
            String[] sLine = br.readLine().split(" ");
            for(int j = 0; j < m; j++)
                g[i][j] = Integer.parseInt(sLine[j]);
        }
        
        for(int i = 0; i < n; i++) Arrays.fill(d[i], -1);
        
        System.out.println(bfs());
    }
    
    public static int bfs(){
        int hh = 0, tt = -1;
        q[++tt] = new Pair(0, 0);
        d[0][0] = 0;
        
        int dx[] = {-1, 0, 1, 0};
        int dy[] = {0, 1, 0, -1};
        
        while(hh <= tt){
            Pair t = q[hh++];
            for(int i = 0; i < 4; i++){
                int x = t.l + dx[i];
                int y = t.r + dy[i];
                if(x >= 0 && x < n && y >= 0 && y < m && d[x][y] == -1 && g[x][y] == 0){
                    d[x][y] = d[t.l][t.r] + 1;
                    q[++tt] = new Pair(x, y);
                }
            }
        }
        return d[n - 1][m - 1];
    }
}

class Pair{
    int l;
    int r;
    public Pair(int l, int r){
        this.l = l;
        this.r = r;
    }
}
```



### 3. 数据结构

> 3.1 单链表

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

+ lc 92.翻转链表ii(https://leetcode-cn.com/problems/reverse-linked-list-ii/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if(head == null || left == right) return head;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode a = dummy;
        for(int i = 0; i < left - 1; i++) a = a.next;
        ListNode b = a.next;
        ListNode c = a.next.next;
        for(int i = 0; i < right - left; i++){
            ListNode d = c.next;
            c.next = b;
            b = c;
            c = d;
        }
        a.next.next = c;// 此时节点a与下一个节点之间的指针还没有断开
        a.next = b;
        return dummy.next;
    }
}
```

+ lc 83.删除链表中的重复元素(https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null) return head;
        ListNode cur = head;
        while(cur != null){
            if(cur.next != null && cur.val == cur.next.val)
                cur.next = cur.next.next;
            else
                cur = cur.next;
        }
        return head;
    }
}
```

+ lc 82.删除链表中的重复元素ii(https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null) return head;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode cur = dummy;
        while(cur.next != null && cur.next.next != null){
            
            if(cur.next != null && cur.next.val == cur.next.next.val){
                int x = cur.next.val;
                while(cur.next != null && cur.next.val == x) 
                    cur.next = cur.next.next;
            }
            else cur = cur.next;
        }
        return dummy.next;
    }
}
```

+ lc 21.合并两个有序链表(https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        ListNode head = new ListNode(-1);
        ListNode a = head;
        while(l1 != null && l2 != null){
            if(l1.val <= l2.val) {
                a.next = l1;
                l1 = l1.next;
            }else{
                a.next = l2;
                l2 = l2.next;
            }
            a = a.next;
        }

        while(l1 != null){
            a.next = l1;
            a = a.next;
            l1 = l1.next;
        }

        while(l2 != null){
            a.next = l2;
            a = a.next;
            l2 = l2.next;
        }
        return head.next;
    }
}
```

+ lc 445.两数相加II(https://leetcode-cn.com/problems/add-two-numbers-ii/)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if(l1 == null) return l2;
        if(l2 == null) return l1;
        ListNode dummy = new ListNode(-1);
        Stack<Integer> s1 = new Stack<>();
        Stack<Integer> s2 = new Stack<>();
        while(l1 != null){
            s1.push(l1.val);
            l1 = l1.next;
        }
        while(l2 != null){
            s2.push(l2.val);
            l2 = l2.next;
        }
        int x = 0; // 加法中的进位
        while(s1.size() > 0 || s2.size() > 0 || x != 0){
            int sum = x;
            if(s1.size() > 0) sum += s1.pop();
            if(s2.size() > 0) sum += s2.pop();
            ListNode cur = new ListNode(sum % 10);
            if(dummy.next == null) dummy.next = cur;
            else {
                ListNode t = dummy.next;
                dummy.next = cur;
                cur.next = t;
            }
            x = sum / 10;
        }
        return dummy.next;
    }
}
```

+ lc 141.环形链表(https://leetcode-cn.com/problems/linked-list-cycle/)

```java
/** 快慢指针
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null || head.next == null) return false;
        ListNode slow = head;
        ListNode fast = head.next;
        while(slow != fast){
            if(fast == null || fast.next == null) return false;
            slow = slow.next;
            fast = fast.next.next;
        }
        return true; 
    }
}
```

> 3.2 双链表

+ 双链表(https://www.acwing.com/problem/content/829/)

```java
import java.io.*;

class Main{
    public static final int N = 100010;
    static int idx;
    static int[] l = new int[N];
    static int[] r = new int[N];
    static int[] e = new int[N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        init();
        while((n--) != 0){
            String[] fLine = br.readLine().split(" ");
            if(fLine[0].equals("R")){
                int x = Integer.parseInt(fLine[1]);
                add(l[1], x);
            }else if(fLine[0].equals("L")){
                int x = Integer.parseInt(fLine[1]);
                add(0, x);
            }else if(fLine[0].equals("IL")){
                int k = Integer.parseInt(fLine[1]);
                int x = Integer.parseInt(fLine[2]);
                add(l[k + 1], x);
            }else if(fLine[0].equals("IR")){
                int k = Integer.parseInt(fLine[1]);
                int x = Integer.parseInt(fLine[2]);
                add(k + 1, x);
            }else {
                int k = Integer.parseInt(fLine[1]);
                remove(k + 1);
            }
        }
        for(int i = r[0]; i != 1; i = r[i]) System.out.print(e[i] + " ");
    }
    
    // 初始化第一个节点从2开始
    public static void init(){
        r[0] = 1;
        l[1] = 0;
        idx = 2;
    }
    
    // 在第k个位置后面添加节点x
    public static void add(int k, int x){
        e[idx] = x;
        r[idx] = r[k];
        l[r[k]] = idx;
        l[idx] = k;
        r[k] = idx++;
    }
    
    // 移除第k个位置上节点
    public static void remove(int k){
        l[r[k]] = l[k];
        r[l[k]] = r[k];
    }
}
```

> 3.3  栈与队列

+ 模拟栈(https://www.acwing.com/problem/content/830/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] s = new int[N];
    static int tt;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        while((n--) != 0){
            String[] fLine = br.readLine().split(" ");
            if(fLine[0].equals("push")){
                int x = Integer.parseInt(fLine[1]);
                s[++tt] = x;
            }else if(fLine[0].equals("query")){
                System.out.println(s[tt]);
            }else if(fLine[0].equals("pop")){
                tt--;
            }else{
                if(tt <= 0) System.out.println("YES");
                else System.out.println("NO");
            }
        }
    }
}
```

+ 单调栈(https://www.acwing.com/problem/content/832/)

```java
import java.io.*;

class Main{
    public static final int N = 100010;
    static int[] s = new int[N];
    static int[] a = new int[N];
    static int tt;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        String[] fLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(fLine[i]);
        for(int i = 0; i < n; i++){
            while(tt > 0 && s[tt] >= a[i]) tt--;
            if(tt > 0) System.out.print(s[tt] + " ");
            else System.out.print("-1 ");
            s[++tt] = a[i];
        }
    }
}
```



+ 剑指offer 31:栈的压入、栈的弹出(https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Stack<Integer> stack = new Stack<>();
        int j = 0;
        for(int i = 0; i < pushed.length; i++){
            stack.push(pushed[i]);
            while(!stack.isEmpty() && stack.peek() == popped[j]){
                stack.pop();
                j++;
            }
        }
        return stack.isEmpty();
    }
}
```





+ 模拟队列(https://www.acwing.com/problem/content/831/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] q = new int[N];
    static int hh;
    static int tt = -1;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        while((n--) != 0){
            String[] fLine = br.readLine().split(" ");
            if(fLine[0].equals("push")){
                int x = Integer.parseInt(fLine[1]);
                q[++tt] = x;
            }else if(fLine[0].equals("query")){
                System.out.println(q[hh] + " ");
            }else if(fLine[0].equals("pop")){
                hh++;
            }else {
                System.out.println(hh <= tt? "NO": "YES");
            }
        }
    }
}
```

+ 剑指 offer 09: 用两个栈实现队列

```java
class CQueue {
    Deque<Integer> s1;
    Deque<Integer> s2; 
    public CQueue() {
        s1 = new LinkedList<Integer>();
        s2 = new LinkedList<Integer>();
    }
    
    public void appendTail(int value) {
        s1.add(value);
    }
    
    public int deleteHead() {
        if(s2.isEmpty()){
            while(!s1.isEmpty()){
                s2.add(s1.pop());
            }
        }

        if(s2.isEmpty()){
            return -1;
        } else{
            int deleteItem = s2.pop();
            return deleteItem;
        }
    }
}

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue obj = new CQueue();
 * obj.appendTail(value);
 * int param_2 = obj.deleteHead();
 */
```



+ 剑指offer 41: 数据流中的中位数(https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

```java
class MedianFinder {
    Queue<Integer> A, B;
    /** initialize your data structure here. */
    public MedianFinder() {
        A = new PriorityQueue<>(); // 小根堆, 保存较大的一半
        B = new PriorityQueue<>((x, y) -> (y - x)); // 大根堆, 保存较小的一半
    }
    // 队列A.size >= B.size
    public void addNum(int num) {
        if(A.size() != B.size()){
            A.add(num);
            B.add(A.poll());
        } else{
            B.add(num);
            A.add(B.poll());
        }
    }
    
    public double findMedian() {
        return A.size() != B.size() ? A.peek(): (A.peek() + B.peek()) / 2.0;
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```



+ 滑动窗口(https://www.acwing.com/problem/content/156/)

```java
import java.io.*;

class Main{
    
    public static final int N = 1000010;
    static int[] a = new int[N];
    static int[] q = new int[N];
    static int hh = 0;
    static int tt = -1;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int k = Integer.parseInt(fLine[1]);
        
        String[] sLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(sLine[i]);
        
        for(int i = 0; i < n; i++){
            if(hh <= tt && i - k + 1 > q[hh]) hh++;
            while(hh <= tt && a[q[tt]] >= a[i]) tt--;
            
            q[++tt] = i;
            if(i >= k - 1) System.out.print(a[q[hh]] + " ");
        }
        System.out.println();
        hh = 0;
        tt = -1;
        for(int i = 0; i < n; i++){
            if(hh <= tt && i - k + 1 > q[hh]) hh++;
            while(hh <= tt && a[q[tt]] <= a[i]) tt--;
            
            q[++tt] = i;
            if(i >= k - 1) System.out.print(a[q[hh]] + " ");
        }
    }
}
```

> 3.4 并查集

+ 合并集合(https://www.acwing.com/problem/content/838/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] p = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        for(int i = 1; i <= n; i++) p[i] = i;
        
        while((m--) != 0){
            String[] sLine = br.readLine().split(" ");
            int a = Integer.parseInt(sLine[1]);
            int b = Integer.parseInt(sLine[2]);
            if(sLine[0].equals("M")) p[find(a)] = find(b);
            else {
                if(find(a) == find(b)) System.out.println("Yes");
                else System.out.println("No");
            }
        }
    }
    
    public static int find(int x){
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    }
}
```

> 3.5 堆

堆排序(https://www.acwing.com/problem/content/840/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] h = new int[N];
    static int len;
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        len = n;
        
        String[] sLine = br.readLine().split(" ");
        for(int i = 1; i <= n; i++) h[i] = Integer.parseInt(sLine[i - 1]);
        for (int i = n / 2; i > 0; i--) down(i);
        
        while((m--) > 0){
            System.out.print(h[1] + " ");
            h[1] = h[len];
            len--;
            down(1);
        }
        
    }
    
    public static void down(int u){
        int t = u;
        if(2 * u <= len && h[2 * u] < h[t]) t = 2 * u;
        if(2 * u + 1 <= len && h[2 * u + 1] < h[t]) t = 2 * u + 1;
        if(t != u){
            int cur = h[t];
            h[t] = h[u];
            h[u] = cur;
            down(t);
        }
    }
}
```

> 3.6 散列表

模拟散列表(https://www.acwing.com/problem/content/842/)

版本1：数组模拟

```java
import java.io.*;

class Main{
    
    public static final int N = 20003, INF = 0x3f3f3f;
    static int[] h = new int[N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        while((n--) != 0){
            String[] fLine = br.readLine().split(" ");
            int x = Integer.parseInt(fLine[1]);
            if(fLine[0].equals("I")){
                h[find(x)] = x;
            }else {
                if(h[find(x)] == x) System.out.println("Yes");
                else System.out.println("No");
            }
        }
    }
    
    public static int find(int x){
        int k = (x % N + N) % N;
        while(h[k] != x && h[k] != INF){
            k++;
            if(k == N) k = 0;
        }
        return k;
    }
}
```

版本2：链表模拟

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 100003;
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] ne = new int[N];
    static int idx;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        Arrays.fill(h, -1);
        
        while((n--) > 0){
            String[] fLine = br.readLine().split(" ");
            int x = Integer.parseInt(fLine[1]);
            if(fLine[0].equals("I")){
                insert(x);
            }else {
                if(query(x)) System.out.println("Yes");
                else System.out.println("No");
            }
        }
    }
    
    public static void insert(int x){
        int k = (x % N + N) % N;
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
    
    public static boolean query(int x){
        int k = (x % N  + N) % N;
        for(int i = h[k]; i != -1; i = ne[i]){
            if(e[i] == x) return true;
        }
        return false;
    }
}
```

> 3.7 字符串哈希

```java
import java.io.*;

class Main{
    
    public static final int N = 100010, P = 131;
    static int[] p = new int[N];
    static int[] h = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        String sLine = br.readLine();
        
        char[] str = new char[N];
        
        for(int i = 1; i <= n; i++) str[i] = sLine.charAt(i - 1);
        p[0] = 1;
        for(int i = 1; i <= n; i++){
            p[i] = p[i - 1] * P;
            h[i] = h[i - 1] * P + str[i];
        }
        
        while((m--) != 0){
            String[] tLine = br.readLine().split(" ");
            int l1 = Integer.parseInt(tLine[0]);
            int r1 = Integer.parseInt(tLine[1]);
            int l2 = Integer.parseInt(tLine[2]);
            int r2 = Integer.parseInt(tLine[3]);
            
            if(get(l1, r1) == get(l2, r2)) System.out.println("Yes");
            else System.out.println("No");
        }
    }
    
    public static int get(int l, int r){
        return h[r] - h[l - 1] * p[r - l + 1];
    }
}
```



> 3.8 树与图

+ n-皇后(https://www.acwing.com/problem/content/845/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 20;
    static char[][] g = new char[N][N];
    static boolean[] col = new boolean[N];
    static boolean[] dg = new boolean[N];
    static boolean[] udg = new boolean[N];
    static int n;
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        n = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < n; i++)
            for(int j = 0; j < n; j++)
                g[i][j] = '.';
        
        dfs(0);
    }
    
    public static void dfs(int u){
        if(u == n){
            for(int i = 0; i < n; i++){
                for(int j = 0; j < n; j++)
                    System.out.print(g[i][j]);
                System.out.println();
            }
            System.out.println();
            return;
        }
        
        for(int i = 0; i < n; i++){
            if(col[i] == false && dg[i + u] == false && udg[i - u + n] == false){
                g[u][i] = 'Q';
                col[i] = dg[i + u] = udg[i - u + n] = true;
                dfs(u + 1);
                col[i] = dg[i + u] = udg[i - u + n] = false;
                g[u][i] = '.';
            }
        }
    }
}
```



+ 剑指offer 28: 对称的二叉树(https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
/*
对称二叉树定义： 对于树中 任意两个对称节点 LL 和 RR ，一定有：
L.val = R.valL.val=R.val ：即此两对称节点值相等。
L.left.val = R.right.valL.left.val=R.right.val ：即 LL 的 左子节点 和 RR 的 右子节点 对称；
L.right.val = R.left.valL.right.val=R.left.val ：即 LL 的 右子节点 和 RR 的 左子节点 对称。
*/
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return root == null? true: recur(root.left, root.right);
    }

    public boolean recur(TreeNode l, TreeNode r){
        if(l == null && r == null) return true;
        if(l == null || r == null || l.val != r.val) return false;
        return recur(l.left, r.right) && recur(r.left, l.right);
    }
}
```



+ 剑指offer 36: 二叉搜索树与双链表(https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val,Node _left,Node _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
    Node pre, head;
    public Node treeToDoublyList(Node root) {
        if(root == null) return null;
        dfs(root);
        head.left = pre;
        pre.right = head;
        return head;
    }

    void dfs(Node cur){
        if(cur == null) return;
        dfs(cur.left);
        if(pre != null) pre.right = cur;
        else head = cur;
        cur.left = pre;
        pre = cur;
        dfs(cur.right);
    }
}
```



+ 图中点的层次(https://www.acwing.com/problem/content/849/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 100010;
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] ne = new int[N];
    static int idx;
    
    static int[] q = new int[N];
    // 表示从1到n的距离
    static int[] d = new int[N];
    
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        Arrays.fill(h, -1);
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int k = Integer.parseInt(sLine[0]);
            int x = Integer.parseInt(sLine[1]);
            insert(k, x);
        }
        
        int dist = bfs();
        System.out.println(dist);
    }
    
    public static void insert(int k, int x){
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
    
    public static int bfs(){
        int tt = -1, hh = 0;
        Arrays.fill(d, -1);
        d[1] = 0;
        q[++tt] = 1;
        while(hh <= tt){
            int t = q[hh++];
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(d[j] == -1){
                    d[j] = d[t] + 1;
                    q[++tt] = j;
                }
            }
        }
        return d[n];
    }
}
```



+ 有向图的拓扑序列(https://www.acwing.com/problem/content/850/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 100010;
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] ne = new int[N];
    static int idx;
    
    static int n;
    static int m;
    
    static int[] q = new int[N];
    static int[] d = new int[N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        Arrays.fill(h, -1);
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int k = Integer.parseInt(sLine[0]);
            int x = Integer.parseInt(sLine[1]);
            insert(k, x);
            d[x]++;
        }
        
        if(top())
            for(int i = 0; i < n; i++) System.out.print(q[i] + " ");
        else System.out.println("-1");
    }
    
    public static boolean top(){
        int tt = -1, hh = 0;
        for(int i = 1; i <= n; i++){
            if(d[i] == 0)
                q[++tt] = i;
        }
        
        while(hh <= tt){
            int t = q[hh++];
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                d[j]--;
                if(d[j] == 0) q[++tt] = j;
            }
        }
        return tt == n - 1;
    }
    
    public static void insert(int k, int x){
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
}
```



单源最短路径

+ Dijkstra求最短路 I(https://www.acwing.com/problem/content/851/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 510, INF = 0x3f3f3f;
    static int[][] g = new int[N][N];
    
    static boolean[] stk = new boolean[N];
    static int[] dist = new int[N];
    
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        for(int i = 0; i < N; i++) Arrays.fill(g[i], INF);
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int x = Integer.parseInt(sLine[0]);
            int y = Integer.parseInt(sLine[1]);
            int z = Integer.parseInt(sLine[2]);
            
            g[x][y] = Math.min(g[x][y], z);
        }
        
        System.out.println(dijkstra());
    }
    
    public static int dijkstra(){
        Arrays.fill(dist, INF);
        dist[1] = 0;
        for(int i = 0; i < n; i++){
            int t = -1;
            
            for(int j = 1; j <= n; j++){
                if(stk[j] == false && (t == -1 || dist[t] > dist[j]))
                    t = j;
            }
            
            stk[t] = true;
            
            for(int j = 1; j <= n; j ++){
                dist[j] = Math.min(dist[j], dist[t] + g[t][j]);
            }
        }
        
        if(dist[n] >= INF) return -1;
        return dist[n];
    }
}
```



+ Dijkstra求最短路 II(https://www.acwing.com/problem/content/852/)

```java
import java.io.*;
import java.util.Arrays;
import java.lang.Comparable;
import java.util.PriorityQueue;

class Main{
    
    public static final int N = 200010, INF = 0x3f3f3f3;
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] ne = new int[N];
    static int[] w = new int[N];
    
    static int[] dist = new int[N];
    static boolean[] stk = new boolean[N];
    
    static int idx;
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        Arrays.fill(h, -1);
        
        while((m--) > 0){
            String[] sLine = br.readLine().split(" ");
            int x = Integer.parseInt(sLine[0]);
            int y = Integer.parseInt(sLine[1]);
            int z = Integer.parseInt(sLine[2]);
            
            insert(x, y, z);
        }
        
        System.out.println(dijkstra());
    }
    
    public static void insert(int k, int x, int z){
        e[idx] = x;
        w[idx] = z;
        ne[idx] = h[k];
        h[k] = idx++;
    }
    
    public static int dijkstra(){
        Arrays.fill(dist, INF);
        PriorityQueue<Pair> q = new PriorityQueue<>();
        dist[1] = 0;
        q.add(new Pair(0, 1));
        while(!q.isEmpty()){
            Pair t = q.poll();
            int cur = t.r;
            int d = t.l;
            
            if(stk[cur]) continue;
            
            stk[cur] = true;
            
            for(int i = h[cur]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > d + w[i]){
                    dist[j] = d + w[i];
                    q.add(new Pair(dist[j], j));
                }
            }
        }
        
        if(dist[n] >= INF) return -1;
        return dist[n];
    }
}

class Pair implements Comparable<Pair>{
    int l;
    int r;
    
    public Pair(int l, int r){
        this.l = l;
        this.r = r;
    }
    
    public int compareTo(Pair p){
        return this.l - p.l;
    }
}
```



+ 有边数限制的最短路径(https://www.acwing.com/problem/content/855/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 510, M = 100010, INF = 0x3f3f3f3f;
    static int[] dist = new int[N];
    static int[] backup = new int[N];
    
    static Edge[] edges = new Edge[M];
    static int n;
    static int m;
    static int k;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        k = Integer.parseInt(fLine[2]);
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");	
            int x = Integer.parseInt(sLine[0]);
            int y = Integer.parseInt(sLine[1]);
            int z = Integer.parseInt(sLine[2]);
            
            edges[i] = new Edge(x, y, z);
        }
        
        int t = bellman_ford();
        if(t == -1) System.out.println("impossible");
        else System.out.println(t);
        
    }
    
    public static int bellman_ford(){
        Arrays.fill(dist, INF);
        dist[1] = 0;
        
        for(int i = 0; i < k; i++){
            backup = Arrays.copyOf(dist, dist.length);
            for(int j = 0; j < m; j++){
                int a = edges[j].l, b = edges[j].r, w = edges[j].w;
                dist[b] = Math.min(dist[b], backup[a] + w);
            }
        }
        if(dist[n] > INF / 2) return -1;
        return dist[n];
    }
}

class Edge{
    int l;
    int r;
    int w;
    public Edge(int l, int r, int w){
        this.l = l;
        this.r = r;
        this.w = w;
    }
}
```



+ spfa求最短路(https://www.acwing.com/problem/content/853/)

```java
import java.io.*;
import java.util.Arrays;
import java.util.ArrayDeque;

class Main{
    public static final int N = 100010, INF = 0x3f3f3f3f;
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] ne = new int[N];
    static int[] w = new int[N];
    static int idx;
    
    static int[] dist = new int[N];
    static boolean[] stk = new boolean[N];
    
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        Arrays.fill(h, -1);
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int k = Integer.parseInt(sLine[0]);
            int x = Integer.parseInt(sLine[1]);
            int z = Integer.parseInt(sLine[2]);
            
            insert(k, x, z);
        }
        
        int t = spfa();
        if(t == -1) System.out.println("impossible");
        else System.out.println(t);
        
    }
    
    public static int spfa(){
        Arrays.fill(dist, INF);
        ArrayDeque<Integer> q = new ArrayDeque<>();
        q.add(1);
        dist[1] = 0;
        stk[1] = true;
        
        while(!q.isEmpty()){
            int t = q.poll();
            stk[t] = false;
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > dist[t] + w[i]){
                    dist[j] = dist[t] + w[i];
                    if(stk[j] == false){
                        q.add(j);
                        stk[j] = true;
                    }
                }
            }
        }
        
        if(dist[n] >= INF) return -1;
        return dist[n];
    }
    
    public static void insert(int k, int x, int z){
        e[idx] = x;
        w[idx] = z;
        ne[idx] = h[k];
        h[k] = idx++;
    }
}
```



+ spfa判断负环(https://www.acwing.com/problem/content/854/)

```java
import java.io.*;
import java.util.Arrays;
import java.util.ArrayDeque;

class Main{
    public static final int N = 100010, INF = 0x3f3f3f3f;
    static int[] h = new int[N];
    static int[] e = new int[N];
    static int[] w = new int[N];
    static int[] ne = new int[N];
    static int idx;
    
    static int[] dist = new int[N];
    static boolean[] stk = new boolean[N];
    static int[] cnt = new int[N];
    
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        Arrays.fill(h, -1);
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int x = Integer.parseInt(sLine[0]);
            int y = Integer.parseInt(sLine[1]);
            int z = Integer.parseInt(sLine[2]);
            
            insert(x, y, z);
        }
        
        if(spfa()) System.out.println("Yes");
        else System.out.println("No");
    }
    
    public static boolean spfa(){
        Arrays.fill(dist, INF);
        ArrayDeque<Integer> q = new ArrayDeque<>();
        
        for(int i = 1; i <= n; i++){
            q.add(i);
            stk[i] = true;
        }
        
        while(!q.isEmpty()){
            int t = q.poll();
            stk[t] = false;
            for(int i = h[t]; i != -1; i = ne[i]){
                int j = e[i];
                if(dist[j] > dist[t] + w[i]){
                    dist[j] = dist[t] + w[i];
                    cnt[j] = cnt[t] + 1;
                    if(cnt[j] >= n) return true;
                    
                    if(stk[j] == false){
                        q.add(j);
                        stk[j] = true;
                    }
                }
            }
        }
        return false;
    }
    
    public static void insert(int k, int x, int z){
        e[idx] = x;
        w[idx] = z;
        ne[idx] = h[k];
        h[k] = idx++;
    }
}
```



+ Floyd求最短路(https://www.acwing.com/problem/content/856/)

```java
import java.io.*;

class Main{
    
    public static final int N = 210, INF = (int)1e7;
    
    static int[][] dist = new int[N][N];
    
    static int n;
    static int m;
    static int k;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        k = Integer.parseInt(fLine[2]);
        
        for(int i = 0; i < N; i++)
            for(int j = 0; j < N; j++)
                if(i == j) dist[i][j] = 0;
        		else dist[i][j] = INF;
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int a = Integer.parseInt(sLine[0]);
            int b = Integer.parseInt(sLine[1]);
            int w = Integer.parseInt(sLine[2]);
            
            dist[a][b] = Math.min(dist[a][b], w);
        }
        
        floyd();
        
        while((k--) > 0){
            String[] tLine = br.readLine().split(" ");
            int l = Integer.parseInt(tLine[0]);
            int r = Integer.parseInt(tLine[1]);
            
            if(dist[l][r] > INF / 2) System.out.println("impossible");
            else System.out.println(dist[l][r]);
        }
    }
    
    public static void floyd(){
        for(int k = 0; k <= n; k++)
            for(int i = 0; i <= n; i++)
                for(int j = 0; j <= n; j++)
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
    }
}
```



+ Prim算法求最小生成树(https://www.acwing.com/problem/content/860/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 510, INF = (int)1e7;
    static int[][] g = new int[N][N];
    static int[] dist = new int[N];
    
    static boolean[] stk = new boolean[N];
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        for(int i = 0; i < N; i++) Arrays.fill(g[i], INF);
        
        while((m--) > 0){
            String[] sLine = br.readLine().split(" ");
            int a = Integer.parseInt(sLine[0]);
            int b = Integer.parseInt(sLine[1]);
            int w = Integer.parseInt(sLine[2]);
            
            g[a][b] = g[b][a] = Math.min(g[a][b], w);
        }
        
        int t = prim();
        if(t == -1) System.out.println("impossible");
        else System.out.println(t);
    }
    
    public static int prim(){
        Arrays.fill(dist, INF);
        
        int res = 0;
        for(int i = 0; i < n; i++){
            int t = -1;
            for(int j = 1; j <= n; j++)
                if(stk[j] == false && (t == -1 || dist[t] > dist[j]))
                    t = j;

            stk[t] = true;

            if(i > 0 && dist[t] >= INF) return -1;
            if(i > 0) res += dist[t];
            for(int j = 1; j <= n; j++) dist[j] = Math.min(dist[j], g[t][j]);
        }
        
        return res;
    }
}
```



+ Kruskal算法求最小生成树(https://www.acwing.com/problem/content/861/)

```java
import java.io.*;
import java.util.Arrays;
import java.util.Comparator;

class Main{
    public static final int N = 100010;
    static int[] p = new int[N];
    static Edge[] edges;
    
    static int n;
    static int m;
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        edges = new Edge[m];
        
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int a = Integer.parseInt(sLine[0]);
            int b = Integer.parseInt(sLine[1]);
            int c = Integer.parseInt(sLine[2]);
            
            edges[i] = new Edge(a, b, c);
        }
        
        Comparator<Edge> com = new Comparator<Edge>(){
            public int compare(Edge e1, Edge e2){
                return e1.w - e2.w;
            }
        };
        
        for(int i = 0; i <= n; i++) p[i] = i;
        
        Arrays.sort(edges, com);
        
        int cnt = 0, res = 0;
        for(int i = 0; i < m; i++){
             
            int a = edges[i].l, b = edges[i].r, c = edges[i].w;
            a = find(a);
            b = find(b);
            
            if(a != b){
                p[a] = b;
                res += c;
                cnt++;
            }
        }
        
        if(cnt < n - 1) System.out.println("impossible");
        else System.out.println(res);
    }
    
    public static int find(int x){
        if(p[x] != x) p[x] = find(p[x]);
        return p[x];
    } 
}

class Edge{
    int l;
    int r;
    int w;
    public Edge(int l, int r, int w){
        this.l = l;
        this.r = r;
        this.w = w;
    }
}
```



+ 染色法判定二分图(https://www.acwing.com/problem/content/862/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 100010, M = 200010;
    static int[] h = new int[N];
    static int[] e = new int[M];
    static int[] ne = new int[M];
    static int idx;
    
    static int n;
    static int m;
    
    static int[] color = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        Arrays.fill(h, -1);
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int a = Integer.parseInt(sLine[0]);
            int b = Integer.parseInt(sLine[1]);
            
            insert(a, b);
            insert(b, a);
        }
        
        boolean flag = true;
        for(int i = 1; i <= n; i++){
            if(color[i] == 0){
                if(dfs(i, 1) == false){
                    flag = false;
                    break;
                }
            }
        }
        
        if(flag == false) System.out.println("No");
        else System.out.println("Yes");
        
    }
    
    public static boolean dfs(int u, int c){
        color[u] = c;
        for(int i = h[u]; i != -1; i =ne[i]){
            int j = e[i];
            if(color[j] == 0){
                if(dfs(j, 3 - c) == false) return false;
            }
            else if(color[j] == c) return false;
        }
        return true;
    }
    
    public static void insert(int k, int x){
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
}
```



+ 二分图的最大匹配(https://www.acwing.com/problem/content/863/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 100010, M = 200010;
    static int[] h = new int[N];
    static int[] e = new int[M];
    static int[] ne = new int[M];
    static int idx;
    
    static int n1;
    static int n2;
    static int m;
    
    static int[] match = new int[N];
    static boolean[] stk = new boolean[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n1 = Integer.parseInt(fLine[0]);
        n2 = Integer.parseInt(fLine[1]);
        m = Integer.parseInt(fLine[2]);
        Arrays.fill(h, -1);
        for(int i = 0; i < m; i++){
            String[] sLine = br.readLine().split(" ");
            int k = Integer.parseInt(sLine[0]);
            int x = Integer.parseInt(sLine[1]);
            insert(k, x);
        }
        
        int res = 0;
        for(int i = 1; i <= n1; i++){
            Arrays.fill(stk, false);
            if(find(i)) res++;
        }
        
        System.out.println(res);
        
    }
    
    public static boolean find(int x){
        for(int i = h[x]; i != -1; i = ne[i]){
            int j = e[i];
            if(stk[j] == false){
                stk[j] = true;
                if(match[j] == 0 || find(match[j])){
                    match[j] = x;
                    return true;
                }
            }
        }
        return false;
    }
    
    public static void insert(int k, int x){
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
}
```



### 4. 数学知识

+ 试除法判定质数(https://www.acwing.com/problem/content/868/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        while((n--) != 0){
            int x = Integer.parseInt(br.readLine());
            boolean flag = false;
            for(int i = 2; i <= x / i; i++){
                if(x % i == 0){
                    flag = true;
                    break;
                }
            }
            if(flag == true || x < 2) System.out.println("No");
            else System.out.println("Yes");
        }
        
        
    }
}
```



+ 分解质因数(https://www.acwing.com/problem/content/869/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        while((n--) > 0){
            int x = Integer.parseInt(br.readLine());
            
            get_divide(x);
        }
    }
    
    public static void get_divide(int x){
        for(int i = 2; i <= x / i; i++){
            int count = 0;
            if(x % i == 0){
                
                while(x % i == 0){
                    count++;
                    x /= i;
                }
                System.out.println(i + " " + count);
            }
            
        }
        if(x > 1) System.out.println(x + " " + 1);
        System.out.println();
    }
}
```



+ 筛质数(https://www.acwing.com/problem/content/870/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010;
    static int[] primes = new int[N];
    static boolean[] stk = new boolean[N];
    static int cnt;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int x = Integer.parseInt(br.readLine());
        for(int i = 2; i <= x; i++){
            if(stk[i] == false) primes[cnt++] = i;
            for(int j = i + i; j <= x; j += i) stk[j] = true;
        }
        System.out.println(cnt);
    }
}
```



+ 试除法求约数(https://www.acwing.com/problem/content/871/)

```java
import java.io.*;
import java.util.Arrays;
import java.util.Collections;
import java.util.ArrayList;
import java.util.List;

class Main{
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        while((n--) != 0){
            int x = Integer.parseInt(br.readLine());
            List<Integer> res = get_divide(x);
            
            for(Integer item: res) System.out.print(item + " ");
            System.out.println();
        }
    }
    
    public static List<Integer> get_divide(int x){
        List<Integer> res = new ArrayList<>();
        for(int i = 1; i <= x / i; i++){
            if(x % i == 0){
                res.add(i);
                if(i != x / i) res.add(x / i);
            }
            
        }
        Collections.sort(res);
        return res;
    }
}
```



+ 约数个数(https://www.acwing.com/problem/content/872/)

```java
import java.io.*;
import java.util.*;

class Main{
    
    public static final int N = (int)1e9 + 7;    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        Map<Integer, Integer> map = new HashMap<>();
        while((n--) > 0){
            int x = Integer.parseInt(br.readLine());
            
            for(int i = 2; i <= x / i; i++){
                while(x % i == 0){
                    x /= i;
                    if(map.containsKey(i)){
                        int num = map.get(i);
                        map.put(i, num + 1);
                    }else map.put(i, 1);
                    
                }
            }
            
            if(x > 1){
                if(map.containsKey(x)){
                    int num = map.get(x);
                    map.put(x, num + 1);
                }else map.put(x, 1);
            }
        }
        
        long finalRes = 1L;
        for(Map.Entry item: map.entrySet()) finalRes = finalRes * ((int)item.getValue() + 1) % N;
        System.out.println(finalRes);
    }
}
```



+ 最大公约数(https://www.acwing.com/problem/content/874/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        while((n--) > 0){
            String[] fLine = br.readLine().split(" ");
            int l = Integer.parseInt(fLine[0]);
            int r = Integer.parseInt(fLine[1]);
            int res = gcd(l, r);
            System.out.println(res);
        }
        
    }
    
    public static int gcd(int a, int b){
        return b > 0? gcd(b, a % b): a;
    }
}
```



+ 剑指offer 65: 不用加减乘除做加法(https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

```java
/*
设两数字的二进制形式 a, b，其求和 s = a + b，a(i) 代表 a 的二进制第 i 位，则分为以下四种情况：
a[i]    b[i]    无进位和n[i]    进位c[i+1]
 0        0        0             0
 0        1        1             0
 1        0        1             0
 1        1        0             1
观察发现，无进位和 与 异或运算 规律相同，进位 和 与运算 规律相同（并需左移一位）
(和 s ) =（非进位和 n ）+（进位 c ）。即可将 s = a + b 转化为
*/
class Solution {
    public int add(int a, int b) {
        while(b != 0){
            int c = (a & b) << 1;
            a ^= b;
            b = c;
        }
        return a;
    }
}
```





+ 快速幂(https://www.acwing.com/problem/content/877/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        while((n--) != 0){
            String[] fLine = br.readLine().split(" ");
            int a = Integer.parseInt(fLine[0]);
            int b = Integer.parseInt(fLine[1]);
            int p = Integer.parseInt(fLine[2]);
            
            long res = qmi(a, b, p);
            System.out.println(res);
        }
    }
    
    public static long qmi(long a, int b, int p){
        long res = 1L;
        while(b != 0){
            if((b & 1) == 1) res = res * a % p;
            b >>= 1;
            a = a * a % p;
        }
        return res;
    }
}
```



+ 快速幂求逆元(https://www.acwing.com/problem/content/878/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        while((n--) > 0){
            String[] fLine = br.readLine().split(" ");
            int a = Integer.parseInt(fLine[0]);
            int p = Integer.parseInt(fLine[1]);
            if(a % p != 0) System.out.println(qmi(a, p - 2, p));
            else System.out.println("impossible");
        }
    }
    
    public static long qmi(long a, int b, int p){
        long res = 1L;
        while(b != 0){
            if((b & 1) == 1) res = res * a % p;
            b >>= 1;
            a = a * a % p;
        }
        return res;
    }
}
```



+ 求组合数 I(https://www.acwing.com/problem/content/887/)

```java
import java.io.*;

class Main{
    
    public static final int N = 2010, mod = (int)1e9 + 7;
    static int[][] c = new int[N][N];
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        for(int i = 0; i < N; i++)
            for(int j = 0; j <= i; j++)
                if(j == 0) c[i][j] = 1;
                else c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod;
        
        while((n--) > 0){
            String[] fLine = br.readLine().split(" ");
            int l = Integer.parseInt(fLine[0]);
            int r = Integer.parseInt(fLine[1]);
            System.out.println(c[l][r]);
        }
    }
}
```



+ 求组合数 II(https://www.acwing.com/problem/content/888/)

```java
import java.io.*;

class Main{
    
    public static final int N = 100010, mod = (int)1e9 + 7;
    
    static long[] factor = new long[N];
    static long[] infactor = new long[N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        factor[0] = infactor[0] = 1L;
        for(int i = 1; i < N; i++){
            factor[i] = factor[i - 1] * i % mod;
            infactor[i] = infactor[i - 1] * qmi(i, mod - 2, mod) % mod;
        }
        
        while((n--) != 0){
            String[] fLine = br.readLine().split(" ");
            int l = Integer.parseInt(fLine[0]);
            int r = Integer.parseInt(fLine[1]);
            
            System.out.println(factor[l] * infactor[l - r] % mod * infactor[r] % mod);
        }
    }
    
    public static long qmi(long a, int b, int p){
        long res = 1L;
        while(b != 0){
            if((b & 1) == 1) res = res * a % p;
            b >>= 1;
            a = a * a % p;
        }
        return res;
    }
}
```



+ 满足条件的01序列(https://www.acwing.com/problem/content/891/)

```java
import java.io.*;

class Main{
    
    public static final int mod = (int)1e9 + 7;
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        int a = 2 * n, b = n;
        long res = 1L;
        for(int i = a; i > a - b; i--) res = res * i % mod;
        for(int i = 1; i <= b; i++) res = res * qmi(i, mod - 2, mod) % mod;
        
        res = res * qmi(n + 1, mod - 2, mod) % mod;
        System.out.println(res);
    }
    
    public static long qmi(long a, int b, int p){
        long res = 1L;
        while(b != 0){
            if((b & 1) == 1) res = res * a % p;
            b >>= 1;
            a = a * a % p;
        }
        return res;
    }
}
```



+ Nim游戏(https://www.acwing.com/problem/content/893/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        int res = 0;
        String[] fLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++)
            res ^= Integer.parseInt(fLine[i]);
        if(res != 0) System.out.println("Yes");
        else System.out.println("No");
    }
}
```



+ 台阶-Nim游戏(https://www.acwing.com/problem/content/894/)

```java
import java.io.*;

class Main{
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        String[] fLine = br.readLine().split(" ");
        int res = 0;
        for(int i = 1; i <= n; i++)
            if(i % 2 != 0) res ^= Integer.parseInt(fLine[i - 1]);
        
        if(res != 0) System.out.println("Yes");
        else System.out.println("No");
    }
}
```



+ 剑指offer 44: 数字序列中某一位的数字(https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

```java
class Solution {
    public int findNthDigit(int n) {
        int digit = 1; // 数位
        long start = 1; // 起始位置 如10~99, start = 10
        long count = 9; // 某个digit内包含的数字位数 如10~99 占了180位 (99-10+1) * 2
        while(n > count){
            n -= count;
            digit += 1;
            start *= 10;
            count = 9 * start * digit;
        }
        long num = start + (n - 1) / digit;
        return Long.toString(num).charAt((n - 1) % digit) - '0';
    }
}
```



+ 剑指offer 14-I：剪绳子(https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

```java
/*
切分规则：
最优： 3 。把绳子尽可能切为多个长度为 3 的片段，留下的最后一段绳子的长度可能为 0,1,2 三种情况。
次优： 2 。若最后一段绳子长度为 2 ；则保留，不再拆为 1+1。
最差： 1 。若最后一段绳子长度为 1 ；则应把一份 3 + 1 替换为 2 + 2，因为 2×2>3×1。

算法流程：
当 n≤3 时，按照规则应不切分，但由于题目要求必须剪成m>1段，因此必须剪出一段长度为 1 的绳子，即返回 n - 1 。
当 n>3 时，求 n 除以 3 的 整数部分 a 和 余数部分 b （即 n = 3a + b ），并分为以下三种情况：
	当 b = 0 时，直接返回 3^a；
	当 b = 1 时，要将一个 1 + 3 转换为 2+2，因此返回 3^{a-1} * 4;
	当 b = 2 时，返回 3^a * 2。
*/
class Solution {
    public int cuttingRope(int n) {
        if(n <= 3) return n - 1;
        int a = n / 3, b = n % 3;
        if(b == 0) return (int)Math.pow(3, a);
        if(b == 1) return (int)Math.pow(3, a - 1) * 4;
        return (int)Math.pow(3, a) * 2;
    }
}
```



### 5. 动态规划

+ 01背包问题(https://www.acwing.com/problem/content/2/)

```java
import java.io.*;

class Main{
    public static final int N = 1010;
    static int[][] f = new int[N][N];
    static int[] v = new int[N];
    static int[] w = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        for(int i = 1; i <= n; i++){
            String[] sLine = br.readLine().split(" ");
            v[i] = Integer.parseInt(sLine[0]);
            w[i] = Integer.parseInt(sLine[1]);
        }
        
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= m; j++){
                f[i][j] = f[i - 1][j];
                if(j >= v[i]) f[i][j] = Math.max(f[i][j], f[i - 1][j - v[i]] + w[i]);
            }
        }
        
        System.out.println(f[n][m]);
    }
}
```



+ 完全背包问题(https://www.acwing.com/problem/content/3/)

```java
import java.io.*;

class Main{
    public static final int N = 1010;
    static int[][] f = new int[N][N];
    static int[] v = new int[N];
    static int[] w = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        for(int i = 1; i <= n; i++){
            String[] sLine = br.readLine().split(" ");
            v[i] = Integer.parseInt(sLine[0]);
            w[i] = Integer.parseInt(sLine[1]);
        }
        
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= m; j++){
                for(int k = 0; k * v[i] <= j; k++){
                	f[i][j] = Math.max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i]);
                }
            }
        }
        System.out.println(f[n][m]);
    }
}
```



+ 多重背包问题 I(https://www.acwing.com/problem/content/4/)

```java
import java.io.*;

class Main{
    public static final int N = 1010;
    static int[][] f = new int[N][N];
    static int[] v = new int[N];
    static int[] w = new int[N];
    static int[] s = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        for(int i = 1; i <= n; i++){
            String[] sLine = br.readLine().split(" ");
            v[i] = Integer.parseInt(sLine[0]);
            w[i] = Integer.parseInt(sLine[1]);
            s[i] = Integer.parseInt(sLine[2]);
        }
        
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= m; j++){
                for(int k = 0; k <= s[i] && k * v[i] <= j; k++){
                	f[i][j] = Math.max(f[i][j], f[i - 1][j - k * v[i]] + k * w[i]);
                }
            }
        }
        System.out.println(f[n][m]);
    }
}
```



+ 多重背包问题 II(https://www.acwing.com/problem/content/5/)

```java
import java.io.*;

class Main{
    
    public static final int N = 10010, M = 2010;
    static int[] v = new int[N];
    static int[] w = new int[N];
    static int[] f = new int[M];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        int cnt = 0;
        for(int i = 1; i <= n; i++){
            String[] sLine = br.readLine().split(" ");
            int a = Integer.parseInt(sLine[0]);
            int b = Integer.parseInt(sLine[1]);
            int s = Integer.parseInt(sLine[2]);
            
            int k = 1;
            while(k <= s){
                cnt++;
                v[cnt] = a * k;
                w[cnt] = b * k;
                s -= k;
                k *= 2;
            }
            
            if(s > 0){
                cnt++;
                v[cnt] = a * s;
                w[cnt] = b * s;
            }
        }
        
        n = cnt;
        for(int i = 1; i <= n; i++)
            for(int j = m; j >= v[i]; j--)
                f[j] = Math.max(f[j], f[j - v[i]] + w[i]);
        
        System.out.println(f[m]);
    }
}
```



+ 分组背包问题(https://www.acwing.com/problem/content/9/)

```java
import java.io.*;

class Main{
    
    public static final int N = 110;
    static int[][] v = new int[N][N];
    static int[][] w = new int[N][N];
    static int[][] f = new int[N][N];
    
    static int[] s = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        for(int i = 1; i <= n; i++){
            s[i] = Integer.parseInt(br.readLine());
        	for(int j = 0; j < s[i]; j++){
                String[] sLine = br.readLine().split(" ");
                v[i][j] = Integer.parseInt(sLine[0]);
                w[i][j] = Integer.parseInt(sLine[1]);
            }
        }
        
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= m; j++)
                for(int k = 0; k <= s[i]; k++){
                    if(j >= v[i][k]) f[i][j] = Math.max(f[i][j], f[i - 1][j - v[i][k]] + w[i][k]);
                }
        }
        
        System.out.println(f[n][m]);
    }
}
```



+ 数字三角形(https://www.acwing.com/problem/content/900/)

```java
import java.io.*;

class Main{
    
    public static final int N = 1010;
    static int[][] w = new int[N][N];
    // f[i][j] 表示从底部走到f[1][1]最大值
    static int[][] f = new int[N][N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        for(int i = 1; i <= n; i++){
            String[] fLine = br.readLine().split(" ");
            for(int j = 1; j <= i; j++)
                w[i][j] = Integer.parseInt(fLine[j - 1]);
        }
        
        // 从底部往上走
        for(int i = 1; i <= n; i++) f[n][i] = w[n][i];
        
        for(int i = n - 1; i > 0; i--)
            for(int j = 0; j <= i; j++)
                f[i][j] = Math.max(f[i + 1][j] + w[i][j], f[i + 1][j + 1] + w[i][j]);
        
        System.out.println(f[1][1]);
    }
}
```



+ 最长上升子序列(https://www.acwing.com/problem/content/897/)

```java
import java.io.*;

class Main{
    
    public static final int N = 1010;
    static int[] w = new int[N];
    static int[] f = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        String[] fLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) w[i] = Integer.parseInt(fLine[i]);
        
        int res = -1;
        for(int i = 0; i < n; i++){
            f[i] = 1;
            for(int j = 0; j < i; j++)
                if(w[i] > w[j]) f[i] = Math.max(f[i], f[j] + 1);
            res = Math.max(res, f[i]);
        }
        System.out.println(res);
    }
}
```



+ 最长上升子序列 II(https://www.acwing.com/problem/content/898/)

```java
import java.io.*;

class Main{
    public static final int N = 100010;
    static int[] a = new int[N];
    static int[] q = new int[N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        String[] fLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(fLine[i]);
        
        int len = 0;
        for(int i = 0; i < n; i++){
            int l = 0;
            int r = len;
            while(l < r){
                int mid = l + r + 1 >> 1;
                if(q[mid] < a[i]) l  = mid;
                else r = mid - 1;
            }
            len = Math.max(len, r + 1);
            q[r + 1] = a[i];
        }
        System.out.println(len);
    }
}
```



+ 最长公共子序列(https://www.acwing.com/problem/content/899/)

```java
import java.io.*;

class Main{
    
    public static final int N = 1010;
    static char[] a = new char[N];
    static char[] b = new char[N];
    static int[][] f = new int[N][N];
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        int n = Integer.parseInt(fLine[0]);
        int m = Integer.parseInt(fLine[1]);
        
        char[] t1 = br.readLine().toCharArray();
        char[] t2 = br.readLine().toCharArray();
        
        for(int i = 1; i <= n; i++) a[i] = t1[i - 1];
        for(int i = 1; i <= m; i++) b[i] = t2[i - 1];
        
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= m; j++){
                f[i][j] = Math.max(f[i - 1][j], f[i][j - 1]);
                if(a[i] == b[j]) f[i][j] = Math.max(f[i][j], f[i - 1][j - 1] + 1);
            }
        }
        System.out.println(f[n][m]);
    }
}
```



+ 最短编辑距离(https://www.acwing.com/problem/content/904/)

```java
import java.io.*;

class Main{
    
    public static final int N = 1010;
    static char[] a = new char[N];
    static char[] b = new char[N];
    // f[i][j]表示从把0-i变成0-j一共需要的操作数
    static int[][] f = new int[N][N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine().trim());
        char[] t1 = br.readLine().toCharArray();
        int m = Integer.parseInt(br.readLine().trim());
        char[] t2 = br.readLine().toCharArray();
        
        for(int i = 1; i <= n; i++) a[i] = t1[i - 1];
        for(int i = 1; i <= m; i++) b[i] = t2[i - 1];
        
        for(int i = 1; i <= n; i++) f[i][0] = i;
        for(int j = 1; j <= m; j++) f[0][j] = j;
        
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= m; j++){
                f[i][j] = Math.min(f[i - 1][j] + 1, f[i][j - 1] + 1);
                if(a[i] == b[j]) f[i][j] = Math.min(f[i][j], f[i - 1][j - 1]);
                else f[i][j] = Math.min(f[i][j], f[i - 1][j - 1] + 1);
            }
        }
        System.out.println(f[n][m]);
    }
}
```



+ 石子合并(https://www.acwing.com/problem/content/284/)

```java
import java.io.*;

class Main{
    
    public static final int N = 310, INF = (int)1e9;
    static int[] s = new int[N];
    static int[][] f = new int[N][N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        String[] fLine = br.readLine().split(" ");
        for(int i = 1; i <= n; i++) s[i] = Integer.parseInt(fLine[i - 1]);
        
        // 预处理前缀和
        for(int i = 1; i <= n; i++) s[i] += s[i - 1];
        
        for(int len = 2; len <= n; len++){
            for(int i = 1; i + len - 1 <= n; i++){
                int l = i;
                int r = i + len - 1;
                f[l][r] = INF;
                for(int k = l; k < r; k++)
                    f[l][r] = Math.min(f[l][r], f[l][k] + f[k + 1][r] + s[r] - s[l - 1]);
            }
        }
        
        System.out.println(f[1][n]);
    }
}
```



+ 整数划分(https://www.acwing.com/problem/content/902/)

```java
import java.io.*;

class Main{
    
    public static int N = 1010, mod = (int)1e9 + 7;
    // 从1-i个物品中选，体积恰好为j的选法数量
    static int[][] f = new int[N][N];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        for(int i = 0; i <= n; i++) f[i][0] = 1;
        
        for(int i = 1; i <= n; i++){
            for(int j = 0; j <= n; j++){
                f[i][j] = f[i - 1][j] % mod;
                if(j >= i) f[i][j] = (f[i - 1][j] + f[i][j - i]) % mod;
            }
        }
        System.out.println(f[n][n]);
    }
}
```



+ 没有上司的舞会(https://www.acwing.com/problem/content/287/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 6010;
    static int[] e = new int[N];
    static int[] h = new int[N];
    static int[] ne = new int[N];
    static int idx;
    
    static boolean[] has_father = new boolean[N];
    static int[] happy = new int[N];
    static int[][] f = new int[N][2];
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        
        for(int i = 1; i <= n; i++) happy[i] = Integer.parseInt(br.readLine());
        Arrays.fill(h, -1);
        for(int i = 0; i < n - 1; i++){
            String[] fLine = br.readLine().split(" ");
            int a = Integer.parseInt(fLine[0]);
            int b = Integer.parseInt(fLine[1]);
            has_father[a] = true;
            add(b, a);
        }
        
        int root = 1;
        while(has_father[root]) root++;
        
        dfs(root);
        
        int res = Math.max(f[root][0], f[root][1]);
        System.out.println(res);
        
    }
    
    public static void dfs(int u){
        f[u][1] = happy[u];
        
        for(int i = h[u]; i != -1; i = ne[i]){
            int j = e[i];
            dfs(j);
            f[u][1] += f[j][0];
            f[u][0] += Math.max(f[j][0], f[j][1]);
        }
    }
    
    public static void add(int k, int x){
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
}
```



+ 滑雪(https://www.acwing.com/problem/content/903/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    
    public static final int N = 310;
    static int[][] h = new int[N][N];
    static int[][] f = new int[N][N];
    
    static int[] dx = {-1, 0, 1, 0};
    static int[] dy = {0, 1, 0, -1};
    
    static int n;
    static int m;
    
    public static void main(String[] args) throws IOException{
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String[] fLine = br.readLine().split(" ");
        n = Integer.parseInt(fLine[0]);
        m = Integer.parseInt(fLine[1]);
        
        for(int i = 1; i <= n; i++)
        {
            String[] sLine = br.readLine().split(" ");
            for(int j = 1; j <= m; j++)
                h[i][j] = Integer.parseInt(sLine[j - 1]);
        }
        
        for(int i = 0; i < N; i++) Arrays.fill(f[i], -1);
        
        int res = 0;
        for(int i = 1; i <= n; i++)
            for(int j = 1; j <= m; j++)
                res = Math.max(res, dp(i, j));
        System.out.println(res);
        
    }
    
    public static int dp(int x, int y){
        if(f[x][y] != -1) return f[x][y];
        
        f[x][y] = 1;
        for(int i = 0; i < 4; i++){
            int a = x + dx[i];
            int b = y + dy[i];
            if(a >=1 && a <= n && b >= 1 && b <= m && h[a][b] < h[x][y])
                f[x][y] = Math.max(f[x][y], dp(a, b) + 1);
        }
        return f[x][y];
    }
}
```



+ 剑指offer 62: 圆圈中最后剩下的数字(https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

```java
class Solution {
    /* 约瑟夫环
    dp[i] = (dp[i - 1] + m) % i
    dp[i - 1] = (dp[i - 2] + m) % i
    ...
    dp[2] = (dp[1] + m) % 1
    dp[1] = 0
    */
    public int lastRemaining(int n, int m) {
        int x = 0;
        for(int i = 1; i <= n; i++){
            x = (x + m) % i;
        }
        return x;
    }
}
```



### 6. 贪心

+ 区间选点(https://www.acwing.com/problem/content/907/)

```java
import java.io.*;
import java.util.Arrays;
import java.lang.Comparable;
class Main{
    static Range[] ranges;
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        ranges = new Range[n];
        
        for(int i = 0; i < n; i++){
            String[] fLine = br.readLine().split(" ");
            int a = Integer.parseInt(fLine[0]);
            int b = Integer.parseInt(fLine[1]);
            ranges[i] = new Range(a, b);
        }
        
        Arrays.sort(ranges);
        
        int res = 0;
        int ed = (int)-1e9;
        for(int i = 0; i < n; i++){
            if(ranges[i].l > ed){
                res++;
                ed = ranges[i].r;
            }
        }
        System.out.println(res);
        
    }
}

class Range implements Comparable<Range>{
    int l;
    int r;
    
    public Range(int l, int r){
        this.l = l;
        this.r = r;
    }
    
    public int compareTo(Range o){
        return this.r - o.r;
    }
}
```



+ 最大不相交区间数量(https://www.acwing.com/problem/content/910/)

```java
import java.io.*;
import java.util.Arrays;
import java.util.Comparator;

class Main{
    static Range[] ranges;
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        ranges = new Range[n];
        
        for(int i = 0; i < n; i++){
            String[] fLine = br.readLine().split(" ");
            int a = Integer.parseInt(fLine[0]);
            int b = Integer.parseInt(fLine[1]);
            ranges[i] = new Range(a, b);
        }
        
        Arrays.sort(ranges, new Comparator<Range>(){
           public int compare(Range o1, Range o2){
               return o1.r - o2.r;
           } 
        });
        
        int res = 0;
        int ed = (int)-1e9;
        for(int i = 0; i < n; i++){
            if(ranges[i].l > ed){
                res++;
                ed = ranges[i].r;
            }
        }
        System.out.println(res);
    }
}

class Range{
    int l;
    int r;
    public Range(int l, int r){
        this.l = l;
        this.r = r;
    }
}
```



+ 区间分组(https://www.acwing.com/problem/content/908/)

```java
import java.io.*;
import java.util.Arrays;
import java.lang.Comparable;
import java.util.PriorityQueue;

class Main{
    static Range[] ranges;
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        ranges = new Range[n];
        for(int i = 0; i < n; i++){
            String[] fLine = br.readLine().split(" ");
            int a = Integer.parseInt(fLine[0]);
            int b = Integer.parseInt(fLine[1]);
            ranges[i] = new Range(a, b);
        }
        
        Arrays.sort(ranges);
        
        PriorityQueue<Integer> h = new PriorityQueue<>();
        for(int i = 0; i < n; i++){
            Range t = ranges[i];
            if(h.isEmpty() || h.peek() >= t.l) h.add(t.r);
            else {
                h.poll();
                h.add(t.r);
            }
        }
        
        System.out.println(h.size());
    }
}

class Range implements Comparable<Range>{
    int l;
    int r;
    public Range(int l, int r){
        this.l = l;
        this.r = r;
    }
    public int compareTo(Range o){
        return this.l - o.l;
    }
}
```



+ 合并果子(https://www.acwing.com/problem/content/150/)

```java
import java.io.*;
import java.util.PriorityQueue;

class Main{
    
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        // trim()去除首尾的空格
        int n = Integer.parseInt(br.readLine().trim());
        
        PriorityQueue<Integer> heap = new PriorityQueue<>();
        String[] fLine = br.readLine().split(" ");
        for(int i = 0; i < n; i++) heap.add(Integer.parseInt(fLine[i]));
        int res = 0;
        while(heap.size() > 1){
            int a = heap.poll();
            int b = heap.poll();
            res += a + b;
            heap.add(a + b);
        }
        System.out.println(res);
    }
}
```



+ 货仓选址(https://www.acwing.com/problem/content/106/)

```java
import java.io.*;
import java.util.Arrays;

class Main{
    public static final int N = 100010;
    static int[] a = new int[N];
    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        String[] fLine = br.readLine().split(" ");
        
        for(int i = 0; i < n; i++) a[i] = Integer.parseInt(fLine[i]);
        Arrays.sort(a, 0, n);
        int res = 0;
        for(int i = 0; i < n; i++) res += Math.abs(a[i] - a[n / 2]);
        System.out.println(res);
    }
}
```



+ lc 11: 盛水最多的容器(https://leetcode-cn.com/problems/container-with-most-water)

```java
class Solution {
    public int maxArea(int[] height) {
        int n = height.length;
        int l = 0, r = n - 1;
        int res = 0;
        while(l < r){
            // 获取当前两个指针之间的面积(min是获取容器的短板)
            int area = Math.min(height[l], height[r]) * (r - l);
            res = Math.max(res, area);
            if(height[l] <= height[r]) l++;
            else r--;
        }
        return res;
    }
}
```



+ 剑指offer 10-II 青蛙跳台阶

```java
class Solution {

    public static final int mod = (int)1e9 + 7;
    public int numWays(int n) {
        if(n == 0) return 1;
        if(n == 1 || n == 2) return n;
        int one = 2;
        int two = 1;
        int sum = 0;
        for(int i = 3; i <= n; i++){
            sum  = (one + two) % mod;
            two = one;
            one = sum;
        }
        return sum;
    }
}
```



+ 剑指 offer 42: 连续子数组的最大和

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int sum = nums[0];
        int max = nums[0];
        for(int i = 1; i < nums.length; i++){
            if(sum < 0) sum = nums[i];
            else sum += nums[i];

            if(sum > max) max = sum;
        }
        return max;
    }
}
```

