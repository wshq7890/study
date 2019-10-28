# 学习相关

## <u>零：算法相关</u>

### 1.递归和动态规划

**介绍递归和动态规划：**

**暴力递归**：

1.把问题转化为规模缩小了的同类问题的子问题

2.有明确的不需要继续进行递归的条件（base case）

3.有当得到了子问题的结果之后的决策过程

4.不记录每一个子问题的解

**动态规划：**

1.从暴力递归中来

2.将每一个子问题的解记录下来，避免重复计算

3.把暴力递归的过程，抽象成了状态表达

4.存在化简状态表达，使其更加简洁的可能



**题目一：求n！的结果**

**题目二：汉诺塔问题（打印n层汉诺塔从最左边移动到最右边的全部过程）**









## 一：LeetCode题解

#### 11.[盛最多水的容器]

**题目描述**

给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

![1572158797254](C:\Users\17\AppData\Roaming\Typora\typora-user-images\1572158797254.png)



方法一：暴力法

时间：O（$n^2$）   空间：O(1)

代码：

```java
class Solution {
    public int maxArea(int[] height) {
        if(height == null || height.length < 2){
            return 0;
        }
        int max = 0;
        for(int i = 0; i < height.length-1; i++){
            for(int j = i+1; j < height.length ; j++){
                int h = Math.min(height[i],height[j]);
                max = Math.max(max , (j-i)*h);
            }
        }
        return max;
    }
}
```

==方法二：==

【双指针】

首尾两个指针，用maxArea记录指针移动过程中出现的最大面积

最后两个指针相遇的时候，maxArea就是所求

```java
class Solution {
    //双指针法
    public int maxArea(int[] height) {
        if(height == null || height.length < 2){
            return 0;
        }
        int pre = 0;
        int last = height.length-1;
        int maxArea = 0;//记录最大面积
        while(pre < last){
            maxArea = Math.max(maxArea,(last-pre)*Math.min(height[pre],height[last]));
            if(height[pre] < height[last]){
                pre++;
            }else{
                last--;
            }
        }
        return maxArea;
    }
}
```







#### 17.[电话号码的字母组合]

**题目描述：**

![1572162891349](C:\Users\17\AppData\Roaming\Typora\typora-user-images\1572162891349.png)

==解法：==

【回溯】

回溯是一种通过穷举所有可能情况来找到所有解的算法。如果一个候选解最后被发现并不是可行解，回溯算法会舍弃它，并在前面的一些步骤做出一些修改，并重新尝试找到可行解。

给出回溯函数backTracking(combination,digits)

其中combination:目前已经产生的组合

digits:接下来要输入的数字

如果digits == "",那么说明combination已经组合好了

如果还有数字要输入，遍历这个数字所映射的字母，将当前字母添加到combination后面，也就是combination+letter作为下一次的参数

重复这个过程，输入下一次的参数backTrackint(combination+letter,digits.substring(1))



代码：

```java
class Solution {
    //https://blog.csdn.net/zdyueguanyun/article/details/79216998
    //这里是HashMap的初始化赋值
    /*
    第一个{}定义了一个匿名内部类
    第二个{}是一个实例初始化块
    坏处：如果这个对象要串行化，可能会导致串行化失败-->这里不太理解
    */
    HashMap<String,String> map = new HashMap<String,String>(){{
        put("2","abc");
        put("3","def");
        put("4","ghi");
        put("5","jkl");
        put("6","mno");
        put("7","pqrs");
        put("8","tuv");
        put("9","wxyz");
    }};
    List<String> res = new ArrayList<>();//用来最后返回结果
    
    public void backTracking(String combination,String digits){
        if(digits.length() == 0){
            res.add(combination);
        }
        else{String digit = digits.substring(0,1);
            for(int i = 0 ; i < map.get(digit).length() ;i++){
                //下面注释掉的这里的写法有问题，如果这么写，那么当i走完0进入1时，combination已经被修改了。所以我们只需要记住新的数字所对应的字母是什么，然后把这个字母（letter）和combination放在一起传入下面的回溯过程即可
                //combination = combination + map.get(digit).charAt(i);
                //backTracking(combination,digits.substring(1));
                String letter = map.get(digit).charAt(i)+"";
                backTracking(combination+letter,digits.substring(1));
            }
        }
    }
    
    public List<String> letterCombinations(String digits) {
        if(digits == null || digits.length() == 0){
            return res;
        }
        backTracking("",digits);
        return res;
    }
}
```



#### 22.[括号生成]

**题目描述：**

![1572271979651](C:\Users\17\AppData\Roaming\Typora\typora-user-images\1572271979651.png)



==解法：==

==【回溯法】==

记录左右括号的数量

如果左括号等于n，那么此时一直加右括号直到numRight == n，然后把String sb加入到res中

如果左括号不等于n，

1.numLeft>numRight，此时可以加入”（“或者“）”

2.numLeft == numRight，此时只能加入“（”



代码：

