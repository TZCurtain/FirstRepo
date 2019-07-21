# LeetCode
## 3 Longest Substrings without repeating characters
开一个数组或者哈希表hm[i]，记录i字符上次出现的位置
另外使用一个变量tail记录当前子串的最前面一个字符的位置
则遍历循环，即可求得最长的子串长度

``` Java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        //HashMap<Character,Integer> hm = new HashMap<Character,Integer>();
        int[] hm = new int[256];
        
        int len = s.length();
        int res = 0,now = 0,tail = 0;
        for (int i=0;i<len;++i){
            char nowc = s.charAt(i);
            if (hm[nowc] == 0){
               // hm.put(nowc,i);
                hm[nowc] = i+1;
                ++now;
            }
            else {
                int bef = hm[nowc];
                hm[nowc] = i+1;
                res = Math.max(now,res);
                if (bef >= tail){
                    now = i+1 - bef;
                }
                else
                    now = i+1 - tail + 1; 
               // System.out.println(i + " " + bef + " " + now);
                tail = Math.max(tail,bef + 1);
            }
        }
        res = Math.max(now,res);
        
        return res;
    }
}
```
## 4 Median of Two Sorted Arrays
关键是将
left_part                    |        right_part
A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]
使得
左右两边part的长度相等且左边的最大值小于等于右边的最小值
这样中位数等于左边的最大值加右边的最小值除

如果达成这样呢
二分搜索 i从0-m使得b[j-1] <= a[i] && a[i-1] <= b[j] (j = (m+n+1)/2 - i)
此处需要保证n >= m
imin = 0 imax = m;
i = (imin + imax) / 2; j = (m + n + 1) / 2 - i;
if (b[j-1] <= a[i] && a[i-1] <= b[j]) stop search;
if (b[j-1] > a[i])  说明a[i]太小了增加i 则imin = i+1 重来
if (a[i-1] > b[j]) 说明a[i]太大了，则imax = i-1; 重来

得到i以后就可以得到中位数
然后是注意边界情况的考虑
i = 0说明左边最大的是b[j-1]
j = 0说明左边最大的是a[i-1]
长度为奇数的情况直接返回maxleft
i = m 说明右边最小的是b[j]
j = n 说明左边最小的a[i]

``` java
class Solution {
    public double findMedianSortedArrays(int[] A, int[] B) {
        int m = A.length,n = B.length;
        if (m > n)
            return findMedianSortedArrays(B,A);
        int imin = 0, imax = m, half_len = (m + n + 1) / 2;
        while (imin <= imax){
            int i = (imin + imax) / 2;
            int j = half_len - i;
            if (i < m && B[j-1] > A[i])
                imin = i + 1;
            else if (i > 0 && A[i-1] > B[j])
                imax = i - 1;
            else {        
                int max_of_left,min_of_right;
                if (i == 0)
                    max_of_left = B[j-1];
                else if (j == 0) 
                    max_of_left = A[i-1];
                else
                    max_of_left = Math.max(A[i-1], B[j-1]);

                if ((m + n) % 2 == 1)
                    return max_of_left;

                if (i == m)
                    min_of_right = B[j];
                else if (j == n) 
                    min_of_right = A[i];
                else
                    min_of_right = Math.min(A[i], B[j]);

                return (max_of_left + min_of_right) / 2.0;
            }
        }
        // shouldn't reach here
        return -1;
    }
}
```
## 7 Reverse Integer
``` java
public int reverse(int x) {
        long res = x,t = 0;
        while (res != 0){
            long tail = res % 10;
            t = t * 10 + tail;
            res /= 10;
        }
        if (t > Integer.MAX_VALUE || t < Integer.MIN_VALUE) return 0;
        return (int)t;
    }
```
## 8 String to Integer
细心处理即可，主要是针对各种边界情况的处理
## 29 Divide Two Integers
不使用乘除mod符号来实现整数除法
很自然想到只能用位运算了
15 = 3 << 4 + 3;
所以转换为一步步分解的过程。

