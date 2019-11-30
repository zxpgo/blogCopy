---

title: LeetCode算法题目C++
date: 2019-06-22 22:28:50
tags:  面试题目

---

LeetCode刷起来！<!--more-->

### 7. 整数反转

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例 1:
	
	输入: 123
	输出: 321
 示例 2:
	
	输入: -123
	输出: -321
示例 3:

	输入: 120
	输出: 21
注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31,  2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0

__思路：__每次取整数的最后一位，将整数除以10，一直循环得到整数为0。其中需要注意，整数翻转的时候可能超出了整数的表示范围（-2^32,2^32-1），所以乘以10之前需要判断乘以10之前会不会超过表示范围。

代码：

	int reverse(int x) {
	        int ans = 0;
	        while(x)
	        {
	            if (ans > INT_MAX/10 || ans < INT_MIN/10)
	                return 0;
	            ans = (x % 10) + ans * 10;
	            x = x /10;
	        }
	        return ans;
	    }


### 9. 回文数

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:
	
	输入: 121
	输出: true
示例 2:
	
	输入: -121
	输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
示例 3:
	
	输入: 10
	输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
进阶:

你能不将整数转为字符串来解决这个问题吗？

__思路：__首先判断整数是否为正数，如果负数则直接返回false，如果x等于0直接返回true。然后就是将将x一位一位放入容器vector中。最后两个指针一个从头开始一个从尾开始，如果两个相等，则同时加加，如果不相等直接返回false。


代码：

	bool isPalindrome(int x) {
	        vector<int> vi;
	        if (x < 0 )
	            return false;
	        else if (x == 0)
	            return true;
	        else
	        {
	           
	            while (x)
	            {
	                vi.push_back(x%10);
	                x = x /10;
	            }
	        }
	        auto ite_begin = vi.begin();
	        auto ite_end = vi.end()-1;
	        while (ite_begin < ite_end)
	        {
	            if (*ite_begin == *ite_end)
	            {
	                ite_begin++;
	                ite_end--;
	            }
	            else
	            {
	                return false;
	            }
	        }
	        return true;       
	    }

### 13. 罗马数字转整数

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

	字符          数值
	I             1
	V             5
	X             10
	L             50
	C             100
	D             500
	M             1000

例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例 1:
	
	输入: "III"
	输出: 3
示例 2:

	输入: "IV"
	输出: 4
示例 3:
	
	输入: "IX"
	输出: 9
示例 4:

	输入: "LVIII"
	输出: 58
	解释: L = 50, V= 5, III = 3.
示例 5:

	输入: "MCMXCIV"
	输出: 1994
	解释: M = 1000, CM = 900, XC = 90, IV = 4.

__思路：__这个题目写得有些复杂，其实挺简单的。题目意思是：小数在大数左侧，则小数变成相应的负数。即`IXLDI`，结果可以表示为`-1-10-50+500+1`。

代码：

	int romanToInt(string s) {
	        map<char, int> cim;
	        cim['I'] = 1;
	        cim['V'] = 5;
	        cim['X'] = 10;
	        cim['L'] = 50;
	        cim['C'] = 100;
	        cim['D'] = 500;
	        cim['M'] = 1000;
	        int ans = 0;
	        for (int i = 0; i < s.size(); i++)
	        {
	            if (i < s.size()-1 && cim[s[i]] < cim[s[i+1]])
	                ans -= cim[s[i]];
	            else
	                ans += cim[s[i]];
	        }
	        return ans;
	    }


### 14. 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

示例 1:

	输入: ["flower","flow","flight"]
	输出: "fl"
示例 2:

	输入: ["dog","racecar","car"]
	输出: ""
	解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z

__思路：__首先确定所有字符串中最短字符串的长度，所有字符串比较的次数就是最短字符串的长度。然后就是针对第一个字符，每次字符串都比较一次，依次比较，如果相等，将当前字符添加到str（用来存储最终的结果），遇到一个不相等，直接break当前循环，返回str。

注意，在C++中string，不能用str[i]往里面填加字符，而应该采用追加的形式，即`str += 's'`。

代码：

	string longestCommonPrefix(vector<string>& strs) {
	        int len = INT_MAX;
	        string str = "";
	        if (strs.size() < 1)
	            return "";
	        for (int i = 0; i < strs.size(); i++)
	        {
	            len = min(len, (int)strs[i].size());
	        }
	        int j = 0;
	        while (j < len)
	        {
	            int i = 0;
	            while ((i < strs.size()-1) && (strs[i][j] == strs[i+1][j]) )
	                i++;   
	            if (i == strs.size()-1)
	               str += strs[0][j];
	            else
	                break;
	            j++;
	        }
	        return str;
	    }
	 

### 20. 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

示例 1:
	
	输入: "()"
	输出: true
示例 2:

	输入: "()[]{}"
	输出: true
示例 3:

	输入: "(]"
	输出: false
示例 4:
	
	输入: "([)]"
	输出: false
示例 5:
	
	输入: "{[]}"
	输出: true

思路： 如果遇到([{，就放入栈中，如果遇到)]}，首先判断栈是否为空，如果不为空，然后从栈中弹出元素，看是否能够组成一对，如果没法组成一对，直接返回false。