```java
class Solution {
    List<String> res = new ArrayList<>();
    
    public List<String> generateParenthesis(int n) {
        if(n < 1){
            return res;
        }
        String sb = "";
        generateCore(n,0,0,sb);
        return res;
    }
    
    public void generateCore(int n,int numLeft,int numRight,String sb){
        if(numLeft == n){
            while(numRight != n){
                sb = sb + ")";
                numRight++;
            }
            res.add(sb);
            return;
        }
        
        if(numLeft > numRight){
            generateCore(n,numLeft+1,numRight,sb+"(");//加'('
            generateCore(n,numLeft,numRight+1,sb+")");//加')'
        }else if(numLeft == numRight){
            generateCore(n,numLeft+1,numRight,sb+"(");//加'('
        }
    }
}
```



#### 31.[下一个排列]

**题目描述：**

![1572276310707](C:\Users\17\AppData\Roaming\Typora\typora-user-images\1572276310707.png)



==解法==

1.如果序列为降序，那么整体reverse即可

2.从后往前找，我们的目的是找到下一个更【大】的序列

我们需要找第一组，arr[i-1]<arr[i]的这一对i-1和i，此时i-1右侧都是降序的，所以我们要找一个刚好比arr[i-1]大的数字和arr[i-1]交换，然后把arr[i-1]右侧的内容reverse（因为此时arr[i-1]右侧是降序的，我们改成升序，这样的序列是刚好的）



代码：

```java
class Solution {
    
    public void nextPermutation(int[] nums) {
        boolean isSwaped = false;
        if(nums == null || nums.length < 1){
            return;
        }
        int j = nums.length-1;
        for(; j > 0 ; j-- ){
            //找到第一个连续的数对：左边的数nums[j-1]比右边的数nums[j]小
            //找到了就可以break了，不然还会往下走
            if(nums[j-1] < nums[j]){
                //在j-1的右侧找到一个比nums[j-1]大的数其中的最小的一个数
                int swapIndex = 0;
                int temp = j;
                while(temp < nums.length && nums[temp] > nums[j-1]){
                    swapIndex = temp;
                    temp++;
                }
                swap(nums,j-1,swapIndex);
                isSwaped = true;
                break;
            }
        }
        if(isSwaped){
            reverse(nums,j);//把j-1右边部分reverse
        }else{
            reverse(nums,0);
        }
    }
    
    //从i开始。包括i在内的右边reverse
    public void reverse(int[] arr,int i){
        if(arr == null){
            return;
        }
        int pre = i;
        int last = arr.length-1;
        while(pre < last){
            swap(arr,pre++,last--);
        }
    }
    
    
    public void swap(int[] arr,int i ,int j){
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```



### 数组：

#### 189.旋转数组

 给定一个数组，将数组中的元素向右移动 *k* 个位置，其中 *k* 是非负数。 



解法一：

先定义一步旋转的操作，执行k%length次

**时间复杂度：$O(k*n)$																	空间复杂度：$O(1) $**

```java
class Solution {
    public void rotate(int[] nums, int k) {
        //k >= 0
        if(nums == null || nums.length == 0){
            return;
        }
        for(int i = 0;i < k%nums.length ;i++){
            rotateOne(nums);
        }
    }
    
    //旋转1步的过程
    public void rotateOne(int[] arr){
        if(arr == null){
            return;
        }
        int old = arr[arr.length-1];
        int cur = 0;
        for(int i = 0;i < arr.length ;i++){
            cur = arr[i];//记住当前的数字
            arr[i] = old;
            old = cur;
        }
    }
}
```



解法二：

利用额外的数组

**时间复杂度：$O(n)$																	空间复杂度：$O(n)$**

```java
class Solution {
    //使用额外的数组
    public void rotate(int[] nums, int k) {
        if(nums == null || nums.length == 0){
            return;
        }
        int[] res = new int[nums.length];
        for(int i = 0;i < nums.length ;i++){
            res[(i+k)%nums.length] = nums[i];
        }
        for(int i = 0;i < nums.length ;i++){
            nums[i] = res[i];
        }
    }
}
```



==解法三：==

**【使用环状替换】**

直接把每一个数字放在它的最终位置。

时间复杂度：$O(n)$ 																		空间复杂度:$ O(1)$



注意：如果n%k == 0的话， （其中的k = k%n），我们会发现没有遍历到所有的数字，就又回到了出发点。

我们这时候要从下一个数字开始重复相同的过程。

这里面用了一个count变量来记录移动了多少次，最后的移动次数应该为nums.length，因此，如果count == nums.length，说明已经移动结束。



代码：

```java
class Solution {
    public void rotate(int[] nums, int k) {
        if(nums == null || nums.length == 0){
            return;
        }
        int count = 0;//统计交换的次数
        for(int start = 0;count < nums.length; start++){
            int cur = start;//cur-->已经排好的下标，初始化为第一个点
            int pre = nums[cur];//pre-->排好的最后一个值是多少
            //这里用do...while循环，因为最开始cur赋值为start，我们想让循环先进行一次
            do{
                int next = (cur + k) % nums.length;//cur的下一个位置
                int temp = nums[next];//next下标上的值
                nums[next] = pre;//替换
                pre = temp;//更新pre
                cur = next;//更新cur
                count++;//别忘了count要++!!!!!
            }while(cur != start);
        }
    }
}
```