```c++
int divide(int dividend, int divisor) {
    if (dividend == INT_MIN && divisor == -1){
        return INT_MAX;
    }
    int minus = ((dividend < 0) ^ (divisor < 0))? 1 : -1;
    long long div = labs((long long) dividend);
    long long divis = labs((long long) divisor);
    
    int res = 0;
    while (div >= divis){
    	long long temp = divis,multi = 1;
    	while (div >= (temp << 1)){
    		temp <<= 1;
    		multi <<= 1;
		}
		div -= temp;
		res += multi;
	}    
    return minus == 1? -res:res ;
}

```
## 30 Substring with Concatenation of All Words
## 32 Longest Valid Parentheses
```java
class Solution {
    public int longestValidParentheses(String s) {
        LinkedList<Integer> st = new LinkedList<Integer>();
        int res = 0;
        boolean[] mark = new boolean[s.length() + 2];
        for (int i=0;i<s.length();++i){
            if (s.charAt(i) == ')'){
                if (st.size() == 0) {
                    continue;
                }
                int j = st.removeLast(); 
                mark[i] = true; mark[j] = true;
            }
            else {
                st.addLast(i);
            }
        }

        int now = 0;
        for (int i=0;i<s.length();++i){
            if (!mark[i]){
                res = Math.max(now,res); now = 0;
            }
            else 
                ++now;
        }
        if (now > 0){
            res = Math.max(now,res);   
        }
        
        
        return res;
    }
}
```
还有使用dp的
dp[i]表示以i结尾的最长有效括号对
则若s[i] = '('易得dp[i] = 0
否则dp[i] = dp[i-2] + 2 (dp[i-1] = '(')
   dp[i]  = dp[i-dp[i-1]-2] + dp[i-1] + 2 (dp[i-1] =')' && dp[i-dp[i-1]-1] == '(') 
## 44 Wildcard Matching
通过贪心解决，如果dp的话有些恶心的例子过不去
主要是多个*的操作，当我们有连续多个*的时候，我们事实上只要处理最后的一个*就可以了。
参考讨论里的人写的，非常简单的思路
``` java
class Solution {
    public boolean isMatch(String s, String p) {
        int nows = 0,nowp = 0;
        int befstars = 0,befstarp = -1;
        while (nows < s.length()){
            if (nowp < p.length() && (p.charAt(nowp) == '?' || p.charAt(nowp) == s.charAt(nows))){
                ++nows; ++nowp; continue;
            }
            if (nowp < p.length() && p.charAt(nowp) == '*'){
                befstars = nows; befstarp = nowp; 
                ++nowp; continue;
            }
            else if (befstarp != -1){
                nowp = befstarp + 1;
                ++befstars; // 让*往后匹配一格
                nows = befstars;
            }
            else return false;
        }
        while (nowp < p.length() && p.charAt(nowp) == '*') ++nowp;
        return nowp == p.length();
    }
}
```
## 65 Valid Number
题目根本不告诉你啥算有效数字是最难的地方
使用正则边试边改解决
``` c++
class Solution {
public:
	bool isNumber(string s) {
        regex pat(" *[-+]?[0-9]*(\\.)?[0-9]*(e[+-]?[0-9]+)? *");
        regex pat2(" *[-+]?(\\.)?(e[+-]?[0-9]*)? *");
        return regex_match(s,pat) && (!regex_match(s,pat2));
	}
};
```
## 98 Validate Binary Search Tree
``` java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root,Long.MAX_VALUE,Long.MIN_VALUE);
    }
    
    private boolean isValidBST(TreeNode root,long maxval,long minval){
        if (root == null) return true;
        if (root.val >= maxval || root.val <= minval) return false;
        return isValidBST(root.left,root.val,minval) && isValidBST(root.right,maxval,root.val);
    }
}
```
## 126 Word Ladder II
BFS