还有另一种方法，就如遇到(，就将)入栈；如果遇到[，就将]入栈；果遇到{就将}入栈。然后遇到([{就从栈中弹出元素，判断跟当前元素是否相等。

代码：

	    bool isValid(string s) {
	        stack<char> sc;
	        if (s.size() == 1)
	            return false;
	        if (s.size() == 0)
	            return true;
	        int i = 0;
	        while (s[i] != '\0')
	        {
	            if (s[i] == '(' || s[i] == '{' || s[i] == '[')
	            {
	                sc.push(s[i]);
	                i++;
	            }
	            else if (!sc.empty() && ((sc.top() == '(' && s[i] == ')') || (sc.top() == '[' && s[i] == ']') || (sc.top() == '{' && s[i] == '}') ))
	            {
	                sc.pop();
	                i++;
	            }
	            else
	            {
	                return false;
	            }
	        }
	        if (sc.empty())
	            return true;
	        return false;
	    }

### 28. 实现strStr()


实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:

	输入: haystack = "hello", needle = "ll"
	输出: 2
示例 2:

	输入: haystack = "aaaaa", needle = "bba"
	输出: -1
说明:

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

__思路：__

第一种解法：暴力解法。设父字符串为P，子串为L。对L中的字符一个一个在P中对比，如果遇到不想当的，P中的标记向前走一步，L中的标记回到0。直到其他一个字符串到达末尾结束。

第二种解法：KMP，比较复杂，在此不论述，，可以百度一下。

还有就是可以直接调用string的库函数。

代码：

	int strStr(string haystack, string needle) {
      /*直接调用string库
      int temp = haystack.find(needle);
        return temp;*/
      int len_hay = haystack.size();
      int len_nee = needle.size();
      if (len_nee == 0)
          return 0;
      if (len_hay == 0)
          return -1;
      int i = 0;
      int j = 0;
      int temp = 1;
      while (needle[j] != '\0' && haystack[i] != '\0')
      {
          if(haystack[i++] != needle[j++])
          {
              j = 0;
              i = temp;
              temp += 1;
          }
      }
      if (needle[j] == '\0')
          return i-needle.size();
      else 
          return -1;
    }


### 38. 报数

报数序列是一个整数序列，按照其中的整数的顺序进行报数，得到下一个数。其前五项如下：

	1.     1
	2.     11
	3.     21
	4.     1211
	5.     111221

- 1 被读作  "one 1"  ("一个一") , 即 11。
- 11 被读作 "two 1s" ("两个一"）, 即 21。
- 21 被读作 "one 2",  "one 1" （"一个二" ,  "一个一") , 即 1211。

给定一个正整数 n（1 ≤ n ≤ 30），输出报数序列的第 n 项。

注意：整数顺序将表示为一个字符串。

 

示例 1:

	输入: 1
	输出: "1"
示例 2:
	
	输入: 4	 
	输出: "1211"

思路： 对于每次都判断一下当前字符跟下一个字符是否相等，如果相等则，计数器count加1，否则直接将count转换为字符串加上当前字符一起加到答案字符串的结尾，直到字符串到达结尾结束。

代码：

	string countAndSay(int n) {
	        string s;
	        string str("1");
	        if (n == 1)
	            return  str;
	        for (int i = 2; i <= n; i++)
	        {
	            int j = 0;
	            int count = 1;
	            string s;
	            while(str[j] != '\0')
	            {
	                if (str[j] == str[j+1])
	                    count++;
	                else
	                {
	                    s +=to_string(count)+str[j]; 
	                    count = 1;
	                }
	                j++;
	            }
	            str = s;
	        }
	        return str;
	    }

### 53. 最大子序和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:
	
	输入: [-2,1,-3,4,-1,2,1,-5,4],
	输出: 6
	解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

进阶:

如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

__思路：__ 这是一道动态规划的问题。边界条件是数组遍历结束，最有子结构是如果前面的序列小于零，则对于最大和不会起到作用，所以直接加0，如果前面的序列最大和大于0，则表示前面的序列对最大和会起作用，将前面序列的最大和加到当前序列的最大和。

代码：

	 int maxSubArray(vector<int>& nums) {
	        int n = nums.size();
	        int * dp = new int[n];
	        dp[0] = nums[0];
	        int m = nums[0];
	        for (int i = 1; i < n; i++)
	        {
	            dp[i] = nums[i] + (dp[i-1] > 0 ?  dp[i-1] : 0);
	            m = max(m, dp[i]);
	        }
	        return m;
	    }

### 58. 最后一个单词的长度

给定一个仅包含大小写字母和空格 ' ' 的字符串，返回其最后一个单词的长度。

如果不存在最后一个单词，请返回 0 。

说明：一个单词是指由字母组成，但不包含任何空格的字符串。

示例:

	输入: "Hello World"
	输出: 5

__思路：__如果字符串的末尾有空格，则将字符串的长度减去空格的数量。然后但遇到最后一个字符串前面的空格就结束，并记录该空格的位置。最后，将字符串长度减去空格的位置移动，就得到最后一个单词的长度。

代码：

    int lengthOfLastWord(string s) {
        int len = s.size();
        if (len == 0 || (len == 1 && s[0] == ' '))
            return 0;
        int i;
        while (s[len-1] == ' ' )
        {
            len--;
        }
        for (i = len - 1;  i >= 0 ; i--)
        {
            
            if (s[i] == ' ')
            {
                break;
            }
        }
        return len-i-1;
    }

### 66. 加一

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储一个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

示例 1:

	输入: [1,2,3]
	输出: [1,2,4]
	解释: 输入数组表示数字 123。
示例 2:
	
	输入: [4,3,2,1]
	输出: [4,3,2,2]
	解释: 输入数组表示数字 4321。

__思路：__ 将最后一个数字加1，并除以10，如果等于0，表示不需要进位，如果等于1，表示需要进位。如果到达数组中的第一个数字，并且有进位，则需要在数组之前添加一个1。

其实这样做有些复杂，可以直接判断数组中的元素是否为9，如果为9表示需要进位，如果不为9则不需要进位。在第一位时，并且该元素为9，则需要在数组之前添加一个1。

代码：

    vector<int> plusOne(vector<int>& digits) {
        int i = digits.size()-1;
        int temp = 1;
        while (i >= 0)
        {
            temp = (digits[i]+temp) / 10;
            if (temp == 0)
            {
                digits[i] = (digits[i]+1) % 10;
                break;
            }
            else
            {
                digits[i] = (digits[i]+temp) % 10;
                if (i == 0 && temp == 1 )
                    digits.insert(digits.begin(), 1);
            }
            i--;
        }
        return digits;
    }

### 67. 二进制求和

给定两个二进制字符串，返回他们的和（用二进制表示）。

输入为非空字符串且只包含数字 1 和 0。

示例 1:

	输入: a = "11", b = "1"
	输出: "100"
示例 2:

	输入: a = "1010", b = "1011"
	输出: "10101"

__思路：__ 首先，判断两个字符串的长度，将最大的字符串放入a。然后执行最大字符串长度次数的循环，如果两个元素加起来等于2，则需要进位，如果不等式2，则不需要进位。

注意，字符串需要转换为整数，即减去48。

代码：

    string addBinary(string a, string b) {
        string temp;
        if (a.size() < b.size())
        {
            temp = a;
            a = b;
            b = temp;
        }
        int i = a.size()-1;
        int j = b.size() - 1;
        bool t = 0;
        char k;
        while (i >= 0)
        {
            if (j >=  0)
            {
                k = a[i];
                a[i] = ((a[i] - 48 + b[j]  -48+ t) % 2) + 48;
                t = (k - 48 + b[j] - 48 + t) / 2;
                j--;
            }
            else
            {
                k= a[i];
                a[i] = (a[i] - 48 + t) % 2 + 48;
                t = (k -48+ t) / 2;
            }
            if (i == 0 && t == 1)
            {
                a.insert(a.begin(), '1');
            }
            i--;
        }
        return a;
    }


## 第二周

### 69. x的平方根

实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

示例 1:
	
	输入: 4
	输出: 2
示例 2:
	
	输入: 8
	输出: 2
	说明: 8 的平方根是 2.82842..., 
	     由于返回类型是整数，小数部分将被舍去。


__直观的方法：__

思路：从1开始一个一个尝试，看是否满足平方根的条件。需要注意的是，不能用乘法，只能用除法，乘法会超过int的表示范围。这样做速度太慢。

代码：
	
	int mySqrt(int x) {
	        if (x <= 0)
	            return x;
	        int i = 1;
	        while (i <= x)
	        {
	            if (i <= x/i && x/(i+1) < (i+1))
	                break;
	            i++;
	        }
	        return i;
	        
	    }

__牛顿迭代法：__


思路： 找到一个数r，使得`r^2-n=0`。即求解该方程。然后利用牛顿迭代法求解该方程。迭代公式：

	x=(x+n/x)/2;

牛顿迭代的具体公式：

![](https://i.imgur.com/9nP8tQc.png)

图曲线图：

![](https://i.imgur.com/ZNaZYaP.png)

一直逼近点x*。


代码：

	int mySqrt(int x) {
	        if (x <= 0)
	            return x;
	        long r = x;
	        while (r > x / r)
	            r = (r + x/r) / 2;
	        return r;
	    }



###

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：
	
	输入： 2
	输出： 2
	解释： 有两种方法可以爬到楼顶。
	1.  1 阶 + 1 阶
	2.  2 阶
示例 2：

	输入： 3
	输出： 3
	解释： 有三种方法可以爬到楼顶。
	1.  1 阶 + 1 阶 + 1 阶
	2.  1 阶 + 2 阶
	3.  2 阶 + 1 阶

__思路：__这是一个动态规划问题(DP)，边界条件是，只有1阶时，dp[1] = 1; 只有2阶时，dp[2] = 2。最优子结构：向前走一步和或向前走两步，然后将两种情况的相加。

	dp[i] = dp[i-1] + dp[i-2];

可以使用递归的形式实现。

代码：
	  int climbStairs(int n) {
	        if (n == 1)
	            return 1;
	    
	        /*if (n == 1)
	            return 1;
	        if (n == 2)
	            return 2;
	        int a = climbStairs(n-1) + climbStairs(n-2);
	        return a;*/
	        if (n == 1)
	            return 1;
	        int * dp = new int[n+1];
	        dp[1] = 1;
	        dp[2] = 2;
	        for (int i = 3; i <= n; i++)
	        {
	            dp[i] = dp[i-1] + dp[i-2];
	        }
	        return dp[n];
	    }


### 83. 删除排序列表中的重复元素

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

示例 1:

	输入: 1->1->2
	输出: 1->2
示例 2:

	输入: 1->1->2->3->3
	输出: 1->2->3

__思路：__直接前后两个元素比较，如果相等，则删除第二元素。

代码：

    ListNode* deleteDuplicates(ListNode* head) {
        map<int, int> map_i_i;
        ListNode * temp = head;
        ListNode * p = NULL;
        if (temp != NULL && temp->next != NULL)
            p = temp->next;
        while (temp != NULL &&  p != NULL)
        {
            
            if (temp->val == p->val)
            {
                temp->next = p->next;
                ListNode * q = p;
                p =  p->next;
                delete q;
            }
            else
            {
                temp = p;
                p = p->next;
            }
        }
        return head;
	}


### 88. 合并两个有序数组

给定两个有序整数数组 nums1 和 nums2，将 nums2 合并到 nums1 中，使得 num1 成为一个有序数组。

说明:

初始化 nums1 和 nums2 的元素数量分别为 m 和 n。
你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
示例:

输入:
	nums1 = [1,2,3,0,0,0], m = 3
	nums2 = [2,5,6],       n = 3
	
	输出: [1,2,2,3,5,6]

__思路：__新创建一个数组，然后另一个数组之间元素相比，小的元素放入新数组。


代码：

    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i = 0, j = 0, k = 0;
        vector<int> temp(n+m, 0);
        while (i < m || j < n)
        {
            if (i >= m)
                temp[k++] = nums2[j++];
            else if (j >= n)
                temp[k++] = nums1[i++];
            else if (nums1[i] >= nums2[j])
                temp[k++] = nums2[j++];
            else
                temp[k++] = nums1[i++];
        }
        i = 0;
        k = 0;
        while(i < m+n)
            nums1[i++] = temp[k++];
    }


### 1. 两数之和


给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:
	
	给定 nums = [2, 7, 11, 15], target = 9
	
	因为 nums[0] + nums[1] = 2 + 7 = 9
	所以返回 [0, 1]

__思路：__ 创建一个字典，将所有元素当作key，其对应的下标当作value，添加的字典map中。然后用target减去每一个元素，判断得到的差在不在map中，如果在map，这返回该值的下标和map中对应差的value。

代码：

    vector<int> twoSum(vector<int>& nums, int target) {
        map<int, int> map_i_i;
        for (int i = 0; i < nums.size(); i++)
            map_i_i[nums[i]] = i;
        for (int i = 0; i < nums.size(); i++)
        {
            if (map_i_i.find(target-nums[i]) != map_i_i.end() && i != map_i_i[target-nums[i]])
                return {i, map_i_i[target-nums[i]]};
        }
        return {};
    }

### 26. 删除排序数组中的重复项


给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

示例 1:

	给定数组 nums = [1,1,2], 
	
	函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
	
	你不需要考虑数组中超出新长度后面的元素。
示例 2:
	
	给定 nums = [0,0,1,1,1,2,2,3,3,4],
	
	函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
	
	你不需要考虑数组中超出新长度后面的元素。
说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

	// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
	int len = removeDuplicates(nums);
	
	// 在函数里修改输入数组对于调用者是可见的。
	// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
	for (int i = 0; i < len; i++) {
	    print(nums[i]);
	}

思路: 定义一个指针从0开始，i从1开始，然后两个元素相等，res不变，i向前走一个；如果两个元素相等，则将nums[i]赋给nums[res++]，相同将i和res向前走一个。



代码：

	int removeDuplicates(vector<int>& nums) {
	        int res = 0;
	        if (nums.size() == 0)
	            return 0;
	        for (int i = 1; i < nums.size(); i++)
	        {
	            if (nums[res] != nums[i])
	            {
	                nums[++res] = nums[i];
	            }
	     
	        
	        }
	        return res+1;
	    }



### 27. 移除重复元素


给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例 1:
	
	给定 nums = [3,2,2,3], val = 3,
	
	函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。
	
	你不需要考虑数组中超出新长度后面的元素。
示例 2:
	
	给定 nums = [0,1,2,2,3,0,4,2], val = 2,
	
	函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。
	
	注意这五个元素可为任意顺序。
	
	你不需要考虑数组中超出新长度后面的元素。
说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:
	
	// nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
	int len = removeElement(nums, val);
	
	// 在函数里修改输入数组对于调用者是可见的。
	// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
	for (int i = 0; i < len; i++) {
	    print(nums[i]);
	}


思路：定义一个res指针，指向需要被替换的元素。如果i指向的元素相等val，则res不变，i向下移动一个；如果i指向元素不等于val，则将i指向的元素赋给res位置。

代码：

    int removeElement(vector<int>& nums, int val) {
        int i;
        if (nums.size() == 0)
            return 0;
        int res = 0;
        for(i = 0; i < nums.size(); i++)
        {
            if (nums[i] != val)
                nums[res++] = nums[i];
        }
        return res;
    }


### 35. 搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

示例 1:
	
	输入: [1,3,5,6], 5
	输出: 2
示例 2:

	输入: [1,3,5,6], 2
	输出: 1
示例 3:
	
	输入: [1,3,5,6], 7
	输出: 4
示例 4:
	
	输入: [1,3,5,6], 0
	输出: 0

思路：如果目标元素小于等于数组中的元素，则该元素对应的下标就是i所在值或所需插入的位置。如果目标值比数组中最大元素还有大，则返回数组的长度。

代码：

	 int searchInsert(vector<int>& nums, int target) {
	        int i;
	        for (i = 0; i < nums.size(); i++)
	            if (nums[i] >= target)
	                return i;
	        return i;
	    }


### 118. 杨辉三角

给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。

![](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

在杨辉三角中，每个数是它左上方和右上方的数的和。

示例:

	输入: 5
	输出:
	[
	     [1],
	    [1,1],
	   [1,2,1],
	  [1,3,3,1],
	 [1,4,6,4,1]
	]

思路: 略

代码：
	
	vector<vector<int>> generate(int numRows) {
	        vector<vector<int>>  ans(numRows);
	        for (int i = 0; i < numRows; i++)
	        {
	            ans[i] = vector<int>(i+1);
	            ans[i][0] = ans[i][i] = 1;
	            for (int j = 1; j < i; j++)
	            {
	                ans[i][j] = ans[i-1][j-1] + ans[i-1][j];
	            }
	            
	        }
	        return ans;
	    }


### 119. 杨辉三角Ⅱ

给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行。

![](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

在杨辉三角中，每个数是它左上方和右上方的数的和。

示例:
	
	输入: 3
	输出: [1,3,3,1]
	进阶：

你可以优化你的算法到 O(k) 空间复杂度吗？

最简单的做法，但是空间复杂度比较高，无法满足o(k)。

思路：略

代码：

	vector<int> getRow(int rowIndex) {
	        vector<vector<int>>  ans(rowIndex+1);
		        for (int i = 0; i <= rowIndex; i++)
		        {
		            ans[i] = vector<int>(i+1);
		            ans[i][0] = ans[i][i] = 1;
		            for (int j = 1; j < i; j++)
		            {
		                ans[i][j] = ans[i-1][j-1] + ans[i-1][j];
		            }
		            
		        }
		        return ans[rowIndex];
	    }

考虑复杂度

思路：利用杨辉三角的推导公式。每一项等于该项所在的行号减1的阶乘除以列号减1的阶乘和行号减列号的阶乘之积。

但是由于阶乘得到的数非常大，甚至超过了long long类型。在求每项的元素，无法每次求出阶乘。可以通过下面的迭代公式求得：
 
直接使用组合公式C(n,i) = n!/(i!*(n-i)!), 则第(i+1)项是第i项的倍数=(n-i+1)/i;

注意行号n从0开始，列号i从0开始。

代码：

	 vector<int> getRow(int rowIndex) {
	        vector<int>  ans(rowIndex+1);
		    ans[0] = ans[rowIndex] = 1;
	        long temp = 1;
	        for (int i = 1; i <= (rowIndex+1)/2; i++)
	        {
	           temp = temp * (rowIndex-i+1) / i;
	           ans[i] = ans[rowIndex-i] = temp;
	        }
	        return ans;
	    }

### 121. 买卖股票的最佳时机 Ⅰ

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

示例 1:

	输入: [7,1,5,3,6,4]
	输出: 5
	解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
	     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
示例 2:

	输入: [7,6,4,3,1]
	输出: 0
	解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

思路： 这一个求山的峰谷的问题，即求出最低谷到最高峰的差值。首先，将最大利润设置为0，最低谷设置为第一个元素的值。然后每次循环，判断当前元素是否为低谷，最后，将每个元素都与低谷做差，并取最大的差值。

	int maxProfit(vector<int>& prices) {
	        if (prices.size() <= 1)
	            return 0;
	        int minprices = prices[0];  
	        int maxprofit = 0;
	        for (int i = 1; i < prices.size(); i++)
	        {
	            
	            minprices = min(minprices, prices[i]);
	            int profit =  prices[i] - minprices;
	            maxprofit = max(profit, maxprofit);
	        }
	        return maxprofit;
	    }

### 122. 买卖股票的最佳时机Ⅱ

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例 1:
	
	输入: [7,1,5,3,6,4]
	输出: 7
	解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
	     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
示例 2:
	
	输入: [1,2,3,4,5]
	输出: 4
	解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
	     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
	     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
示例 3:

	输入: [7,6,4,3,1]
	输出: 0
	解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

思路：这也是一个山的峰谷的问题，不过这是求出所有的低谷到山峰差值的和。而上一个题目是求最大的山谷。每过了一个山峰，就将最大利润和最低谷重置，计算下一个峰。

![](https://pic.leetcode-cn.com/d447f96d20d1cfded20a5d08993b3658ed08e295ecc9aea300ad5e3f4466e0fe-file_1555699515174)

代码：

	int maxProfit(vector<int>& prices) {
        if (prices.size() <= 1)
            return 0;
        int maxprofit = 0;
        int sum = 0;
        int minprice = prices[0];
        for (int i = 1; i < prices.size(); i++)
        {
            minprice = min(minprice, prices[i]);
            maxprofit = max(prices[i] - minprice, maxprofit);
            if (i < prices.size()-1 && prices[i+1] < prices[i])
            {
                sum += maxprofit;
                maxprofit = 0;
                minprice = prices[i+1];
            }
        }
        sum += maxprofit;
        return sum;
    }

### 167. 两数之和Ⅱ-输入有序数组

给定一个已按照升序排列 的有序数组，找到两个数使得它们相加之和等于目标数。

函数应该返回这两个下标值 index1 和 index2，其中 index1 必须小于 index2。

说明:

- 返回的下标值（index1 和 index2）不是从零开始的。
- 你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。

示例:
	
	输入: numbers = [2, 7, 11, 15], target = 9
	输出: [1,2]
	解释: 2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

思路： 两个指针法。一个指针指i向最开头的元素，另一个指针j指向最末尾的元素。如果两个元素的和等于target，这将这两个值的下标放入数组中。如果最末尾的元素大于target或者两个两个元素的值加起来大于target，则j向前移动；如果两个元素的值加起来小于target，则指针i先后移动。


代码：


	vector<int> twoSum(vector<int>& numbers, int target) {
        if (numbers.size() <= 1)
            return {};
        vector<int> res;
        //双指针
        int i = 0; 
        int j = numbers.size()-1;
        while(i < j)
        {
            if (numbers[i] + numbers[j] == target)
            {
                res.push_back(i+1);
                res.push_back(j+1);
                break;
            }
            if(numbers[j] > target || numbers[i] + numbers[j] > target)
                j--;
            if (numbers[i] + numbers[j] < target)
                i++;
        }
        return res;
    }

暴力解法：

	vector<int> twoSum(vector<int>& numbers, int target) {
        if (numbers.size() <= 1)
            return {};
        vector<int> res;
        //暴力解法
        for (int i = 0; i < numbers.size()-1 && numbers[i] <= target; i++)
        {
            auto t = find(numbers.begin()+i+1, numbers.end(), target - numbers[i]);
            if ( t != numbers.end())
            {
                res.push_back(i+1);
                res.push_back(distance(numbers.begin(), t)+1);
                break;
            }
        }
        return res;
    }

### 169. 求众数

给定一个大小为 n 的数组，找到其中的众数。众数是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

示例 1:

	输入: [3,2,3]
	输出: 3
示例 2:

	输入: [2,2,1,1,1,2,2]
	输出: 2

方法一：字典计数法


思路：使用一个字典来记录每个元素出现的次数，key为对应的元素，value为key出现的次数。然后找出value最大所对应的key。

代码：

	int majorityElement(vector<int>& nums) {
        map<int, int> map_i_i;
        for (int i = 0; i < nums.size(); i++)
        {
            if (map_i_i.find(nums[i]) != map_i_i.end())
                map_i_i[nums[i]]++;
            else
                map_i_i[nums[i]] = 1;
        }
        int m = 0;
        int temp = 0;
        for (auto ite = map_i_i.begin(); ite != map_i_i.end(); ite++)
        {
            if (ite->second >= m)
            {
                temp = ite->first;
                m = ite->second;
            }
            
        }
        return temp;


方法二：

用一个计数器进行计数。从第一个数开始count=1，遇到相同的就加1，遇到不同的就减1，减到0就重新换个数开始计数，总能找到最多的那个
	
	int majorityElement(vector<int>& nums) {
	        int count = 1;
	        int temp = nums[0];
	        for (int i = 1; i < nums.size(); i++)
	        {
	            if (nums[i] == temp)
	                count++;
	            else
	            {
	                count--;
	                if (count == 0)
	                    temp = nums[i+1];
	            }
	        }
	        return temp;
	    }


方法三：

先对数组排序，然后取数组的中间元素。

	int majorityElement(vector<int>& nums) {
	        sort(nums.begin(), nums.end());
	        return nums[nums.size()/2];
	    }

方法二的效率最高。


### 189. 旋转数组

给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

示例 1:

	输入: [1,2,3,4,5,6,7] 和 k = 3
	输出: [5,6,7,1,2,3,4]
	解释:
	向右旋转 1 步: [7,1,2,3,4,5,6]
	向右旋转 2 步: [6,7,1,2,3,4,5]
	向右旋转 3 步: [5,6,7,1,2,3,4]
示例 2:
	
	输入: [-1,-100,3,99] 和 k = 2
	输出: [3,99,-1,-100]
	解释: 
	向右旋转 1 步: [99,-1,-100,3]
	向右旋转 2 步: [3,99,-1,-100]
说明:

尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
要求使用空间复杂度为 O(1) 的原地算法。

思路：将数组旋转三次就可以得到想要的结果。首先，对整体数组旋转一次，其次，对前k个元素旋转一次，最后，对后n-k个元素旋转一次。

代码：

	void rotate(vector<int>& nums, int k) {
	        if (nums.size() == 1)
	            return;
	        k = k % nums.size();
	        reverse(nums, 0, nums.size()-1);
	        reverse(nums, 0, k-1);
	        reverse(nums, k, nums.size()-1);
	    }
	  
	    
	    void reverse(vector<int> & nums, int a, int b)
	    {
	        int t = 1;
	        while (a < b)
	        {
	            int temp = nums[a];
	            nums[a] = nums[b];
	            nums[b] = temp;
	            a++;
	            b--;
	        }
	    }


### 217. 存在重复元素

给定一个整数数组，判断是否存在重复元素。

如果任何值在数组中出现至少两次，函数返回 true。如果数组中每个元素都不相同，则返回 false。

示例 1:
	
	输入: [1,2,3,1]
	输出: true
示例 2:
	
	输入: [1,2,3,4]
	输出: false
示例 3:

	输入: [1,1,1,3,3,4,3,2,4,2]
	输出: true

思路：首先对数组进行排序，然后判断是否存在前后两个元素相等的情况。


代码：

	 bool containsDuplicate(vector<int>& nums) {
	        if (nums.size() == 0)
	            return false;
	        sort(nums.begin(), nums.end());
	        for (int i = 1; i < nums.size(); i++)
	        {
	            if (nums[i] == nums[i-1])
	                return true;
	        }
	        return false;
	    }


方法二：利用set的特性，不允许重复的key，判断set和vector的大小即可。

代码：

	bool containsDuplicate(vector<int>& nums) {
	        if (nums.size() == 0)
	            return false;
	        set<int> set_i(nums.begin(), nums.end());
	        return nums.size() > set_i.size();   
	    }

### 219. 存在重复元素Ⅱ

给定一个整数数组和一个整数 k，判断数组中是否存在两个不同的索引 i 和 j，使得 nums [i] = nums [j]，并且 i 和 j 的差的绝对值最大为 k。

示例 1:

	输入: nums = [1,2,3,1], k = 3
	输出: true
示例 2:
	
	输入: nums = [1,0,1,1], k = 1
	输出: true
示例 3:
	
	输入: nums = [1,2,3,1,2,3], k = 2
	输出: false

思路：创建一个map存储相同值的数量，如果map中存储对应元素，则判断个数是否已经大于k，如果不大于k，则将map的value加1；如果map中没有对应元素，则插入一个pair。


代码：

	bool containsNearbyDuplicate(vector<int>& nums, int k) 	{
        if (nums.size() <= 1)
            return false;
        map<int, int> map_i_i;
        for (int i = 0; i < nums.size(); i++)
        {
            if (map_i_i.count(nums[i]))
            {
                if (i - map_i_i[nums[i]] <= k)
                    return true;
                else
                    map_i_i[nums[i]] = i;
            }
            else
                map_i_i.insert(pair<int, int>(nums[i], i));
        }
        return false;
    }


### 268. 缺失数字

给定一个包含 0, 1, 2, ..., n 中 n 个数的序列，找出 0 .. n 中没有出现在序列中的那个数。

示例 1:
	
	输入: [3,0,1]
	输出: 2
示例 2:
	
	输入: [9,6,4,2,3,5,7,0,1]
	输出: 8

说明:

你的算法应具有线性时间复杂度。你能否仅使用额外常数空间来实现?

思路： 求和法： 将1到元素个数+1中间所有元素的和减去数组中所有元素，得到的结果就是缺失的元素。


代码：

 	int missingNumber(vector<int>& nums) {
       	int len = nums.size();
        int sum = len * (len + 1) / 2;
        for (int i = 0; i < len; i++)
            sum -= nums[i];
        int sum = 0;
        for (int i = 0; i < nums.size(); i++ )
            sum = sum^(i+1)^(nums[i]);
        return sum;
    }


异或法：间每个元素和1~n异或，最后得到的结果就是确实的元素

	int missingNumber(vector<int>& nums) {
       	int len = nums.size();
        int sum = 0;
        for (int i = 0; i < nums.size(); i++ )
            sum = sum^(i+1)^(nums[i]);
        return sum;
    }


### 283. 移动零

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:
	
	输入: [0,1,0,3,12]
	输出: [1,3,12,0,0]

说明:

- 必须在原数组上操作，不能拷贝额外的数组。
- 尽量减少操作次数。

思路： 双指针法：定义一个指针记录需要覆盖的元素，最后将末尾的元素全部赋值为0。

可以先把所有非0的元素移到前面，然后将后面的位置补0。 使用指针i，指向需要插入的下标，使用指针j指向遍历的下标。遍历一遍，如果j指向的位置为0，则i不变，j++后移；如果j指向的位置不为0，则将j位置的元素值赋值到i位置，然后i++。

代码：


	void moveZeroes(vector<int>& nums) {
        int res = 0;
        for (int i = 0; i < nums.size(); i++)
        {
            if (nums[i] != 0)
            {
                nums[res] = nums[i];
                res++;
            }
        }
        for (int j = res; j < nums.size(); j++)
            nums[j] = 0;
    }


### 414. 第三大的数

给定一个非空数组，返回此数组中第三大的数。如果不存在，则返回数组中最大的数。要求算法时间复杂度必须是O(n)。

示例 1:

	输入: [3, 2, 1]
	
	输出: 1
	
	解释: 第三大的数是 1.
示例 2:

	输入: [1, 2]
	
	输出: 2
	
	解释: 第三大的数不存在, 所以返回最大的数 2 .
示例 3:

	输入: [2, 2, 3, 1]
	
	输出: 1
	
	解释: 注意，要求返回第三大的数，是指第三大且唯一出现的数。
	存在两个值为2的数，它们都排第二。


思路：首先对元素进行排序（从小到大），设置一个count，遇到两个不相等的，就减1，当count等于0时，返回当前元素。如果没有就返回最大元素。


代码：

    int thirdMax(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int count = 2;
        for (int  i = nums.size()-1; i >= 1; i--)
        {
            if (nums[i] != nums[i-1])
            {
                count--;
            }
            if (count == 0)
            {
                return nums[i-1];
            }
        }
        return nums[nums.size()-1];
    }

思路： 利用set去重，而且set是自动排序的，从小到大排序，然后集合大小小于3，直接返回最大的元素，否则往后退两个元素。

代码：

	int thirdMax(vector<int>& nums) {
        //采用set去重
        set<int> set_i;
        for(int i = 0; i < nums.size(); i++)
            set_i.insert(nums[i]);
        set<int>::iterator ite = set_i.end();
        if  (set_i.size() < 3)  
        {
            ite--;
            return *ite;
        }
        else
        {
            ite--;
            ite--;
            ite--;
            return *ite;
        }
        
    }


### 448. 找到所有数组中消失的数字

给定一个范围在  1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。

找到所有在 [1, n] 范围之间没有出现在数组中的数字。

您能在不使用额外空间且时间复杂度为O(n)的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。

示例:
	
	输入:
	[4,3,2,7,8,2,3,1]
	
	输出:
	[5,6]

思路：原理是遍历数组中所有元素，将元素值 - 1对应下标处的值取对应的负数作为标记，最后数组中依然大于0的即为未出现的元素



代码：

	vector<int> findDisappearedNumbers(vector<int>& nums) {
        vector<int> res;
        for (int i = 0; i < nums.size(); i++)
            nums[abs(nums[i])-1] = -abs(nums[abs(nums[i])-1]);
        for (int i = 0; i < nums.size(); i++)
            if (nums[i] > 0)
                res.push_back(i+1);
        return res;
    }


### 485. 最大连续的个数

给定一个二进制数组， 计算其中最大连续1的个数。

示例 1:

	输入: [1,1,0,1,1,1]
	输出: 3
	解释: 开头的两位和最后的三位都是连续1，所以最大连续1的个数是 3.
注意：

- 输入的数组只包含 0 和1。
- 输入数组的长度是正整数，且不超过 10,000。

思路： count记录连续1的个数，如果遇到0，就将count重置为0，每次取最大的count。

代码：

	int findMaxConsecutiveOnes(vector<int>& nums) {
        int count = 0;
        int m = 0;
        for (int i = 0; i < nums.size(); i++)
        {
            if (nums[i] == 1)
                count++;
            else
                count = 0;
            m = max(m, count);
        }
        return m;
    }

### 532. 数组中的K-diff数对

给定一个整数数组和一个整数 k, 你需要在数组里找到不同的 k-diff 数对。这里将 k-diff 数对定义为一个整数对 (i, j), 其中 i 和 j 都是数组中的数字，且两数之差的绝对值是 k.

示例 1:
	
	输入: [3, 1, 4, 1, 5], k = 2
	输出: 2
	解释: 数组中有两个 2-diff 数对, (1, 3) 和 (3, 5)。
	尽管数组中有两个1，但我们只应返回不同的数对的数量。
示例 2:

	输入:[1, 2, 3, 4, 5], k = 1
	输出: 4
	解释: 数组中有四个 1-diff 数对, (1, 2), (2, 3), (3, 4) 和 (4, 5)。
示例 3:

	输入: [1, 3, 1, 5, 4], k = 0
	输出: 1
	解释: 数组中只有一个 0-diff 数对，(1, 1)。
注意:

- 数对 (i, j) 和数对 (j, i) 被算作同一数对。
- 数组的长度不超过10,000。
- 所有输入的整数的范围在 [-1e7, 1e7]。

思路：首先对于k=0的情况单独分析，直接找相同的元素。对于其他情况，将元素放入一个集合中进行去重，然后判断每个元素+k是否在集合中。

代码：

	int findPairs(vector<int>& nums, int k) {
        map<int, int> map_i_i;
        int count = 0;
        if (k < 0)
            return 0;
        if (k == 0)
        {
            for (int i = 0; i < nums.size(); i++)
            {
                if (map_i_i.find(nums[i]) != map_i_i.end())
                    map_i_i[nums[i]]++;
                else
                    map_i_i[nums[i]] = 1;
                if (map_i_i[nums[i]] == 2)
                    count++;
            }
            return count;
        }
        set<int> set_i(nums.begin(), nums.end());
        auto ite = set_i.begin();
        while (ite != set_i.end())
        {
            if (set_i.find(*ite+k) != set_i.end())
                count ++;
            ite++;
        }
        return count;
    }

### 561. 数组拆分Ⅰ

给定长度为 2n 的数组, 你的任务是将这些数分成 n 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从1 到 n 的 min(ai, bi) 总和最大。

示例 1:
	
	输入: [1,4,3,2]
	
	输出: 4
	解释: n 等于 2, 最大总和为 4 = min(1, 2) + min(3, 4).
提示:

- n 是正整数,范围在 [1, 10000].
- 数组中的元素范围在 [-10000, 10000].

思路：首先对数组进行排序，然后取一个元素，隔一个元素再去下一个元素。

代码：

    int arrayPairSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int sum = 0;
        for (int i = 0; i < nums.size(); i+=2)
        {
            sum += nums[i];
        }
        return sum;
    }

### 566. 重塑矩阵

在MATLAB中，有一个非常有用的函数 reshape，它可以将一个矩阵重塑为另一个大小不同的新矩阵，但保留其原始数据。

给出一个由二维数组表示的矩阵，以及两个正整数r和c，分别表示想要的重构的矩阵的行数和列数。

重构后的矩阵需要将原始矩阵的所有元素以相同的行遍历顺序填充。

如果具有给定参数的reshape操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。

示例 1:

	输入: 
	nums = 
	[[1,2],
	 [3,4]]
	r = 1, c = 4
	输出: 
	[[1,2,3,4]]
	解释:
	行遍历nums的结果是 [1,2,3,4]。新的矩阵是 1 * 4 矩阵, 用之前的元素值一行一行填充新矩阵。
示例 2:

	输入: 
	nums = 
	[[1,2],
	 [3,4]]
	r = 2, c = 4
	输出: 
	[[1,2],
	 [3,4]]
	解释:
	没有办法将 2 * 2 矩阵转化为 2 * 4 矩阵。 所以输出原矩阵。
注意：

- 给定矩阵的宽和高范围在 [1, 100]。
- 给定的 r 和 c 都是正数。

思路：首先判断重塑矩阵的大小是否能够容纳原始矩阵的所有元素。然后，将原始矩阵的所有元素看成是一个1×n的矩阵，并将元素依次放入重塑矩阵中。

这里重点掌握二维矩阵的创建和使用。

	vector<vector<int>> ans(r);
	for (auto i : r)
		ans[i] = vector<int>(c);

代码：
	
	vector<vector<int>> matrixReshape(vector<vector<int>>& nums, int r, int c) {
        int row = nums.size();
        int column = nums[0].size();
        if (r * c >  row * column)
            return nums;
        int i = 0;
        vector<vector<int> > ans(row*column/c); 
        while (i < row * column)
        {
            if (i%c == 0)
                ans[i/c] = vector<int>(c);
            ans[i/c][i%c] = nums[i/column][i%column];
            i++;
        }
        return ans;
    }

### 581. 最短无序连续子数组

给定一个整数数组，你需要寻找一个连续的子数组，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。

你找到的子数组应是最短的，请输出它的长度。

示例 1:

	输入: [2, 6, 4, 8, 10, 9, 15]
	输出: 5
	解释: 你只需要对 [6, 4, 8, 10, 9] 进行升序排序，那么整个表都会变为升序排序。

说明 :

- 输入的数组长度范围在 [1, 10,000]。
- 输入的数组可能包含重复元素 ，所以升序的意思是<=。

思路：首先对元素进行排序，然后两个指针分别从最开头和最末尾开始，判断是否等于排序好的元素，如果不相等就停止，最后，如果两个指针相等，则答案是0，否则答案就是两个指针之查减一。

代码：

	int findUnsortedSubarray(vector<int>& nums) {
        int i = 0;
        int j = nums.size()-1;
        vector<int>  temp(nums.begin(), nums.end());
        sort(temp.begin(), temp.end());
        while(i < j)
        {
            if (temp[i] != nums[i])
                break;
            i++;
        }
        while (j > i)
        {
            if (temp[j] != nums[j])
                break;
            j--;
        }
        return i == j ? 0 : j - i + 1;
    }


### 605. 种花问题

假设你有一个很长的花坛，一部分地块种植了花，另一部分却没有。可是，花卉不能种植在相邻的地块上，它们会争夺水源，两者都会死去。

给定一个花坛（表示为一个数组包含0和1，其中0表示没种植花，1表示种植了花），和一个数 n 。能否在不打破种植规则的情况下种入 n 朵花？能则返回True，不能则返回False。

示例 1:
	
	输入: flowerbed = [1,0,0,0,1], n = 1
	输出: True
示例 2:
	
	输入: flowerbed = [1,0,0,0,1], n = 2
	输出: False
注意:

- 数组内已种好的花不会违反种植规则。
- 输入的数组长度范围为 [1, 20000]。
- n 是非负整数，且不会超过输入数组的大小。

方法一：

思路：记录0的个数，len个连续0可以放入(len-1)/2个1，最后还需要考虑末尾的时候，两个零就可以放入一个1。

代码：

	bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        int len = 1, ans = 0;
        for(auto i : flowerbed)
        {
            if (i)
            {
                ans += (len-1)/2;
                len = 0;
            }
            else
                len++;
        }
        ans += len/2;
        return ans >= n;
    }

方法二：

思路：判断当前位置是否为0，以及前一个位置是否为0或者当前处于第一个元素，并且后一个位置为0或者当前处于最后一个元素，如果当前可以放入一个1，则直接向后跳两步，否则跳一步。

代码：

	bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        int i = 0;
        int len = flowerbed.size();
        while(i < len && n != 0)
        {
            if (flowerbed[i] == 0 && (i == 0 || flowerbed[i-1] == 0) && (i == len-1 || flowerbed[i+1] == 0))
            {
                n--;
                i+=2;
            }
            else
                i++;
        }
        return n == 0;
    }


### 628. 三个数的最大乘积


给定一个整型数组，在数组中找出由三个数组成的最大乘积，并输出这个乘积。

示例 1:

	输入: [1,2,3]
	输出: 6
示例 2:

	输入: [1,2,3,4]
	输出: 24
注意:

- 给定的整型数组长度范围是[3,10^4]，数组中所有的元素范围是[-1000, 1000]。
- 输入的数组中任意三个数的乘积不会超出32位有符号整数的范围。


思路：首先对数组进行排序，末尾的元素一定是需要三个数之一；然后判断是取前面两个元素还是后面两个元素：如果倒数第二元素小于零，则一定是取前面两个元素；如果第一个和第二元素小于零，并且两者的乘积大于倒数第二和第三两个元素之积，则一定是取前面两个元素。否则取倒数第二和第三个元素。

代码：

	int maximumProduct(vector<int>& nums) {
        if (nums.size() <= 2)
            return 0;
        sort(nums.begin(), nums.end());
        int ans = 1;
        ans *= nums[nums.size()-1];
        if (nums[nums.size()-2] < 0 || (nums[0] < 0 && nums[1] < 0 && abs(nums[0])*abs(nums[1]) > nums[nums.size()-2]*nums[nums.size()-3]) )
            ans *= nums[0]*nums[1];
        else
            ans *= nums[nums.size()-2]*nums[nums.size()-3];
        return ans;
    }


### 643 子数组最大平均数 I

给定 n 个整数，找出平均数最大且长度为 k 的连续子数组，并输出该最大平均数。

示例 1:
	
	输入: [1,12,-5,-6,50,3], k = 4
	输出: 12.75
	解释: 最大平均数 (12-5-6+50)/4 = 51/4 = 12.75
	 

注意:

- 1 <= k <= n <= 30,000。
- 所给数据范围 [-10,000，10,000]。

思路： 窗口平滑。首先计算k元素的和，然后从每次减去开头第i个元素的值，加上第k+i个元素的值，每次取最大和。

代码：

    double findMaxAverage(vector<int>& nums, int k) {
        int max = 0;
        for (int i = 0; i < k; i++)
            max += nums[i];
        int max1 = max;
        int max2 = 0;
        for (int i = 1; i < nums.size() - k + 1; i++)
        {
            max2 = max1 - nums[i-1] + nums[k+i-1];
            max = max > max2 ? max : max2;
            max1 = max2;
        }
        return (double)(max)/k;
    }


### 661. 图片平滑器

包含整数的二维矩阵 M 表示一个图片的灰度。你需要设计一个平滑器来让每一个单元的灰度成为平均灰度 (向下舍入) ，平均灰度的计算是周围的8个单元和它本身的值求平均，如果周围的单元格不足八个，则尽可能多的利用它们。

示例 1:

	输入:
	[[1,1,1],
	 [1,0,1],
	 [1,1,1]]
	输出:
	[[0, 0, 0],
	 [0, 0, 0],
	 [0, 0, 0]]
	解释:
	对于点 (0,0), (0,2), (2,0), (2,2): 平均(3/4) = 平均(0.75) = 0
	对于点 (0,1), (1,0), (1,2), (2,1): 平均(5/6) = 平均(0.83333333) = 0
	对于点 (1,1): 平均(8/9) = 平均(0.88888889) = 0

注意:

- 给定矩阵中的整数范围为 [0, 255]。
- 矩阵的长和宽的范围均为 [1, 150]。

思路: 考虑所有情况。如果位置为第一行，则只需要考虑数组所在的行和下一行；如果位置为最后一行，则只需要考虑数字所在的行和前一行。如果位置位于第一列，则只需要考虑数字所在的列和后一列；如果位置位于最后一列，则只需要考虑数字所在的列和前一列。



代码：

   vector<vector<int>> imageSmoother(vector<vector<int>>& M) {
        vector<vector<int> >  ans(M.size());
        int sum = 0;
        int count = 0;
        for(int i = 0; i < M.size(); i++)
        {
            ans[i] = vector<int>(M[0].size());
            for (int j = 0; j < M[0].size(); j++)
            {
                sum = 0;
                count = 0;
                //计算左侧一列
                if (j > 0)
                {
                    sum += M[i][j-1];
                    count++;
                    if (i > 0)
                    {
                        sum += M[i-1][j-1];
                        count++;
                    }
                    if (i < M.size()-1)
                    {
                        sum += M[i+1][j-1];
                        count++;
                    }
                }
                //计算中间一列
                sum += M[i][j];
                count++;
                if (i > 0)
                {
                    sum += M[i-1][j];
                    count++;
                }
                if (i < M.size()-1)
                {
                    sum += M[i+1][j];
                    count++;
                }
                //计算右侧一列
                if (j < M[0].size() - 1)
                {
                    sum += M[i][j+1];
                    count++;
                    if (i > 0)
                    {
                        sum += M[i-1][j+1];
                        count++;    
                    }
                    if (i < M.size() - 1)
                    {
                        sum += M[i+1][j+1];
                        count++;
                    }
                }
                ans[i][j] = sum / count;
            }
        }
        return ans;
        
    }

### 665. 非递减数列

给定一个长度为 n 的整数数组，你的任务是判断在最多改变 1 个元素的情况下，该数组能否变成一个非递减数列。

我们是这样定义一个非递减数列的： 对于数组中所有的 i (1 <= i < n)，满足 array[i] <= array[i + 1]。

示例 1:
	
	输入: [4,2,3]
	输出: True
	解释: 你可以通过把第一个4变成1来使得它成为一个非递减数列。
示例 2:
	
	输入: [4,2,1]
	输出: False
	解释: 你不能在只改变一个元素的情况下将其变为非递减数列。

说明:  n 的范围为 [1, 10,000]。

思路：    

	   如果出现 a[i] > a[i+1]   改变一个数 就面临两种选择
	   1. 把a[i]变大
	   2. 把a[i+1] 变小
	   这两种选择其实是有依据的 需要比较a[i-1] 与 a[i+1]的值
	   eg.  ... 1 4 3 ...   只能选择把4变小   ... 3 4 1 ... 只能选择把1变大
	   改变完之后，记录改变次数，再检测是否升序
	   如果次数大于1，至少改了两次 返回false
	    
	   先让前两个有序
	   因为没有左边没有数 所以对于前两个数来说，最佳选择就是吧 a[0] 变小

代码：

    bool checkPossibility(vector<int>& nums) {
        // 数组个数小于2个 一定可以
        if (nums.size() < 3)
            return true;
        int count = 0;
        if (nums[0] > nums[1])
        {
            count++;
            nums[0] = nums[1];
        }
        for (int i = 1; i < nums.size()-1;  i++)
        {
            int right = nums[i+1];
            if (nums[i] > right)
            {
                count++;
                if (count > 1)
                    return false;
                int left = nums[i-1];
                if (left > right)
                    nums[i+1] = nums[i];
                else
                    nums[i] = left;
            }
        }
        return true;
    }


### 746. 使用最小花费爬楼梯

数组的每个索引做为一个阶梯，第 i个阶梯对应着一个非负数的体力花费值 cost[i](索引从0开始)。

每当你爬上一个阶梯你都要花费对应的体力花费值，然后你可以选择继续爬一个阶梯或者爬两个阶梯。

您需要找到达到楼层顶部的最低花费。在开始时，你可以选择从索引为 0 或 1 的元素作为初始阶梯。

示例 1:
	
	输入: cost = [10, 15, 20]
	输出: 15
	解释: 最低花费是从cost[1]开始，然后走两步即可到阶梯顶，一共花费15。
 示例 2:
	
	输入: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
	输出: 6
	解释: 最低花费方式是从cost[0]开始，逐个经过那些1，跳过cost[3]，一共花费6。
注意：

- cost 的长度将会在 [2, 1000]。
- 每一个 cost[i] 将会是一个Integer类型，范围为 [0, 999]。

思路：动态规划。最小子问题，每一步i的最小从花费等于前一步开销加上当前i阶梯开销和前两步的开销加上当前i阶梯的开销中的最小值。对于，最后一步，如果是前两步的开销必须加上最后一步的开销，而对于前一步的开销就不需要加上最后一步的开销。

代码：

    int minCostClimbingStairs(vector<int>& cost) {
        if (cost.size() == 0)
            return 0;
        if (cost.size() == 1)
            return cost[0];
        if (cost.size() == 2)
            return min(cost[0], cost[1]);
        vector<int> dp(cost.size());
        dp[0] = cost[0];
        dp[1] = cost[1];
        int i;
        for (i = 2; i < cost.size()-1; i++)
        {
            dp[i] = min(dp[i-1]+cost[i], dp[i-2]+cost[i]);
        }
        dp[i] = min(dp[i-1], dp[i-2]+cost[i]);
        return dp[i];
    }


## 动态规划

### 5. 最大子序和


给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:
	
	输入: [-2,1,-3,4,-1,2,1,-5,4],
	输出: 6
	解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

进阶:

如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。


思路： 子问题是每次判断前面所有的和是否大于0，如果小于零，则对于求最大和没有作用，所以直接省略前面的和。


代码：

    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int * dp = new int[n];
        dp[0] = nums[0];
        int m = nums[0];
        for (int i = 1; i < n; i++)
        {
            dp[i] = nums[i] + (dp[i-1] > 0 ?  dp[i-1] : 0);
            m = max(m, dp[i]);
        }
        return m;
    }

### 70 爬楼梯

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：

	输入： 2
	输出： 2
	解释： 有两种方法可以爬到楼顶。
	1.  1 阶 + 1 阶
	2.  2 阶

示例 2：
	
	输入： 3
	输出： 3
	解释： 有三种方法可以爬到楼顶。
	1.  1 阶 + 1 阶 + 1 阶
	2.  1 阶 + 2 阶
	3.  2 阶 + 1 阶


思路：初始问题，一个台阶只有一种爬法；两个台阶有两种爬法。而对于i个台阶，其爬法为：i-1个台阶爬法和i-2个台阶的爬法之和。

代码：

	    int climbStairs(int n) {
	        if (n == 1)
	            return 1;
	        int * dp = new int[n+1];
	        dp[1] = 1;
	        dp[2] = 2;
	        for (int i = 3; i <= n; i++)
	        {
	            dp[i] =dp[i-1] + dp[i-2]; 
	        }
	        return dp[n];
	        /*if (n == 1) //递归实现
	            return 1;
	        if (n == 2)
	            return 2;
	        int a = climbStairs(n-1) + climbStairs(n-2);
	        return a;*/
	    }


### 121 买卖股票的最佳时机

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

示例 1:
	
	输入: [7,1,5,3,6,4]
	输出: 5
	解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
	     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
示例 2:
	
	输入: [7,6,4,3,1]
	输出: 0
	解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。


思路： 每次第i个元素和前面i个元素中最小值，并将i个元素减去最小值，然后取差中的最大值。

其实这是一个求最低谷到最高峰的问题。



代码：

    int maxProfit(vector<int>& prices) {
        if (prices.size() <= 1)
            return 0;
        int minprices = prices[0];  
        int maxprofit = 0;
        for (int i = 1; i < prices.size(); i++)
        {
            
            minprices = min(minprices, prices[i]);
            int profit =  prices[i] - minprices;
            maxprofit = max(profit, maxprofit);
        }
        return maxprofit;
    }


### 198 打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

示例 1:
	
	输入: [1,2,3,1]
	输出: 4
	解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
	     偷窃到的最高金额 = 1 + 3 = 4 。
示例 2:
	
	输入: [2,7,9,3,1]
	输出: 12
	解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
	     偷窃到的最高金额 = 2 + 9 + 1 = 12 。


思路： 动态规划。初始问题，只有一个房屋是，小偷收益就是该房间的。如果两个房屋，小偷收益就是两个房屋中的最大值。而对于第i个房屋，小偷的最大收益为当前房屋i的金额加上偷第i-2个房屋的收益和偷第i-1个房屋的收益的最大值。

代码：


    int rob(vector<int>& nums) {
        if (nums.size() == 0)
            return 0;
        if (nums.size() == 1)
            return nums[0];
        vector<int> dp(nums.size());
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < nums.size(); i++)
        {
            dp[i] = max(dp[i-2]+nums[i], dp[i-1]);
        }
        return dp[nums.size()-1];
    }


### 213 打家劫舍 Ⅱ（中等）

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

示例 1:
	
	输入: [2,3,2]
	输出: 3
	解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
示例 2:
	
	输入: [1,2,3,1]
	输出: 4
	解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
	     偷窃到的最高金额 = 1 + 3 = 4 。

思路：分两种情况，分别计算两种情况下的最大收益，即两次动态规划。第一种情况，即偷第一间房屋，这样就不能偷最后一间房屋；第二种情况，不偷第一间放我，这样就可以偷最后一间房屋。

代码：

    int rob(vector<int>& nums) {
        vector<int> dp1(nums.size());
        vector<int> dp2(nums.size());
        if (nums.size()  == 0)
            return 0;
        if (nums.size() == 1)
            return nums[0];
        dp1[0] = nums[0];
        dp1[1] = max(nums[0], nums[1]);
        for (int i = 2; i < nums.size()-1; i++)
        {
            dp1[i] = max(dp1[i-1], nums[i]+dp1[i-2]);
        }
        dp2[0] = 0;
        dp2[1] = nums[1];
        for (int i = 2; i < nums.size(); i++)
        {
            dp2[i] = max(dp2[i-1], nums[i] + dp2[i-2]);
        }
        return max(dp1[nums.size()-2], dp2[nums.size()-1]);
    }


### 303  区域和检索 - 数组不可变

给定一个整数数组  nums，求出数组从索引 i 到 j  (i ≤ j) 范围内元素的总和，包含 i,  j 两点。

示例：
	
	给定 nums = [-2, 0, 3, -5, 2, -1]，求和函数为 sumRange()
	
	sumRange(0, 2) -> 1
	sumRange(2, 5) -> -1
	sumRange(0, 5) -> -3

说明:

- 你可以假设数组不可变。
- 会多次调用 sumRange 方法。


思路：创建一个共享数组。构造函数往共享数组中写数据，该数组中元素是构造函数参数数组中前i个元素的和，而sumRange()函数返回结果。

代码：

	class NumArray {
	public:
	    //创建一个数字记录前面计算过的值，避免重复计算
	    vector<int> num;
	    NumArray(vector<int>& nums) {
	        num.push_back(0);
	        int sum = 0;
	        for (int i = 0; i < nums.size(); i++)
	        {
	            sum += nums[i];
	            num.push_back(sum);
	        }
	    }
	    
	    int sumRange(int i, int j) {
	        return num[j+1] - num[i];
	    }
	};


### 746 使用最小花费爬楼梯

数组的每个索引做为一个阶梯，第 i个阶梯对应着一个非负数的体力花费值 cost [i] (索引从0开始)。

每当你爬上一个阶梯你都要花费对应的体力花费值，然后你可以选择继续爬一个阶梯或者爬两个阶梯。

您需要找到达到楼层顶部的最低花费。在开始时，你可以选择从索引为 0 或 1 的元素作为初始阶梯。

示例 1:
	
	输入: cost = [10, 15, 20]
	输出: 15
	解释: 最低花费是从cost[1]开始，然后走两步即可到阶梯顶，一共花费15。

示例 2:
	
	输入: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
	输出: 6
	解释: 最低花费方式是从cost[0]开始，逐个经过那些1，跳过cost[3]，一共花费6。
注意：

- cost 的长度将会在 [2, 1000]。
- 每一个 cost[i] 将会是一个Integer类型，范围为 [0, 999]。

思路：动态规划问题。最小问题就是对于第i个台阶，所需的总开销就是当前台阶的开销加上前一个台阶和前两个台阶最小开销之和。

需要注意的是，最后一步，如果从第i-1步直接走上去，就不需要加上第i个台阶的开销。而对于从第i-2步走得话，就需要加上第i个台阶得开销。

代码：

    int minCostClimbingStairs(vector<int>& cost) {
        if (cost.size() == 0)
            return 0;
        if (cost.size() == 1)
            return cost[0];
        if (cost.size() == 2)
            return min(cost[0], cost[1]);
        vector<int> dp(cost.size());
        dp[0] = cost[0];
        dp[1] = cost[1];
        int i;
        for (i = 2; i < cost.size()-1; i++)
        {
            dp[i] = min(dp[i-1]+cost[i], dp[i-2]+cost[i]);
        }
        //最后一步需要单独考虑，可能最后一个台阶可以直接跳过
        dp[i] = min(dp[i-1], dp[i-2]+cost[i]);
        return dp[i];
    }

#### 1025. 除数博弈

爱丽丝和鲍勃一起玩游戏，他们轮流行动。爱丽丝先手开局。

最初，黑板上有一个数字 N 。在每个玩家的回合，玩家需要执行以下操作：

- 选出任一 x，满足 0 < x < N 且 N % x == 0 。
- 用 N - x 替换黑板上的数字 N 。

如果玩家无法执行这些操作，就会输掉游戏。

只有在爱丽丝在游戏中取得胜利时才返回 True，否则返回 false。假设两个玩家都以最佳状态参与游戏。

 

示例 1：
	
	输入：2
	输出：true
	解释：爱丽丝选择 1，鲍勃无法进行操作。
	示例 2：
	
	输入：3
	输出：false
	解释：爱丽丝选择 1，鲍勃也选择 1，然后爱丽丝无法进行操作。
 

提示：

	1 <= N <= 1000


方法一： 归纳法

思路： 首先需要明确的是，对于一个确定的数N，只可能赢或者输，不可能存在不确定的情况，也就是只要对于N能存在赢的情况就是赢，那么该问题演变为，如果Alice拿到N是输的话，那么她拿到N+1一定为赢，因为拿到N+1时，她可以只取1，那么Bob就会拿到N，Bob就会输，所以这时候奇数偶数，一个为赢另一个为输的问题，我们只需要确定奇数赢还是偶数赢就可以了，依据示例可以看出偶数是赢的

代码：

    bool divisorGame(int N) {
        return N % 2 == 0;
    }

方法二：暴力解法

代码：

    bool divisorGame(int N) {
        int count = 0;
        while (N > 1)
        {
            for (int i = 1; i < sqrt(N); i++)
            {
                if (N % i == 0)
                {
                    N = N - i;
                    count++;
                    break;
                }
            }
        }
        return (count % 2) != 0;
        
    }

方法三：动态规划DP

代码：

    bool divisorGame(int N) {
        if (N < 2)
            return false;
        vector<bool> dp(N+1);
        dp[0] = false;
        dp[1] = false;
        for (int i = 2; i <= N; i++)
            for (int j = 1; j < i; j++)
            {
                if (!dp[i-j] && i % j == 0)
                {
                    dp[i] = true;
                    break;
                }
            }
        return dp[N];
    }



## 字符串


### 415 字符串相加

给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和。

注意：

- num1 和num2 的长度都小于 5100.
- num1 和num2 都只包含数字 0-9.
- num1 和num2 都不包含任何前导零。
- 你不能使用任何內建 BigInteger 库， 也不能直接将输入的字符串转换为整数形式。


思路：首先将两个字符串进行转序，创建一个num3字符串记录结构。然后从i=0开始，每次将字符串中的字符转换为整型，然后计算两者的和，如果和大于9，则将和取余转换成字符串加入num3，并将flag设置为1，表示需要进位；否则直接结果放入num3中。最后，判断flag是否为1，如果为1，在num3的末尾添加一个字符1。最后，将num3进行转序得到结果。

代码：

    string addStrings(string num1, string num2) {
        reverse(num1.begin(), num1.end());
        reverse(num2.begin(), num2.end());
        string num3;
        int flag = 0;
        int i;
        for (i = 0; i < num1.size() || i < num2.size(); i++)
        {
            int ans = 0, n1 = 0, n2 = 0;
            if (i < num1.size())
                n1 = num1[i] - '0';
            if (i < num2.size())
                n2 = num2[i] - '0';
            ans = n1 + n2 + flag;
            if (ans > 9)
            {
                num3.insert(i,1, ans%10 + '0');
                flag = 1;
            }
            else 
            {
                num3.insert(i,1, ans+'0');
                flag = 0;
            }
        }
        if (flag == 1)
            num3.append("1");
        reverse(num3.begin(), num3.end());
        return num3;
    }

### 434 字符串中的单词数

统计字符串中的单词个数，这里的单词指的是连续的不是空格的字符。

请注意，你可以假定字符串里不包括任何不可打印的字符。

示例:

	输入: "Hello, my name is John"
	输出: 5

思路：但前一个字符不为空格，而当前字符串为空格或者为\0时，表示前面出现一个单词。

代码：

    int countSegments(string s) {
        int count = 0;
        for (int i = 1; i <= s.size(); i++)
        {
            if (s[i-1] != ' ' && (s[i] == ' ' || s[i] == '\0' ))
                count++;
        }
        return count;
    }

### 443 压缩字符串

题目：

给定一组字符，使用原地算法将其压缩。

压缩后的长度必须始终小于或等于原数组长度。

数组的每个元素应该是长度为1 的字符（不是 int 整数类型）。

在完成原地修改输入数组后，返回数组的新长度。

 

进阶：
你能否仅使用O(1) 空间解决问题？

 

示例 1：
	
	输入：
	["a","a","b","b","c","c","c"]
	
	输出：
	返回6，输入数组的前6个字符应该是：["a","2","b","2","c","3"]
	
	说明：
	"aa"被"a2"替代。"bb"被"b2"替代。"ccc"被"c3"替代。
示例 2：
	
	输入：
	["a"]
	
	输出：
	返回1，输入数组的前1个字符应该是：["a"]
	
	说明：
	没有任何字符串被替代。
示例 3：
	
	输入：
	["a","b","b","b","b","b","b","b","b","b","b","b","b"]
	
	输出：
	返回4，输入数组的前4个字符应该是：["a","b","1","2"]。
	
	说明：
	由于字符"a"不重复，所以不会被压缩。"bbbbbbbbbbbb"被“b12”替代。
	注意每个数字在数组中都有它自己的位置。
注意：

- 所有字符都有一个ASCII值在[35, 126]区间内。
- 1 <= len(chars) <= 1000。


思路：双指针法。一个指针记录需要存放字母出现次数的位置。当另一个指针指向的字符和后一个字符相等，则计数器count加加，否则如果两个字符不相等，分两种情况，如果count=1，则不需要将count加入到字符串中；否则，得将count放入字符串中，利用`to_string()`函数将整型转换成字符串，然后一个一个字符添加到字符串中。

代码：

    int compress(vector<char>& chars) {
        int r = 0;
        int count = 1;
        for (int i = 0; i < chars.size()-1; i++)
        {
            if (chars[i] == chars[i+1])
                count++;
            else if (chars[i] != chars[i+1] && count == 1)
            {
                chars[++r] = chars[i+1];
            }
            else
            {
                string temp = to_string(count);
                for (auto c : temp)
                    chars[++r] = c;
                chars[++r] = chars[i+1];
                count = 1;
            }
        }
        if (count != 1)
        {
            string temp = to_string(count);
            for (auto c : temp)
                chars[++r] = c;
        }
        return r+1;
    }

### 459 重复得子字符串

给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。

示例 1:
	
	输入: "abab"
	
	输出: True
	
	解释: 可由子字符串 "ab" 重复两次构成。
示例 2:

	输入: "aba"
	
	输出: False
示例 3:
	
	输入: "abcabcabcabc"
	
	输出: True
	
	解释: 可由子字符串 "abc" 重复四次构成。 (或者子字符串 "abcabc" 重复两次构成。)

思路：创建一个字符数组，其大小是原字符串大小的2倍减1，然后将字符串除第一个字符之外的所有字符放入数组中，以及将除字符最后一个字符之外的所有字符放入数组中。最后，判断新创建的字符串是否包含原字符串。

注意： strncat(dest, src, n)，将src中的前n个字符追加到str的末尾；strstr(str1, str2)判断str2是否是str1的子串。memset(str, 0, sizeof(str))将str中所有字符初始化为0。

代码：

    bool repeatedSubstringPattern(string s) {
        int len = s.size();
        char str1[len+1];
        int i;
        for (i = 0; i < len; i++)
            str1[i] = s[i];
        str1[i] = '\0';
        char str[2 * len - 1];
        memset(str, 0, sizeof(str));
        strncat(str, str1 + 1, len - 1);
        strncat(str, str1, len - 1);

        return strstr(str, str1);
    }


### 520. 检测大写字母

给定一个单词，你需要判断单词的大写使用是否正确。

我们定义，在以下情况时，单词的大写用法是正确的：

- 全部字母都是大写，比如"USA"。
- 单词中所有字母都不是大写，比如"leetcode"。
- 如果单词不只含有一个字母，只有首字母大写， 比如 "Google"。

否则，我们定义这个单词没有正确使用大写字母。

示例 1:
	
	输入: "USA"
	输出: True
示例 2:

	输入: "FlaG"
	输出: False
	注意: 输入是由大写和小写拉丁字母组成的非空单词。

思路： 首先判断首字母是否为大写，用flag=true标记，如果剩余字符中出现大写字母，则将flag设为false，并结束循环；最后判断flag是否为true，为true则返回true。

以上是第一种情况，现在分析第二种和第三种情况，初始化两个计数器，分别记录大写字母的数量和小写字母的数量，通过islower和isupper函数来判断一个字符是否为小写字母和大写字母。最后判断两者的大小是否等于字符串的大小，如果某一个等于字符串大小，则返回true，否则返回false。

代码：

    bool detectCapitalUse(string word) {
        int flag = false;
        if (isupper(word[0]))
            flag = true;
        for (int i = 1; i < word.size(); i++)
        {
            if (isupper(word[i]))
    		{		            
				flag = false;
				break;
			}	
        }
        if (flag == true)
            return true;
        int up_count = 0;
        int low_count = 0;
        for (int i = 0; i < word.size(); i++)
            if (islower(word[i]))
                low_count++;
            else 
                up_count++;
        if (low_count == word.size() || up_count == word.size())
            return true;
        return false;     
    }

第二种解法：记录大写字母的数量，如果出现大写字母的数量小于当前字符的下标，则返回false，说明前面出现非连续的大写字母。

代码：

    bool detectCapitalUse(string word) {
        int uc = 0;
        for (int i = 0; i < word.size(); i++)
        {
            if (isupper(word[i]) && uc++ < i)
                return false;
        }
        return uc == word.size() || uc <= 1;
    }

### 521 最长特殊序列 Ⅰ

给定两个字符串，你需要从这两个字符串中找出最长的特殊序列。最长特殊序列定义如下：该序列为某字符串独有的最长子序列（即不能是其他字符串的子序列）。

子序列可以通过删去字符串中的某些字符实现，但不能改变剩余字符的相对顺序。空序列为所有字符串的子序列，任何字符串为其自身的子序列。

输入为两个字符串，输出最长特殊序列的长度。如果不存在，则返回 -1。

示例 :

	输入: "aba", "cdc"
	输出: 3
	解析: 最长特殊序列可为 "aba" (或 "cdc")
说明:

- 两个字符串长度均小于100。
- 字符串中的字符仅含有 'a'~'z'。

思路：

- s1 = 'ab',s2 = 'a',因为ab是s1独有，所以最长子序列为ab，
- s1 = 'ab', s2 = 'ab', 因为ab是两个串都有，ab排除，a也是两个串都有，排除，b也是两个串都有，排除。所以最长特殊序列不存在，返回-1
- 通过以上分析，我们可以得出结论，如果：两个串相等（不仅长度相等，内容也相等），那么他们的最长特殊序列不存在。返回-1
- 如果两个串长度不一样，那么长的串 永远也不可能是 短串的子序列，即len(s1) > len(s2),则最长特殊序列为s1,返回长度大的数

注意：

string转换成字符数组转的函数： c_str()
	
	string x = "ab";
	char * y = x.c_str();

字符数组转成成string的函数： 直接赋值

	const char * x = "abc";
	string y = x;

整型转string: to_string()
	
	int a = 10;
	string x = a.to_string();

strcmp(str1, str2)，比较两个字符串的是否相等（长度和内容相等），str1和str2为字符数组。若str1=str2，则返回零；若str1<str2，则返回负数；若str1>str2，则返回正数。

代码：

  	int findLUSlength(string a, string b) {
        return (strcmp(a.c_str(), b.c_str()) ? (a.size()> b.size() ? a.size() : b.size() ): -1);
    }


### 541 反转字符串 II

给定一个字符串和一个整数 k，你需要对从字符串开头算起的每个 2k 个字符的前k个字符进行反转。如果剩余少于 k 个字符，则将剩余的所有全部反转。如果有小于 2k 但大于或等于 k 个字符，则反转前 k 个字符，并将剩余的字符保持原样。

示例:
	
	输入: s = "abcdefg", k = 2
	输出: "bacdfeg"

要求:

- 该字符串只包含小写的英文字母。
- 给定字符串的长度和 k 在[1, 10000]范围内。

思路:对字符串进行分隔，然后创建一个临时字符串来存储每个部分的字符串，并利用reverse进行逆转，逆转后将该字符串加入存放结果的字符串中。最后，两种特殊情况进行讨论，即大小k个字符时，以及大于等于k个字符和小于2k。

代码：

    string reverseStr(string s, int k) {
        int n = s.size()%(2*k);
        int m = s.size()/(2*k);
        string str;
        for (int i = 0; i < m; i++)
        {
            string temp = string(s.begin()+2*k*i, s.begin()+2*k*(i+1));
            reverse(temp.begin(), temp.end()-k);
            str += temp;
            temp = "";
        }
        string temp = string(s.begin()+2*k*m, s.end());
        if (n < k)
            reverse(temp.begin(), temp.end());
        if (n >= k && n < 2*k)
            reverse(temp.begin(), temp.begin()+k);
        str += temp;
        return str;
    }


### 551 学生出勤记录

给定一个字符串来代表一个学生的出勤记录，这个记录仅包含以下三个字符：

	'A' : Absent，缺勤
	'L' : Late，迟到
	'P' : Present，到场
如果一个学生的出勤记录中不超过一个'A'(缺勤)并且不超过两个连续的'L'(迟到),那么这个学生会被奖赏。

你需要根据这个学生的出勤记录判断他是否会被奖赏。

示例 1:

	输入: "PPALLP"
	输出: True
示例 2:
	
	输入: "PPALLL"
	输出: False

思路: 不被奖赏的同学包括：大于等于2个A，或者存在连续3个L。所以，我们只要统计A的个数，以及是否存在连续的3个L，两个条件满足一个就返回false。如果字符串长度小于等于1，则一定会被奖赏，返回true。

代码:

    bool checkRecord(string s) {
        if (s.size() <= 1)
            return true;
        int flag1 = 0, flag2 = 0;
        for (int i = 0; i < s.size()-2; i++)
        {
            if (s[i] == 'A')
                flag1 += 1;
            if (s[i] == s[i+1] && s[i+1] == s[i+2] && (s[i] == 'L'))
                flag2 = 1;
        }
        if ( s[s.size()-1] == 'A')
            flag1  += 1;
        if (s[s.size()-2] == 'A')
            flag1++;
        if (flag1 >= 2 || flag2 == 1)
            return false;
        else
            return true;
    }


## 树

### 100 相同的树

给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

示例 1:

	输入:       1         1
	          / \       / \
	         2   3     2   3
	
	        [1,2,3],   [1,2,3]
	
	输出: true
示例 2:

	输入:      1          1
	          /           \
	         2             2
	
	        [1,2],     [1,null,2]
	
	输出: false
示例 3:

	输入:       1         1
	          / \       / \
	         2   1     1   2
	
	        [1,2,1],   [1,1,2]
	
	输出: false

思路： 递归。最小问题时，但两颗树同时指向空，则返回true，如果其中一个为空，另一个不为空，或者两个都不为空，但是不相等，则返回false。

代码：

    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == NULL && q == NULL)
            return true;
        if ( (p != NULL &&  q == NULL) || (p == NULL &&  q != NULL) || ((p != NULL && q != NULL) && (p->val != q->val)) )
            return false;
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        
    }

简单版：

    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == NULL && q == NULL)
            return true;
        if ((p != NULL && q != NULL) && (p->val == q->val)) 
            return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        else
            return false;
    }