==解法四：==

【使用反转】

我们旋转数组k次，k%n个元素会被移动到头部，剩下的元素向后移动。

我们先将所有元素反转，然后反转前k个元素，再反转后面n-k个元素，就能得到想要的结果。



代码：

```java
class Solution {
    public void rotate(int[] nums, int k) {
        if(nums == null || nums.length == 0 ){
            return;
        }
        reverse(nums,0,nums.length-1);
        reverse(nums,0,(k%nums.length)-1);
        reverse(nums,(k%nums.length),nums.length-1);
    }
    
    public void reverse(int[] nums,int start,int end){
        while(start < end){
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
}
```





### 链表：

#### 61.旋转链表

 给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。 



==想法：==

先连成环，然后找到合适的位置断开，相当于旋转了

**【注意】：找好断开的位置。如不明显，可举例说明！！！**



代码：

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
    public ListNode rotateRight(ListNode head, int k) {
        if(head == null){
            return head;
        }
        ListNode p = head;
        int length = 1;
        if(head.next == null || k == 0){
            return head;
        }
        while( p.next != null){
            p = p.next;
            length++;
        }
        p.next = head;
        p = head;
        //注意找到断开的位置
        for(int i = 0;i < length-(k%length)-1;i++){
            p = p.next;
        }
        head = p.next;
        p.next = null;
        return head;
    }
}
```









#### 237.删除链表中的节点

 请编写一个函数，使其可以删除某个链表中给定的（非末尾）节点，你将只被给定要求被删除的节点。 



代码：

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
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

#### 83.删除排序链表中的重复元素I



#### 82.删除排序链表中的重复元素II



### 28.实现strStr() （<u>*KMP算法详解*</u>）==没看懂KMP部分==



==题目描述==

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。



==一：暴力法==

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int M = needle.length();
        int N = haystack.length();
        /*
        当needle是空字符串时，返回什么值，这是一个面试中很好的问题
        对于本题，当needle是空字符串时返回0，与java的indexOf()定义相符合
        */
        if(M == 0){
            return 0;
        }
        //先转化成字符数组
        char[] txt = haystack.toCharArray();
        char[] pat = needle.toCharArray();
        
        for(int i = 0; i <= N - M; i++){
            int j;
            for(j = 0; j < M;j++){
                if(txt[i+j] != pat[j]){
                    break;
                }
            }
            if(j == M){
                return i;
            }
        }
        return -1;
    }
}
```

时间复杂度O（MN）									  空间复杂度O（1）

存在的问题：如果字符串中重复的字符比较多，就显得比较蠢



==二：KMP算法==

KMP算法的不同之处：花费一些空间记录一些信息

KMP 算法**永不回退**`txt` 的指针 i，不走回头路（不会重复扫描 txt），而是**借助 dp 数组中储存的信息**把 `pat `移到**正确的位置**继续匹配，时间复杂度只需 O(N)，用空间换时间。

 在 `pat` 匹配 `txt` 的过程中，只要明确了「当前处在哪个状态」和「遇到的字符是什么」这两个问题，就可以确定应该转移到哪个状态（推进或回退）。 

```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        // dp[状态][字符] = 下个状态
        /*
        状态：代表当前的状态
        字符：代表遇到的字符
        下个状态
        */
        dp = new int[M][256];
        // base case
        dp[0][pat.charAt(0)] = 1;
        // 影子状态 X 初始为 0
        int X = 0;
        // 构建状态转移图（稍改的更紧凑了）
        for (int j = 1; j < M; j++) {
            for (int c = 0; c < 256; c++) {
                dp[j][c] = dp[X][c];
            dp[j][pat.charAt(j)] = j + 1;
            // 更新影子状态
            X = dp[X][pat.charAt(j)];
        }
    }

    public int search(String txt) {
        int M = pat.length();
        int N = txt.length();
        // pat 的初始态为 0
        int j = 0;
        for (int i = 0; i < N; i++) {
            // 计算 pat 的下一个状态
            j = dp[j][txt.charAt(i)];
            // 到达终止态，返回结果
            if (j == M) return i - M + 1;
        }
        // 没到达终止态，匹配失败
        return -1;
    }

}
```



### 88.合并两个有序数组

**题目描述：**

给定两个有序整数数组 nums1 和 nums2，将 nums2 合并到 nums1 中，使得 num1 成为一个有序数组。

说明:

初始化 nums1 和 nums2 的元素数量分别为 m 和 n。
你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。

示例：

输入:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]



**解法一：**

【从前往后】，【双指针】

**时间复杂度：$O(m+n)$ 	**																	**空间复杂度:$O(m)$**



==**解法二：**==

【从后往前】比较，然后复制

**时间复杂度：$O(m+n)$ 																		空间复杂度:$O(1)$**

代码：