## 130 Surrounded Region
``` c++
void dfs(vector<vector<char>>& board,int i,int j){
	if (board[i][j] == 'X' || board[i][j] == 'V') return;
	board[i][j] = 'V';
	if (i > 0) dfs(board,i-1,j);
	if (j > 0) dfs(board,i,j-1);
	if (i < board.size() - 1) dfs(board,i+1,j);
	if (j < board[0].size() - 1) dfs(board,i,j+1);
}
void solve(vector<vector<char>>& board) {
    int height = board.size();
    if (height == 0) return;
    int width = board[0].size();
    
    
    for (int i=0;i<width;++i){
    	dfs(board,0,i); dfs(board,height-1,i);
	}
    
    for (int i=1;i<height-1;++i){
    	dfs(board,i,0); dfs(board,i,width-1);
	}
	
	for (int i=0;i<height;++i){
		for (int j=0;j<width;++j){
			board[i][j] = (board[i][j] == 'V' ? 'O':'X');
		}
	}	
}
```
## 146 LRU Cache
HashMap + 双向链表记录最近最少使用节点，每次put或get的时候通过hashmap获取节点，然后放到前面
``` java
class LRUCache {
    class DLinkedNode {
        int key,val;
        DLinkedNode pre,post;
    }
    
    private DLinkedNode head,tail;
    private void addNode(DLinkedNode node){
        node.pre = head;
        node.post = head.post;
        head.post.pre = node;
        head.post = node;
    }
    private void removeNode(DLinkedNode node){
        DLinkedNode pre = node.pre,post = node.post;
        pre.post = post; post.pre = pre;
    }
    private void moveToHead(DLinkedNode node){
        removeNode(node);
        addNode(node);
    }
    private DLinkedNode popTail(){
        DLinkedNode res = tail.pre;
        removeNode(res);
        return res;
    }
    
    int capacity,size;
    HashMap<Integer,DLinkedNode> hm = new HashMap();
    
    
    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.size = 0;
        head = new DLinkedNode(); tail = new DLinkedNode();
        head.pre = null; head.post = tail;
        tail.pre = head; tail.post = null;
    }
    
    public int get(int key) {
        DLinkedNode res = hm.get(key);
        if (res == null) return -1;
        moveToHead(res);
        return res.val;
    }
    
    public void put(int key, int value) {
        DLinkedNode res = hm.get(key);
        if (res == null) {
            res = new DLinkedNode();
            res.val = value; res.key = key;
            addNode(res);
            hm.put(key,res);
            ++size;
            if (size > capacity){
                DLinkedNode node = popTail();
                hm.remove(node.key);
                --size;
            }
        }
        else {
            res.val = value;
            moveToHead(res);
        }
    }
}

```
## 149 Max Points On a Line
每次循环选定一个点，记录该点和其他点所构成的直线的斜率。斜率相同的点肯定在同一个直线上
使用map存储斜率，注意重合点的处理
ps: 发现直接用double存的话有精度问题，还是用分数存吧
## 151 Reverse Words in a String
我的想法是用栈来写，不过好像不能算O(1)的空间复杂度
还有更简洁的方法是翻转整个字符串，然后翻转各个单词
``` C++
void reverseWords(string &s) { 
    reverse(s.begin(), s.end());
    int storeIndex = 0;
    for (int i = 0; i < s.size(); i++) {
        if (s[i] != ' ') {
            if (storeIndex != 0) storeIndex++;
            int j = i;
            while (j < s.size() && s[j] != ' ')  
                j++;  
            reverse(s.begin() + storeIndex, s.begin() + j);
            storeIndex += (j - i);
            i = j;
        }
    }
    s.erase(s.begin() + storeIndex, s.end());
}
```
## 165 Compare Version Numbers
直接使用java的split比较方便，然后比就是了，注意比完以后后面全为0的情况
## 176  Second Highest Salary
先做过185，这题就不是问题
``` sql
select MAX(Salary) from Employee a where 1 = (select count(distinct(Salary)) from Employee where Salary > a.Salary)
```
## 177 Nth Highest Salary
先做过185，这题就不是问题
``` sql
select distinct(Salary) from Employee a where n-1 = (select count(distinct(Salary)) from Employee where Salary > a.Salary)
```
## 179 Largest Number
字符串排序 s1 + s2 < s2 + s1
``` c++
bool cmp(string s1,string s2){
    return (s1 + s2 < s2 + s1);    
}

class Solution {
public: 
    string largestNumber(vector<int>& nums) {
        vector<string> vec;
        for (int i=0;i<nums.size();++i)
           vec.push_back(to_string(nums[i]));
        sort(vec.begin(),vec.end(),cmp);
        string res = "";
        for (int i=nums.size()-1;i>=0;--i)
            res += vec[i];
        
        if (res[0] == '0') return "0";
        
        return res;
        
    }
};
```
## 184 Department Highest Salary
先做的185，这题从top3变成top1了那肯定更简单
## 185 Department Top Three Salaries
常见的SQL分组排序问题
关键是重复的时候怎么办
``` sql
select Department.Name Department, a.Name Employee, a.Salary Salary 
from Employee a, Department
where Department.Id = a.DepartmentId 
and (select count(distinct(salary)) from Employee  where DepartmentId = a.DepartmentId and Salary > a.Salary ) < 3
order by Department.Name ASC,salary DESC;
```
## 189 Rotate Array
对于这类数组右移左移的，都可以转换为reverse三次解决
``` java
class Solution {
    public void reverse(int[] nums,int l,int r){ // reverse nums[l,r]
        while (l < r){
            int temp = nums[l];
            nums[l] = nums[r];
            nums[r] = temp;
            ++l; r--;
        }
    }
    
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        reverse(nums,0,nums.length-1);
        reverse(nums,0,k-1);
        reverse(nums,k,nums.length-1);
    }
}
```
## 193 Valid Phone Numbers
没啥难的主要是bash命令不熟
``` bash
grep -P '^(\d{3}-|\(\d{3}\) )\d{3}-\d{4}$' file.txt
```
## 220 Contains Duplicate III
使用set当做滑动窗口比较方便
``` C++
bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
	set<long long> s;
	int size = nums.size();
	for (int i=0;i<size;++i){
		if (i > k) {	
			s.erase(nums[i-k-1]);
		}
		auto pos = s.lower_bound((long long)nums[i] - t);
		if (pos != s.end() && *pos - nums[i] <= t) return true;
		s.insert(nums[i]);
	}   
	return false;     
}
```
## 235 Lowest Common Ancestor of a Binary Search Tree
LCA的简单版，都不需要管是不是BST当二叉树使就好
## 236 Lowest Common Ancestor of a Binary Tree
LCA的简单版，只有一个查询。递归就好了
## 262 
SQL题主要是API不熟悉。。。
``` sql
select Request_at Day, round(Count(if (status != "completed",TRUE,NULL))/Count(*),2) as "Cancellation Rate" 
from Trips 
where (Request_at between "2013-10-01" and "2013-10-03")
and Client_Id not in (select Users_Id from Users where Banned = "Yes")
and Driver_id not in (select Users_Id from Users where Banned = "Yes") 
group by Day;  
```
## 307 Range Sum Query - Mutable 
树状数组 单点修改 区间查询
## 457 Circular Array Loop
这题的题目很难理解。这个loop里的数必须是全部正或者全部负的，然后要求开始和终止在一个点。
使用快慢指针来判断，最后j1-j2相遇时看是不是只是一个点的循环。
``` java
class Solution {
    public boolean circularArrayLoop(int[] nums) {
        boolean loop = false;
        for (int i=0;i<nums.length && !loop;++i){
            if (nums[i] != 0){
                int j1 = i,j2 = i,dir = nums[i];
                do {
                    j1 = advanceBy(1, j1, nums, dir);
                    j2 = advanceBy(2, j2, nums, dir);
                } while (j1 >= 0 && j2 >= 0 && j1 != j2);
                
                if (j1 >= 0 && j2 >= 0 && j1 == j2) {
                    if (j1 == advanceBy(1, j1, nums, dir))
                        nums[j1] = 0;
                    else
                        loop = true;
                }
                
                nums[i] = 0;    
            }
        }
        
        return loop;
    }
    
    int advanceBy(int step, int j, int[] nums, int dir) {
        int n = nums.length;
        for (; step > 0; step--) {
            j = (j + nums[j] + n) % n;
            if (nums[j] * dir <= 0)
                return -1;
        }
        return j;
    }
}
```
## 468 Validate IP Address
这类题使用正则比较好写
如果不想一步到位先用\d+捕获数字然后判断范围也可以
``` c++
string validIPAddress(string IP) {
	    regex pat("(([0-9]|1[0-9][0-9]?|2[0-9]|2[0-4][0-9]|25[0-5])\\.){3}([0-9]|1[0-9][0-9]?|2[0-9]|2[0-4][0-9]|25[0-5])");     
	    if (regex_match(IP,pat))
		return "IPv4";	
	    regex pat2("([0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}");
	    if (regex_match(IP,pat2))
	    	return "IPv6";	
	       return "Neither";
    }
```
## 493 Reverse Pairs
类似归并排序求逆序对，只不过现在是a[i] > 2\*a[j]
``` c++
class Solution {
public:
    long long  temp[50005];
    long long  res;
    void mergesort(vector<long long>& nums,int l,int mid,int r){   
        int  nowl = l,nowr = mid+1,now = 0;
        while (nowl <= mid && nowr <= r){
            if (nums[nowl] <= 2*nums[nowr]) ++nowl;
            else {
                res += mid - nowl + 1; ++nowr;
            }
        }
        nowl = l, nowr = mid+1;
        while (nowl <= mid && nowr <= r){
            if (nums[nowl] < nums[nowr] ){
                temp[++now] = nums[nowl++];
            }
            else {
                temp[++now] = nums[nowr];
                ++nowr;
            }
        }
        while (nowl <= mid){
            temp[++now] = nums[nowl++];
        }
        while (nowr <= r){
            int index = mid;
            temp[++now] = nums[nowr++];
        }
        for (int i=l;i<=r;++i) nums[i] = temp[i-l+1];
    }
    
    void merge(vector<long long>& nums,int l,int  r){
        if (l == r) return;
        int  mid = (l + r) / 2;
        merge(nums,l,mid);
        merge(nums,mid+1,r);
        mergesort(nums,l,mid,r);
    }
    
    int reversePairs(vector<int>& nums) {
        res = 0;
        if (nums.empty()) return 0;
        vector<long long> nums2;
        nums2.reserve(nums.size() + 2);
        for (int i=0;i<nums.size();++i)
        	nums2.push_back(nums[i]);
        merge(nums2,0,nums.size()-1);
        return res;
    }
};
```
## 507 Perfect Number
直接算到根号n就可以了
注意数是一个平方数的情况
## 523 Continuous Subarray Sum
计算前缀和，s[i]是0-i的和 % k，则如果i-j的和是k的倍数，可以得到s[i] = s[j]
所以就是寻找存不存在这样的一对i,j
## 665 Non-decreasing Array
一个一个比较过去当遇到array[i]>array[i+1]时
有两种情况：一种是已经改过了那就直接false
否则要考虑怎么改：考虑改array[i] = array[i+1]但是这样要满足array[i-1] <= array[i+1]，如果这个改不了就只能将array[i+1] = array[i];
## 707 Design Linked List
可以使用dummyNode简化链表的边界情况操作，没啥难的
## 741 Cherry Pickup
正反两次走可以看成两次正向走，其中第一次走过的1会变成0。
设两个人的速度都为1，同时开始正向走
dp(t,x1,y1,x2,y2)为t时刻，第一个人走到(x1,y1)，第二个人走到(x2,y2)的最大收益
有x1+y1 = t,x2 + y2 = t
所以可以简化为dp(t,x1,x2)
dp(t,x1,x2) = max(dp(t-1,x1,x2-1),dp(t-1,x1-1,x2),dp(t-1,x1-1,x2-1)) + grid[x1][t-x1] + grid[x2][t - x2] * (x1 != x2)
观察到t的状态只与t-1状态相关，所以可以通过滚动数组压缩掉
最终时间O(n^3)空间O(n^2)
``` java
class Solution {
    public int cherryPickup(int[][] grid) {
        int n = grid.length,m = (n << 1) - 1;
        int[][] dp = new int[n][n];
        dp[0][0] = grid[0][0];
        for (int t=1;t<m;++t){
            for (int i=n-1;i>=0;--i){
                for (int j=n-1;j>=0;--j){
                    int p = t - i,q = t - j;
                    if (p < 0 || p >= n || q < 0 || q >= n || grid[i][p] < 0 || grid[j][q] < 0){
                        dp[i][j] = -1; continue;
                    }
                    
                     if (i > 0) dp[i][j] = Math.max(dp[i][j], dp[i - 1][j]);
		             if (j > 0) dp[i][j] = Math.max(dp[i][j], dp[i][j - 1]);
		             if (i > 0 && j > 0) dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - 1]);
		 
		             if (dp[i][j] >= 0) dp[i][j] += grid[i][p] + (i != j ? grid[j][q] : 0);
                }
            }
        }
        
        return Math.max(dp[n-1][n-1],0);
    }
}
```
## 771 Jewel & Stones
	数组记载一下J出现的字符，回S看一下。就这么简单
	顺带一提，通过这题发现使用toCharArray遍历数组比charAt要快一点