### 101 对称二叉树

给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

	    1
	   / \
	  2   2
	 / \ / \
	3  4 4  3
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

	    1
	   / \
	  2   2
	   \   \
	   3    3
说明:

如果你可以运用递归和迭代两种方法解决这个问题，会很加分。

思路：递归的子问题是：判断两个子树中，一个子树的左子树是否等于另一个子树的右子树，一个子树的右子树是否等于另一个子树的左子树。

代码：


 	bool isSymmetric(TreeNode* root) {
    if (root == NULL)
            return true;
        return isSameTree(root->left, root->right);
    }
    
    bool isSameTree(TreeNode * p, TreeNode * q)
    {
        if (p == NULL && q == NULL)
            return true;
        if (p != NULL && q != NULL && p->val == q->val)
            return isSameTree(p->left, q->right) && isSameTree(p->right, q->left);
        else
            return false;
    }


### 104. 二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

示例：

给定二叉树 [3,9,20,null,null,15,7]，
	
	    3
	   / \
	  9  20
	    /  \
	   15   7

返回它的最大深度 3 。

思路：递归

代码：

    int maxDepth(TreeNode* root) {
        if (root == NULL)
            return 0;
        return max(maxDepth(root->left), maxDepth(root->right))+1;
    }


### 107. 二叉树的层次遍历 II