```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if(nums1 == null || nums2 == null){
            return ;
        }
        int length = nums1.length;
        //只要nums1和nums2有一个没到头，就进入循环
        while( m > 0 || n > 0){
            //如果nums2到头了，那就直接返回即可
            if(n == 0){
                return ;
            }
            //如果nums1到头了，那就把nums2剩余部分复制过来
            if(m == 0){
                while(n > 0){
                    nums1[length-1] = nums2[n-1];
                    length--;
                    n--;
                }
                return;
            }
            //谁大就把谁复制过来，然后下标移动
            if(nums1[m-1] > nums2[n-1]){
                nums1[length-1] = nums1[m-1];
                length--;
                m--;
            }else{
                nums1[length-1] = nums2[n-1];
                length--;
                n--;
            }
        }
    }
}

```

```java
//简化版，双指针
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int p1 = m-1;
        int p2 = n-1;
        int p = m+n-1;
        //两个指针都大于等于0，那么进入循环
        while(p1 >= 0 && p2 >= 0){
            //使用三目运算符简化代码
            nums1[p--] = nums1[p1] > nums2[p2] ? nums1[p1--] : nums2[p2--];
        }
        //出循环的时候，p1或者p2有一个到了-1位置
        System.arraycopy(nums2,0,nums1,0,p2+1);
    }
}
```











## 二：Java面试相关

### 1.为什么String.length（）有括号，而数组的长度length没有括号？



1.对于String：

String是一个类，任何创建字符串都是创建了一个对象，而length()是String类的一个方法

所以在创建了字符串以后，也就是创建了一个String类的对象，可以调用length（）方法返回字符串的长度

2.对于数组：

length是数组的父类Array从Object继承过来的属性，是类的属性，不需要加括号



### 2.Java中如何支持正则表达式表达？



 Java中的String类提供了支持正则表达式操作的方法，包括：matches()、replaceAll()、replaceFirst()、split()。此外，Java中可以用Pattern类表示正则表达式对象，它提供了丰富的API进行各种正则表达式操作，如： 

```java
import	java.util.regex.Matcher;
import	java.util.regex.Pattern;
class	RegExpTest {
	public	static	void	main(String[] args) {
        String str = "成都市(成华区)(武侯区)(高新区)";
        Pattern p = Pattern.compile(".*?(?=\\()");
        Matcher m = p.matcher(str);
        if(m.find()) {
            System.out.println(m.group());
        }
  	}
}
```



### 3.描述一下正则表达式及其用途？	



在编写处理字符串的程序时，经常会有查找符合某些复杂规则的字符串的需要。

**正则表达式就是用于描述这些规则的工具。**换句话说，正则表达式就是记录文本规则的代码。

计算机处理的信息更多的时候不是数值而是字符串，正则表达式就是在进行字符串匹配和处理的时候最为强大的工具，绝大多数语言都提供了对正则表达式的支持。 



### 4.讲一下&和&&的区别？



&运算符有两种用法：**(1)按位与；(2)逻辑与**

&&运算符是**短路与**运算。

逻辑与跟短路与的差别是非常巨大的，虽然二者都要求运算符左右两端的布尔值都是true整个表达式的值才是true。

&&之所以称为短路运算是因为，如果&&左边的表达式的值是false，右边的表达式会被直接短路掉，不会进行运算。

举例：很多时候我们可能都需要用&&而不是&，例如在验证用户登录时判定用户名不是null而且不是空字符串，应当写为：``username != null && !username.equals("")``，二者的顺序不能交换，更不能用&运算符，因为第一个条件如果不成立，根本不能进行字符串的equals比较，否则会产生``NullPointerException``异常。 



### 5.int和Integer有什么区别？



|      **基本数据类型：**       |           包装类            |
| :---------------------------: | :-------------------------: |
| 整型：byte,short,**int**,long | Byte,Short,**Integer**,Long |
|      浮点型:float,double      |        Float,Double         |
|        字符型:**char**        |        **Character**        |
|        布尔型:boolean         |           Boolean           |



int的默认值是0，Integer的默认值是null



为了将基本数据类型能**当成对象操作**，Java为每一个基本数据类型引入了对应的包装类型，int的包装类就是Integer。

从Java5开始，引入了自动装箱（Integer.valueOf() ）/自动拆箱(xx.intValue() )机制，使二者可以相互转换。



注意：

​			Integer里面默认的缓存数字是 -128 --> 127 

​			1.Integer 与 Integer相互比较，数据在-128 -->127 范围内，就会从缓存中拿数据





## 三：剑指Offer

### 面试题3：数组中重复的数字



#### 1.找出数组中重复的数字



 **题目描述：**

在一个长度为n的数组里的所有数字都在0到n-1的范围内。

 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。

请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2或者3。 



==解法三：==

注意到数组中的数字都是0~n-1范围的。

若数组中不存在重复元素，则下标与数组中的值应该是对应相等的；数组中有重复的数字，那么有些位置可能存在多个数字，有些位置可能没有数字。

现在重排这个数组。

从头到尾扫描，来到i位置时，看看numbers[i] == i,若相等，接着往下扫描；若不相等，拿这个numbers[i]和numbers[numbers[i]]比较，若相等，说明重复了；若不相等，则i下标和numbers[i]下标位置交换元素，然后继续比较。