## 780 Reaching Points
当tx和ty都大于sx和sy的时候尝试缩短，等到在一条线上了就好找了
``` java
public boolean reachingPoints(int sx, int sy, int tx, int ty) {
        while (sx<tx && sy<ty) if (tx<ty) ty%=tx; else tx%=ty;
        return sx==tx && (ty-sy)%sx==0 || sy==ty && (tx-sx)%sy==0;
}
```
## 805 Split Array With Same Average
这题好厉害，第一次遇见传说中的NP题目，贴下网上看到的解法：
(1)对于原数组，设他被分为长度为k的A数组和长度为(n-k)的B数组，方便起见设k<=(n-k)，则可以得到total_sum / n = a_sum / k = b_sum / k，可以得到a_sum = totalsum * k / n 因为a_sum肯定是整数，所以则满足分配条件时需要满足total_sum * k % n = 0，可以通过此点先删掉一部分情况直接返回false
(2)尝试使用背包问题递推
dp[i][j]表示所有使用j个元素能否达到i这个sum
则
dp[i][j] |= dp[i-a[k]][j-1]; k从1遍历到n
(3) 就检查是否存在k满足total_sum * k%n=0 && dp[sum * k/n][i] == true，如果满足就返回true否则false
## 807 Max Increase to Keep City Skyline
水题，当grid[i][j] < maxi和maxj时+就是了
## 859 Buddy Strings
水题，注意原本字符串就相同的情况。
## 878 Nth Magical Number
分成一个一个以最小公倍数为结尾的组，每组就是在前面的基础上加个最小公倍数
长度是lcm/a + lcm/b - 1;
则通过n可以计算出前面的组数和当前的index
然后就是计算当前的index的数
可以通过暴力枚举得出。或者用公式(看不懂) 
double nearest = remain / (1./A + 1./B);
int remainIdx = (int)min(ceil(nearest / A) * A, ceil(nearest / B) * B);
## 887 Super Egg Drop 
以前oj上做过kn^2的
dp[x][i]为x层i个蛋所需的次数
dp[x][1] = x 
dp[x][i] = min(max(dp[x-k][i-1],dp[k-1][i])) + 1 k(1-x)
不过现在明显是过不了，需要更好的解法，
首先可以考虑优化原来的思路
观察到当k增大的时候，dp[x-k][i-1]会减少，dp[k-1][i]会增加，所以这两个点接近的时候的k是最后需要的k。另外进一步思考，当dp[x-k][i-1]第一次被dp[k-1][i]超越的时候其实就是最接近的时候，所以我们只需要循环到那个时候就可以直接退出
``` java
 public int superEggDrop(int k, int n) {
        int[][] dp = new int[k+2][n+2];
        for (int i=1;i<=n;++i){
            dp[1][i] = i;
            dp[0][i] = 0;
        }
        for (int j=1;j<=k;++j){
            dp[j][1] = 1;
        }
        for (int i=2;i<=k;++i){
            int x=1;
            for (int j=2;j<=n;++j){
                while (x<j+1) {
                    if (dp[i][x-1]>=dp[i-1][j-x]){
                        dp[i][j]=1 + dp[i][x-1];
                        break;
                    }
                    x+=1;
                }
            }
        }
        return dp[k][n];
    }
```
PS:发现其他思路竟然能到O(klogN)，牛人就是牛人
dp[x][step] x个鸡蛋扔step次能够测出的层数
每次扔一次，鸡蛋没碎，我们能够确认了dp[x][step-1]的层数
如果碎了确认dp[x-1][step-1]的层数。
dp[x][step] = dp[x-1][step-1] + dp[x][step-1] + 1;
所以我们只需要从0次开始循环遍历
``` java
 public int superEggDrop(int K, int N) {
        int dp[] = new int[K + 1], m = 0;
        for (m = 0; dp[K] < N; ++m)
            for (int k = K; k > 0; --k)
                dp[k] += dp[k - 1] + 1;
        return m;
    }
```
真的简洁明了，orz