给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

例如：
给定二叉树 [3,9,20,null,null,15,7],

	    3
	   / \
	  9  20
	    /  \
	   15   7

返回其自底向上的层次遍历为：

	[
	  [15,7],
	  [9,20],
	  [3]
	]

思路：通过队列实现。首先将根节点放入队列中。求出队列的长度，将队列中的元素放入一个数组中，当作一层的输出，并将队列中结点的子节点放入队列中，将上一层的节点在队列中删除。每一层的节点组成的数组放入一个新的数组中。

代码：

    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> res;
        if (root == NULL)
            return res;
        queue<TreeNode*> q;
        q.push(root); //将头节点放入队列中
        while (!q.empty())
        {
            int len = q.size(); //获取队列的长度
            vector<int> temp; 
            for (int i = 0; i < len; i++) //将当前队列中的节点的值取出放入数组，并将节点的子树放入依次放入队列中
            {
                temp.push_back(q.front()->val); 
                if (q.front()->left != NULL)
                    q.push(q.front()->left);
                if (q.front()->right != NULL)
                    q.push(q.front()->right);
                q.pop();
            }
            res.insert(res.begin(), temp); //将一层节点的值放入结果数组中
        }
        return res;
    }


### 108. 将有序数组转换为二叉搜索树

将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