**时间复杂度：$ O(n)$** 									**空间复杂度:$O(1)$**

代码：

```java
public class Solution {
	//函数中参数的含义参考解法二
    public boolean duplicate(int numbers[],int length,int [] duplication) {
        //边界条件的判断
        if(numbers == null || numbers.length <= 0){
            return false;
        }
        //要符合题意，每个位置的元素范围要是0~n-1的
        for(int i = 0 ; i < numbers.length ; i++){
            if(numbers[i] < 0 || numbers[i] > numbers.length - 1 ){
                return false;
            }
        }
        
        for(int i = 0 ; i < numbers.length ; i++ ){
            while(numbers[i] != i){
                if(numbers[i] == numbers[numbers[i]]){
                    duplication[0] = numbers[i];
                    return true;
                }
                swap(numbers,i,numbers[i]);
            }
        }
        return false;
    }
    
    public void swap(int[] arr,int i ,int j){
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```



解法一：

对输入的数组【**排序**】。在排序的数组中找出重复的数字。

排序一个长度n的数组需要    **$O(nlogn)$**  的时间



解法二：

用【**哈希表**】

从头到尾扫描，当扫描到一个数字的时候，可以用O(1)的时间判断哈希表里是否包含了这个数字。

若没有，则加入哈希表；若存在，就找到了一个重复的数字。

**时间复杂度$O(n) $            						空间复杂度$O(n)$**

代码：

```java
import java.util.HashMap;

public class Solution {
    // Parameters:
    //    numbers:     an array of integers
    //    length:      the length of array numbers
    //    duplication: (Output) the duplicated number in the array number,length of duplication array is 1,so using duplication[0] = ? in implementation;
    
    // Here duplication like pointor in C/C++, duplication[0] equal *duplication in C/C++
    //  这里要特别注意~返回任意重复的一个，赋值duplication[0]
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    public boolean duplicate(int numbers[],int length,int [] duplication) {
        //注意这里要先判断numbers是否为空，不为空的情况下分别看下长度是不是0和1
        //如果是，则不可能包含重复元素
        if(numbers == null || numbers.length <= 0){
            return false;
        }
        for(int i = 0 ; i < numbers.length ; i++){
            if(numbers[i] < 0 || numbers[i] > numbers.length - 1 ){
                return false;
            }
        }
        length = numbers.length; 
        HashMap<Integer,Integer> map = new HashMap<>();
        //length >= 2
        for(int i = 0;i < length; i++){
            //如果不在map中，就放进去
            if(!map.containsKey(numbers[i])){
                map.put(numbers[i],1);
            }else{//在map中，那就放进duplication[]中，然后返回true即可
                duplication[0] = numbers[i];
                return true;
            }
        }
        return false;
    }
}
```





#### 2.不修改数组找出重复的数字



**题目描述：**

在一个长度为n+1的数组里的所有数字都在1到n的范围内。

所以数组中至少有一个数字是重复的。

请找出数组中任意一个重复的数字，但不能修改输入的数组。

 例如，如果输入长度为8的数组{2,3,5,4,3,2,6,7}，那么对应的输出是第一个重复的数字2或者3。 



**解法一：**

创建一个长度为$n+1$的辅助数组，然后逐一把原数组的每个数字复制到辅助数组。

如果原数组中被复制的数字是m，则把它复制到辅助数组中下标为m的位置。很容易知道哪个数字重复了。

**空间复杂度：$O(n)$**



==解法二：==

某范围内数字的个数很重要：如1~n的范围内，如果包含n+1个数字，则一定含有重复数字

【二分查找】的思路

把1~n的数字从中间的数字m分成两部分:1-m 和 m+1---n

如果1~m的数字的数目超过了m，则这一半的区间里一定包含重复的数字；否则，另一半m+1---n的区间里一定包含重复的数字。

可以继续把包含重复数字的区间一分为二， 直到找到一个重复的数字。

在二分查找的过程中，多了一步统计区间里的数字的数目。



**【这种方法不能保证找出所有重复的数字】**

如数组{2,3,5,4,3,2,6,7}中的重复数字2。在1~2范围上有1和2两个数字，这个范围的数字也出现了两次。

此时用这个算法不能确定每个数字各出现一次还是某个数字出现了两次。



**时间复杂度:$O(nlogn)$  					空间复杂度:$O(1)$**



### （未）面试题4：二维数组的查找



### 面试题5：替换空格

**题目描述：**

 请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。 



**解法一：**

使用【StringBuffer】,从头到尾遍历

遇见空格，就加入"%20"；否则，直接加入当前的字符

注意最后要把StringBuffer改成String返回

