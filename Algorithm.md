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