示例:

给定有序数组: [-10,-3,0,5,9],

一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：

	      0
	     / \
	   -3   9
	   /   /
	 -10  5


思路： 递归的思想。每次将数组的中间元素作为根结点，左侧的元素位于左子树，右侧的元素位于右子树。

代码：

    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return sortedArrayToBST(nums, 0, nums.size()-1);
    }
    TreeNode * sortedArrayToBST(vector<int> & nums, int i, int j)
    {
        if (i > j)
            return NULL;
        int mid = (j-i)/2+i;
        TreeNode * root = new TreeNode(nums[mid]);
        root->left = sortedArrayToBST(nums, i, mid-1);
        root->right = sortedArrayToBST(nums, mid+1, j);
        return root;
    }


### 110. 平衡二叉树

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过1。

示例 1:

给定二叉树 [3,9,20,null,null,15,7]

	    3
	   / \
	  9  20
	    /  \
	   15   7
返回 true 。

示例 2:

给定二叉树 [1,2,2,3,3,null,null,4,4]

	       1
	      / \
	     2   2
	    / \
	   3   3
	  / \
	 4   4
返回 false 。

思路： 首先判断树是否为空，或者树是否有子树，如果数为空，或者根节点没有子树，就返回true。否则，计算两棵子树的高度差，如果高度差小于1，则递归计算左右两棵子树是否满足平衡二叉树，否则直接返回false。