**代码：**

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
        if(str == null){
            return "";
        }
        StringBuffer res = new StringBuffer();
        char[] arr = str.toString().toCharArray();
        if(arr.length == 0){
            return "";
        }
        for(int i = 0;i < arr.length ;i++){
            if(arr[i] == ' '){
                res.append("%20");
                continue;
            }
            res.append(arr[i]);
        }
        return res.toString();
    }
}
```



**解法二：**

**若要返回修改过后的str**

**从前往后修改$O(n^2)$**，==**从后往前修改$O(n)$**==

代码：

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
        if(str == null){
            return null;
        }
        int spaceNum = 0;//空格数量
        for(int i = 0;i < str.length(); i++){
            if(str.charAt(i) == ' '){
                spaceNum++;
            }
        }
        int oldIndex = str.length() - 1;
        int newLength = str.length() + spaceNum * 2;
        int newIndex = newLength - 1;
        str.setLength(newLength);
        while(oldIndex < newIndex){
            //注意：此处的oldIndex--不能放在if语句中，不然每次都会--
            if(str.charAt(oldIndex) == ' '){
                str.setCharAt(newIndex--,'0');
                str.setCharAt(newIndex--,'2');
                str.setCharAt(newIndex--,'%');
                oldIndex--;
            }else{
                str.setCharAt(newIndex--,str.charAt(oldIndex--));
            }
        }
        return str.toString();
    }
}
```



#### 相关题目：Leetcode 88.合并两个有序数组



### 链表相关：

#### 面试题6：从尾到头打印链表

**题目描述**

输入一个链表，按链表从尾到头的顺序返回一个ArrayList。



==**解法二：**==

【遍历链表】

遍历的顺序是**从头到尾**，打印的顺序是**从尾到头**

典型的**“后进先出”**，可以使用**【栈】**来实现

代码：

```java
/**
*    public class ListNode {
*        int val;
*        ListNode next = null;
*
*        ListNode(int val) {
*            this.val = val;
*        }
*    }
*
*/
import java.util.ArrayList;
import java.util.Stack;

public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        //使用栈
        Stack<ListNode> stack = new Stack<>();
        ArrayList<Integer> list = new ArrayList<>();
        if(listNode == null){
            return list;
        }
        ListNode p = listNode;
        while(p != null){
            stack.push(p);
            p = p.next;
        }
        while(!stack.isEmpty()){
            ListNode temp = stack.pop();
            list.add(temp.val);
        }
        return list;
    }
}
```



**解法三：**

既然想到了用【栈】，【递归】的本质就是一个栈结构

本题可以用【**递归**】来解决

要实现反过来输出链表，我们每访问一个节点的时候，先递归输出它后面的节点，再输出该节点本身，这样链表的输出结果就反过来了

【问题】：当链表很长的时候，会导致函数调用的层级很深，可能导致函数调用栈溢出。

显然，用栈基于循环实现的代码的鲁棒性要好一些。

代码：

```java
/**
*    public class ListNode {
*        int val;
*        ListNode next = null;
*
*        ListNode(int val) {
*            this.val = val;
*        }
*    }
*
*/
import java.util.ArrayList;

public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> res = new ArrayList<>();
        printList(listNode,res);
        return res;
    }
    
    public void printList(ListNode listNode,ArrayList<Integer> res){
        if(listNode == null){
            return;
        }
        printList(listNode.next,res);
        res.add(listNode.val);
    }
}
```





**解法一：**

自然的想法，【反转链表】

然后从头到尾输出

但是这种方法**会改变原来的链表结构**

==面试中，如果我们打算修改输入的数据，最好先问面试官是不是允许修改==



代码：

```java
/**
*    public class ListNode {
*        int val;
*        ListNode next = null;
*
*        ListNode(int val) {
*            this.val = val;
*        }
*    }
*
*/
import java.util.ArrayList;

public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> list = new ArrayList<>();
        if(listNode == null){
            return list;
        }
        
        //反转链表，然后加入list
        ListNode pre = new ListNode(0);
        ListNode cur = listNode;
        ListNode next = null;
        while(cur != null){
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        //此时pre是头节点，且要注意，此时尾节点多了一个
        while(pre.next != null){
            list.add(pre.val);
            pre = pre.next;
        }
        return list;
    }
}
```



#### 面试题18：删除链表的节点

**题目一：** **在$O(1)$时间内删除链表节点。**

给定单向链表的头指针head和一个节点指针i，定义一个函数在$O(1)$时间内删除该节点。



想法一：从头到尾遍历，发现节点h的next指针指向i，则把h的next指针指向i的下一个节点

时间复杂度：$O(n)$

**==想法二：==**我们可以很方便的知道要删除节点的下一个节点，如果把下一个节点的内容复制到要删除的节点上覆盖掉原有的内容，再把下一个节点删除。就相当于把当前需要删除的节点删除了。

**两个问题：**一：要删除的节点在链表的尾部，那么它没有下一个节点。那么我们还是要从头节点开始，顺序遍历得到该节点的前序节点，并完成删除操作。

​				   二：如果链表中只有一个节点，我们需要把头指针设成null。

时间复杂度：$O(1)$    =      $[O(1)*(n-1)+O(n)]/n$ 

上述想法基于一个**【假设】**：要删除的节点的确在链表中。我们需要$O(n)$ 的时间判断链表中是否包含某一节点。



**题目二：** **删除排序链表中重复的节点**

在一个排序的链表中，如何删除重复的节点？



==解法一：==

【递归解法】