树的深度计算也是通过递归实现。

代码：

    bool isBalanced(TreeNode* root) {
        if (root == NULL || root->left == NULL && root->right == NULL)
            return true;
        int t = abs(deepOfTree(root->left) - deepOfTree(root->right));
        if (t <= 1)
            return isBalanced(root->left) && isBalanced(root->right);
        else
            return false;
        
    }
    int deepOfTree(TreeNode * root)
    {
        if (root == NULL)
            return 0;
        return max(deepOfTree(root->left), deepOfTree(root->right)) + 1;
    }


上一种方法比较慢，使用了两次递归。下面的方法每次计算递归计算树的高度。

   bool isBalanced(TreeNode* root) {
        int h;
        return isBalanced(root, h);
    }
    
    bool isBalanced(TreeNode* root, int& height) {
        if (root == nullptr)
        {
            height = 0;
            return true;
        }
        int h1, h2;
        bool flag = isBalanced(root->left, h1) &&
            isBalanced(root->right, h2);
        height = max(h1, h2) + 1;
        return flag && abs(h1 - h2) <= 1;
    }


### 111. 二叉树的最小深度


给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

说明: 叶子节点是指没有子节点的节点。

示例:

给定二叉树 [3,9,20,null,null,15,7],

	    3
	   / \
	  9  20
	    /  \
	   15   7