代码：

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
    //递归写法
    public ListNode deleteDuplicates(ListNode head) {
        //head为空，返回head
        if(head == null){
            return head;
        }
        //head不空
        //head后面可能为null，可能有节点，节点的值可能相同或者不相同
        //只有后面有节点，并且后面节点的值和head的值相同，才会把head这一串的节点都删掉
        if(head.next != null && head.val == head.next.val){
            //让head来到要删除的节点串的最后一个
            while(head != null && head.next != null && head.val == head.next.val){
                head = head.next;
            }
            return deleteDuplicates(head.next);
        }else{//head后面是空，或者是和head值不同的节点，那么head就留下来了。修改head的next指针
            head.next = deleteDuplicates(head.next);
        }
        //head留下来了，那么最后返回的就是head
        return head;
    }
}
```



==解法二：==

【非递归解法】

- 定义一个 dummy 头结点，链接上原链表，cur 指向原链表头部
- ① 当前结点value ！= 当前结点的下一结点value。那么让pre指针来到当前结点，这样就链接了前一结点和当前结点。然后当前结点移至下一结点
- ② 当前结点value == 当前结点的下一结点value。那么就让 cur 一直往下走直到 当前结点value ！= 当前结点的下一结点value，然后此时是不能动 pre 指针的，要避免后面还有重复的，所以让 pre->next = cur->next。然后当前结点移至下一结点。
- 循环结束的条件结合例子即可，如[1,1]

\> - 时间复杂度：O(n) > - 空间复杂度：O(1)



代码：

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
    public ListNode deleteDuplicates(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode pre = dummy;
        ListNode cur = head;
        while(cur != null && cur.next != null){
            if(cur.val != cur.next.val){
                pre = cur;
                cur = cur.next;
            }else{
                while(cur.next != null && cur.val == cur.next.val){
                    cur = cur.next;
                }
                pre.next = cur.next;
                cur = cur.next;
            }
        }
        return dummy.next;
    }
}
```



#### 面试题22：链表中倒数第k个节点

**题目描述**

输入一个链表，输出该链表中倒数第k个结点。



==解法一：==

【栈】

代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
import java.util.Stack;

public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head == null){
            return head;
        }
        Stack<ListNode> stack = new Stack<>();
        int length = 0;
        ListNode p = head;
        //每一个节点进栈，然后计算链表的长度
        while(p != null){
            stack.push(p);
            p = p.next;
            length++;
        }
        //k的边界检验
        if(k <= 0 || k > length){
            return null;
        }
        //输出倒数第k个节点
        ListNode res = null;
        for(int i = 0;i < k;i++){
            res = stack.pop();
        }
        return res;
    }
}
```



==解法二：==

【快慢指针】

快指针先走$k-1$步，要注意边界条件的判定

三个问题：1.输入的head为null 2.节点总数少于k 3.输入的参数k是0



代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    //双指针
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head == null){
            return null;
        }
        ListNode fast = head;
        ListNode slow = head;
        //k是否合法
        if(k <= 0){
            return null;
        }
        //快的先走k-1步
        for(int i = 0; i < k-1;i++){
            //如果k太大，那么fast会提前遇见null，即输入不合法
            if(fast.next != null){
                fast = fast.next;
            }else{
                return null;
            }
        }
        while(fast.next != null){
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
}
```



#### 面试题24：反转链表

==解法：==

代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode pre = null;
        ListNode cur = head;
        ListNode next = cur.next;
        while(cur != null){
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
}
```



#### 面试题25：合并两个排序的链表

==【非递归】==

代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        if(list1 == null){
            return list2;
        }
        if(list2 == null){
            return list1;
        }
        
        //list1和list2 都不空
        //确定head是list1还是list2
        ListNode head = null;
        if(list1.val < list2.val){
            head = list1;
            list1 = list1.next;
        }else{
            head = list2;
            list2 = list2.next;
        }
        //生成新链表
        ListNode p = head;
        while(list1 != null && list2 != null){
            if(list1.val < list2.val){
            p.next = list1;
            list1 = list1.next;
            p = p.next;
        }else{
            p.next = list2;
            list2 = list2.next;
            p = p.next;
            }
        }
        //若list2没了
        if(list1 != null){
            p.next = list1;
        }
        //若list1没了
        if(list2 != null){
            p.next = list2;
        }
        return head;
    }
}
```



==【递归】==