返回它的最小深度  2.


思路：递归实现。每次取两颗子树的最小深度加1，终止条件根节点为NULL。

注意两种情况：一种根节点没有左子树，另一种是根节点没有右子树。即下面两种情况。

![](https://i.imgur.com/C3QPVWZ.png)

上面两种情况，最小深度为3。需要单独考虑，如果不单独考虑，这两种情况的最小深度都为1。


代码：

    int minDepth(TreeNode* root) {
        if (root == NULL)
            return 0;
        if (root->left == NULL && root->right != NULL)
            return minDepth(root->right)+1;
        if (root->left != NULL && root->right == NULL)
            return minDepth(root->left)+1;
        return min(minDepth(root->left), minDepth(root->right)) + 1;
    }


### 112. 路径总和

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

说明: 叶子节点是指没有子节点的节点。

示例: 
给定如下二叉树，以及目标和 sum = 22，

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

思路：递归思路。每次递归都减去当前节点的值，然后取两个子树递归结果的或。如果该节点为叶子节点并且sum=0，则返回true。如果当前节点为空节点返回false。


代码：

    bool hasPathSum(TreeNode* root, int sum) {
        if (root == NULL)
            return false;
        sum -= root->val;
        if (root->left == NULL && root->right == NULL)
            return (sum == 0);
        return hasPathSum(root->left, sum) || hasPathSum(root->right, sum);
    }


### 226. 翻转二叉树 

翻转一棵二叉树。

示例：

输入：
	
	     4
	   /   \
	  2     7
	 / \   / \
	1   3 6   9
输出：

	     4
	   /   \
	  7     2
	 / \   / \
	9   6 3   1

备注:
这个问题是受到 Max Howell 的 原问题 启发的 ：

谷歌：我们90％的工程师使用您编写的软件(Homebrew)，但是您却无法在面试时在白板上写出翻转二叉树这道题，这太糟糕了。

思路：先序遍历实现。

代码：

    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL)
            return nullptr;
        TreeNode * temp = root->right;
        root->right = invertTree(root->left);
        root->left = invertTree(temp);
        return root;
    }