代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    //递归
    public ListNode Merge(ListNode list1,ListNode list2) {
        //base case
        if(list1 == null){
            return list2;
        }
        if(list2 == null){
            return list1;
        }
        
        ListNode head = null;
        if(list1.val < list2.val){
            head = list1;
            head.next = Merge(list1.next,list2);
        }else{
            head = list2;
            head.next = Merge(list1,list2.next);
        }
        return head;
    }
}
```



#### 面试题52：两个链表的第一个公共节点

解法一：

【从后往前比较】【栈】【后进先出】

**时间复杂度：$O(m+n)$ 					空间复杂度：$O(m+n)$**



代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
import java.util.Stack;

public class Solution {
    //栈
    public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
        if(pHead1 == null || pHead2 == null){
            return null;
        }
        Stack<ListNode> stack1 = new Stack<>();
        Stack<ListNode> stack2 = new Stack<>();
        ListNode p1 = pHead1;
        ListNode p2 = pHead2;
        while(p1 != null){
            stack1.push(p1);
            p1 = p1.next;
        }
        while(p2 != null){
            stack2.push(p2);
            p2 = p2.next;
        }
        ListNode m1 = stack1.pop();
        ListNode m2 = stack2.pop();
        ListNode res = null;
        while(m1.val == m2.val){
            //res先记录上一次栈弹出来的元素
            res = m1;
            //如果某个栈空了，此时m1.val = m2.val，说明第一个公共节点就是res，返回res
            if(!stack1.isEmpty()){
                m1 = stack1.pop();
            }else{
                return res;
            }
            if(!stack2.isEmpty()){
                m2 = stack2.pop();
            }else{
                return res;
            }
        }
        return res;
    }
}
```



==解法二：==

**比较解法一更简单的解法，不需要额外的辅助空间，空间复杂度为O（1）**

两个指针，分别遍历list1和list2，遍历的过程中记录两个链表的长度【差值】

如果p1 != p2，那么一定不存在公共节点

然后让p1是长链表的头，p2是短链表的头

让p1先走【差值】这么多步，然后p1和p2一起走

p1和p2遇见的时候，返回p1/p2即可

**时间复杂度：$O(m+n)$ 					空间复杂度：$O(1) $**



代码：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
         if(pHead1 == null || pHead2 == null){
             return null;
         }
        ListNode p1 = pHead1;
        ListNode p2 = pHead2;
        int n = 0;
        while(p1.next != null){
            n++;
            p1 = p1.next;
        }
        while(p2.next != null){
            n--;
            p2 = p2.next;
        }
        //如果p1和p2的地址不同，那么一定不存在公共节点
        if(p1 != p2){
            return null;
        }
        //p1先走
        p1 = n > 0 ? pHead1 : pHead2;
        p2 = p1 == pHead1 ? pHead2 : pHead1;
        while(n != 0){
            p1 = p1.next;
            n--;
        }
        while(p1.val != p2.val){
            p1 = p1.next;
            p2 = p2.next;
        }
        return p1;
    }
}
```



#### 面试题62：圆圈中最后剩下的数字

**题目描述**

每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1)

如果没有小朋友，请返回-1



==解法一：【经典解法】==

【环形链表模拟圆圈】

**时间复杂度：$O(mn)$																		空间复杂度：$O(n) $**

时间：每删除一个元素要m步运算，一共n个元素

空间：用一个辅助链表来模拟圆圈



```java
import java.util.LinkedList;

public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if(m < 1 || n < 1){
            return -1;
        }
        LinkedList<Integer> list = new LinkedList<>();
        //环形链表来模拟圆圈
        for(int i = 0; i < n; i++){
            list.add(i);
        }
        int index = 0;
        while(list.size() > 1){
            //移动m-1次就可以移动到要删除的元素上
            //注意这里remove(index)后，index指的就是下一个节点了，详情参考								//	https://www.cnblogs.com/xiaoxi/p/6102220.html
            for(int i = 0; i < m-1;i++){
                index = (index + 1) % list.size();
            }
            list.remove(index);
        }
        return list.get(0);
    }
}
```



==解法二：==

【数学公式】【创新的解法】

定义f(n,m)：表示每次在n个数字0,1,2...n-1中删除第m个数字最后剩下的数字

第一个被删除的数字是**(m-1)%n-->记为k**

剩下的数字为：0,1,2...k-1,k+1,...,n-1

下一次删除从k+1开始，因此顺序为：k+1,...,n-1,0,1,2,...,k-1(一共n-2个)，该序列最后剩下的数字也是关于n和m的函数，但是和最开始定义的f(n,m)不同，因此此处定义为$f^{'}(n-1,m)$ 

最初序列最后剩下的数字f(n,m)一定是删除一个数字后的序列最后剩下的数字，即f(n,m) = $f^{'}(n-1,m)$ 

映射：

k+1-->0

k+2-->1

...

n-1-->n-k-2

0-->n-k-1

1-->n-k

...

k-1-->n-2

该映射定义为p，则p(x) = (x-k-1)%n，其逆映射为$p^{-1}(x)=(x+k+1)$ %$n$

映射之后的序列，最后剩下的数字为f(n-1,m)

则 $f^{'}(n-1,m)$ =$p^{-1}[f(n-1,m)]$ = $[f(n-1,m)+k+1]$%$n$  又(m-1)%n = k 代入进去

有$f(n,m)=f^{'}(n-1,m)=[f(n-1,m)+m]$%$n$  (n>1)

n=1时，f（n,m）=0



**时间复杂度：$O(n) $																		空间复杂度：$O(1) $**



代码：

```java
public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if(n < 1 || m < 1){
            return -1;
        }
        int res = 0;
        for(int i = 2; i <= n;i++){
            res = (res + m) % i;
        }
        return res;
    }
}
```







#### 面试题36：二叉搜索树与双向链表

#### 面试题35：复杂链表的复制