其他方法：中序遍历和后序遍历实现跟先序遍历相似。还有一种就是层次遍历，利用队列实现。

代码：

    TreeNode* invertTree(TreeNode* root) {   
     //层次遍历实现
        if (root == NULL)
            return nullptr;
        queue<TreeNode *> q;
        q.push(root);
        while (!q.empty())
        {
            TreeNode * node = q.front();
            q.pop();
            TreeNode * temp = node->left;
            node->left = node->right;
            node->right = temp;
            if (node->left != NULL)
                q.push(node->left);
            if (node->right != NULL)
               q.push(node->right) ;
        }
        return root;
    }


中序遍历和后序遍历理解有点困难：

	利用中序遍历
	class Solution {
	    public TreeNode invertTree(TreeNode root) {
	            if (root == null) return null;
	            invertTree(root.left); // 递归找到左节点
	            TreeNode rightNode= root.right; // 保存右节点
	            root.right = root.left;
	            root.left = rightNode;
	            // 递归找到右节点 继续交换 : 因为此时左右节点已经交换了,所以此时的右节点为root.left
	            invertTree(root.left); 
	    }
	}
	
	利用后序遍历
	 class Solution {
	        public TreeNode invertTree(TreeNode root) {
	            // 后序遍历-- 从下向上交换
	            if (root == null) return null;
	            TreeNode leftNode = invertTree(root.left);
	            TreeNode rightNode = invertTree(root.right);
	            root.right = leftNode;
	            root.left = rightNode;
	            return root;
	        }
	    }

### 235 二叉搜索树的最近公共祖先

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/binarysearchtree_improved.png)
 

示例 1:
	
	输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
	输出: 6 
	解释: 节点 2 和节点 8 的最近公共祖先是 6。
示例 2:

	输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
	输出: 2
	解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。
 

说明:

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉搜索树中。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。


思路： 利用二叉搜索树的特性，左子树的值小于根节点的值，右子树的值大于根节点的值。两个节点的最近祖先，满足该节点的值大于一个子节点的值，而小于另一个子节点的值。如果同时大于两个子节点，则表示两个子节点在节点的左子树上；如果同时小于两个子节点，则表示两个子节点在节点的右子树上。


代码：

    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == NULL)
            return NULL;
        while (true)
        {
            if (root->val > p->val && root->val > q->val)
                root = root->left;
            else if (root->val < p->val && root->val < q->val)
                root = root->right;
            else
                return root;
        }
    }


### 257 二叉树的所有路径

给定一个二叉树，返回所有从根节点到叶子节点的路径。

说明: 叶子节点是指没有子节点的节点。

示例:

输入:

	   1
	 /   \
	2     3
	 \
	  5

输出: ["1->2->5", "1->3"]

解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3

思路：递归方法，每次递归中都将结果利用引用的形式存放在参数中，而且将一个路径上的字符串也放在参数中，如果到达叶子节点则将字符串放入结果中。

代码：

    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> res;
        if (root == NULL)
            return res;
        string temp = "";
        binaryTreePaths(root, temp, res);
        return res;
        
    }
    void binaryTreePaths(TreeNode * root, string temp, vector<string> &res)
    {
        if (root == NULL)
            return;
        temp += to_string(root->val);
        if (root->left == NULL && root->right == NULL)
            res.push_back(temp);
        binaryTreePaths(root->left, temp + "->", res);
        binaryTreePaths(root->right, temp + "->", res);
    }


### 404 左叶子之和

计算给定二叉树的所有左叶子之和。

示例：
	
	    3
	   / \
	  9  20
	    /  \
	   15   7

在这个二叉树中，有两个左叶子，分别是 9 和 15，所以返回 24

思路：递归实现，递归函数中保护所要计算的结果和一个标记，标记用来标记一个节点是否为左节点。如果一个节点的左子树和右子树为空，并且标记为true(即为左子树)，则将该节点的值加入到结果中。

代码：

    int sumOfLeftLeaves(TreeNode* root) {
        if (root == NULL)
            return 0;
        int sum = 0;
        sumOfLeftLeaves(root, false, sum);
        return sum;
    }
    void sumOfLeftLeaves(TreeNode* root, bool flag, int &sum) {
        if (root->left == NULL && root->right == NULL && flag == true)
        {
            sum += root->val;
            return;
        }
        if (root->left == NULL && root->right == NULL && flag == false)
            return;
        if (root->left != NULL)
            sumOfLeftLeaves(root->left, true, sum);
        if (root->right != NULL)
            sumOfLeftLeaves(root->right, false, sum);
    }

另一种解法：

    int sumOfLeftLeaves(TreeNode* root) {
        int res = 0;
        if (root == nullptr)
            return res;
        if (root->left == nullptr && root->right == nullptr)
            return res;
        if (root->left != nullptr && root->left->left == nullptr && root->left->right == nullptr)
            res += root->left->val;
        else if (root->left)
            res += sumOfLeftLeaves(root->left);
        if (root->right)
            res += sumOfLeftLeaves(root->right);
        return res;
    }

### 437 路径总和 Ⅲ

给定一个二叉树，它的每个结点都存放着一个整数值。

找出路径和等于给定数值的路径总数。

路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

示例：

root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

	      10
	     /  \
	    5   -3
	   / \    \
	  3   2   11
	 / \   \
	3  -2   1

返回 3。和等于 8 的路径有:

1.  5 -> 3
2.  5 -> 2 -> 1
3.  -3 -> 11

思路：递归。利用数组来记录走过的路径。并判断当前路径是否等于sum。

代码：

    int pathSum(TreeNode* root, int sum) {
        if(root == nullptr) return 0;
        vector<int> buf;
        int res =0;
        pathSum_core(root,sum,buf,res);
        return res;
    }
    
    void pathSum_core(TreeNode *root,int sum,vector<int> &buf, int &res){
        if(root == nullptr) return;
        buf.push_back(root->val);
        int ans = 0;
        for(int i = buf.size()-1;i>=0;i--){  
            ans += buf[i];
            if(ans == sum) res++;
        }
        pathSum_core(root->left,sum,buf,res);
        pathSum_core(root->right,sum,buf,res);
        buf.pop_back();
    }

另一种方法比较笨，直接计算所有可能的路径，判断是否等于sum。效率比较低。

    int pathSum(TreeNode* root, int sum) {
        if (root == nullptr)
            return 0;
        return pathSum(root->left, sum) + pathSum(root->right, sum) + dfs(root, sum);
    }
    int dfs(TreeNode * root, int sum)
    {
        if (root == nullptr)
            return 0;
        int count = 0;
        if (root->val == sum)
            count += 1;
        
        return count + dfs(root->left, sum - root->val) + dfs(root->right, sum - root->val);
    }