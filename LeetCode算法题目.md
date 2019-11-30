---
title: LeetCode算法题目
date: 2018-08-22 22:28:50
tags:  
	 - 面试题目

---

leetcode是个[题库](https://leetcode-cn.com/problemset/all/)，里面有很编程多面试的题目，可以在线编译运行。难度比较高。如果自己能都做出来，对面大公司很有帮助。<!--more-->

## 两数之和

给定一个整数数组和一个目标值，找出数组中和为目标值的两个数。

你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。

**示例：**

	给定 nums = [2, 7, 11, 15], target = 9
	
	因为 nums[0] + nums[1] = 2 + 7 = 9
	所以返回 [0, 1]

**思路：**

利用python实现，先将列表转化为字典dict1，字典的键为列表元素，字典的值为对应元素的下标。

对于每个元素nums[i]，对于给定的target数，检查字典中是否有键（target-nums[i]）,如果存在则说明，该键对应的值和nums[i]的下标为对应的输出结果。注意该键对应的值不能是i!


**代码：**

	class Solution:
	    def twoSum(self, nums, target):
	        """
	        :type nums: List[int]
	        :type target: int
	        :rtype: List[int]
	        """
	        dict1 = {}
	        l = len(nums)
	        for i in range(0,l):
	            dict1[nums[i]] = i
	        for i in range(0,l):
	            temp = target - nums[i]
	            if (temp in dict1.keys()) and dict1.get(temp) != i:
	                return [i,dict1.get(temp)]

**算法分析:**


时间复杂度：O(n)， 我们把包含有 nn 个元素的列表遍历两次。由于哈希表将查找时间缩短到 O(1)O(1) ，所以时间复杂度为 O(n)O(n)。

空间复杂度：O(n)， 所需的额外空间取决于哈希表中存储的元素数量，该表中存储了 nn 个元素。 


## 两数相加

给定两个非空链表来表示两个非负整数。位数按照逆序方式存储，它们的每个节点只存储单个数字。将两数相加返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

**示例：**

	输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
	输出：7 -> 0 -> 8
	原因：342 + 465 = 807

**思路：**

考虑到两个链表长度不一，短的列表后面补零。

最开始的创建一个链表（ll），并赋值一个另一个值(lp)（后面链表的指针需要移动，不指向起始），并创建一个变量来存储进位的值（temp）。

首先判读两个链表是否都已到达末尾，如果没有，将两个链表的值分别赋值给两个变量（val1和val2）,如果其中一个到达末尾，则val1或val2赋值为0。然后，对两个数进行相加求余数，利用余数创建链表节点(lq)，并接在最先创建的链表(ll)后面，将节点ll先后指向下一个节点，同时计算val1+val2+temp的进位值。如果两个节点都已达到末尾，而此时还需进位值不为零（可以最后两个相加大于9），还需要创建一个节点来存储进位的值，并接在节点ll后面。

**代码：**

	# Definition for singly-linked list.
	# class ListNode:
	#     def __init__(self, x):
	#         self.val = x
	#         self.next = None
	
	class Solution:
	    def addTwoNumbers(self, l1, l2):
	        """
	        :type l1: ListNode
	        :type l2: ListNode
	        :rtype: ListNode
	        """
	        ll = ListNode(0)
	        lp = ll
	        temp = 0
	        while l1 or l2:
	            if l1 == None:
	                val1 = 0
	            else:
	                val1 = l1.val
	                l1 = l1.next
	            if l2  == None:
	                val2 = 0
	            else:
	                val2 = l2.val
	                l2 = l2.next
	            lq = ListNode((val1 + val2 + temp) % 10)
	            ll.next = lq
	            ll = ll.next
	            temp = (val1 + val2 + temp) //10
	            if l1 == None and l2 == None and temp != 0:
	                lq = ListNode(temp)
	                ll.next = lq
	        return lp.next


**算法分析：**

时间复杂度：O(n)， 我们只遍历了包含有 nn 个元素的列表一次。在表中进行的每次查找只花费 O(1)O(1) 的时间。

空间复杂度：O(n)， 所需的额外空间取决于哈希表中存储的元素数量，该表最多需要存储 nn 个元素。

## 无重复字符的最长子串

给定一个字符串，找出不含有重复字符的最长子串的长度。

示例 1:

输入: "abcabcbb"
输出: 3 
解释: 无重复字符的最长子串是 "abc"，其长度为 3。
示例 2:

输入: "bbbbb"
输出: 1
解释: 无重复字符的最长子串是 "b"，其长度为 1。
示例 3:

输入: "pwwkew"
输出: 3
解释: 无重复字符的最长子串是 "wke"，其长度为 3。
     请注意，答案必须是一个子串，"pwke" 是一个子序列 而不是子串。


### 方法一：暴力法

思路

逐个检查所有的子字符串，看它是否不含有重复的字符。

算法

假设我们有一个函数 boolean allUnique(String substring) ，如果子字符串中的字符都是唯一的，它会返回true，否则会返回false。 我们可以遍历给定字符串 s 的所有可能的子字符串并调用函数 allUnique。 如果事实证明返回值为true，那么我们将会更新无重复字符子串的最大长度的答案。

现在让我们填补缺少的部分：

为了枚举给定字符串的所有子字符串，我们需要枚举它们开始和结束的索引。假设开始和结束的索引分别为i和j。那么我们有 0≤i<j≤n （这里的结束索引 j 是按惯例排除的）。因此，使用 i 从0到 n−1 以及 jj 从 i+1 到 n 这两个嵌套的循环，我们可以枚举出 s 的所有子字符串。

要检查一个字符串是否有重复字符，我们可以使用集合。我们遍历字符串中的所有字符，并将它们逐个放入 set 中。在放置一个字符之前，我们检查该集合是否已经包含它。如果包含，我们会返回 false。循环结束后，我们返回 true。


**代码：**


	class Solution:
	    def lengthOfLongestSubstring(self, s):
	        """
	        :type s: str
	        :rtype: int
	        """
	       
	        def allUnique(s, start, end):
	            st = set()
	            for i in range(start,end):
	                if s[i] in st:
	                    return 0
	                st.add(s[i])
	            return 1
	        
	        l = len(s)
	        ans = 0
	        for i in range(0, l):
	            for j in range(i+1, l+1):
	                if(allUnique(s,i,j)):
	                    ans = max(ans, j-i)
	        return ans

### 方法二：滑动窗口

**代码：**

	class Solution:    
	    def lengthOfLongestSubstring(self, s):
	        """
	        :type s: str
	        :rtype: int
	        """
	        n = len(s)
	        ans = i = j = 0
	        st = set()
	        while i < n and j < n:
	            if s[j] in st:
	                st.remove(s[i])
	                i = i+1 
	            else:
	                st.add(s[j])
	                j = j + 1
	                ans = max(ans, j-i)
	        return ans

### 方法三：优化的滑动窗口

**代码：**

	class Solution:    
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        n = len(s)
        ans = i = j = 0
        d = {} #将存放
        st = set()
        while j < n:
            if s[j] in d.keys():
                i = max(i, d.get(s[j]))
            ans = max(ans, j-i+1)
            d[s[j]] = j + 1
            j = j + 1
        return ans  

## 两个排序数组的中位数

链接：[https://leetcode-cn.com/problems/median-of-two-sorted-arrays/description/](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/description/)

# 二、数组

##  1.三数之和

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]

### 方法一：
该方案时间复杂度比方案二高

	class Solution:
	    def threeSum(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: List[List[int]]
	        """
	        nums.sort()
	        l = len(nums)
	        a = []
	        dict1 = {}
	        for i in range(0,l):
	            dict1[nums[i]] = i
	        for i in range(0,l):
	            for j in range(i+1,l):
	                temp = -(nums[i] + nums[j])
	                if temp in dict1.keys() and dict1[temp] > i and dict1[temp] > j:
	                    b = [nums[i], nums[j], temp]
	                    if b not in a:
	                        a.append(b)
	                    
	        return a
                

### 方法二：

该方案时间复杂度为O(n^2)

	class Solution:
	    def threeSum(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: List[List[int]]
	        """
	        nums.sort()
	        l = len(nums)
	        a = []
	        for i in range(0,l-2):
	            if i > 0 and nums[i] == nums[i-1]:
	                continue
	            l, r = i + 1, len(nums)-1
	            while l < r:
	                s = nums[i] + nums[l] + nums[r]
	                if s < 0:
	                    l += 1
	                elif s > 0:
	                    r -= 1
	                else:
	                    a.append([nums[i], nums[l], nums[r]])
	                    while l < r and nums[l] == nums[l+1]:
	                        l += 1
	                    while l < r and nums[r] == nums[r-1]:
	                        r -= 1
	                    l += 1
	                    r -= 1
	        return a
 

## 2.最接近的三数之和

给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.

与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).

**代码**

	class Solution:
	    def threeSumClosest(self, nums, target):
	        """
	        :type nums: List[int]
	        :type target: int
	        :rtype: int
	        """
	        ll = len(nums)
	        nums.sort()
	        result = nums[0] + nums[1] + nums[2]
	        for i in range(ll-2):
	            l, r = i + 1,ll-1
	            while l < r:
	                sum1 = nums[i] + nums[l] + nums[r]
	                if sum1 == target:
	                    return sum1
	                if abs(sum1 - target) < abs(result - target):
	                    result =  sum1
	                if target < sum1:
	                    r -= 1
	                elif target > sum1:
	                    l += 1
	        return result

## 3.四数之和

给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

注意：

答案中不可以包含重复的四元组。

示例：

	给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。
	
	满足要求的四元组集合为：
	[
	  [-1,  0, 0, 1],
	  [-2, -1, 1, 2],
	  [-2,  0, 0, 2]
	]

**代码**

    def fourSum(self, nums, target):
        def findNsum(nums, target, N, result, results):
            ll = len(nums)
            if  ll < N or N < 2 or target < nums[0]*N or target > nums[-1]*N: 
                return
            if N == 2: 
                l,r = 0,ll-1
                while l < r:
                    s = nums[l] + nums[r]
                    if s == target:
                        results.append(result + [nums[l], nums[r]])
                        l += 1
                        while l < r and nums[l] == nums[l-1]:
                            l += 1
                    elif s < target:
                        l += 1
                    else:
                        r -= 1
            else: 
                for i in range(len(nums)-N+1):
                    if i == 0 or (i > 0 and nums[i-1] != nums[i]):
                        findNsum(nums[i+1:], target-nums[i], N-1, result+[nums[i]], results)

        results = []
        nums.sort()
        findNsum(nums, target, 4, [], results)
        return results


## 4.删除排序数组中的重复项
        
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

### 双指针法	        
                
数组完成排序后，我们可以放置两个指针i和j ，其中i是慢指针，而j是快指针。只要nums[i]=nums[j]，我们就增加j 以跳过重复项。

当我们遇到 nums[j] \n  eq nums[i]时，跳过重复项的运行已经结束，因此我们必须把它（nums[j]）的值复制到nums[i+1]。然后递增i，接着我们将再次重复相同的过程，直到j 到达数组的末尾为止。
            
**代码：**

	class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        i = 0
        if len(nums) == 0:
            return 0
        for j in range(1, len(nums)):
            if nums[i] != nums[j]:
                i += 1
                nums[i] = nums[j]  
        return i + 1    


## 5.移除元素


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

### 双指针法

当我们遇到 nums[i] = val 时，我们可以将当前元素与最后一个元素进行交换，并释放最后一个元素。这实际上使数组的大小减少了 1。

**代码：**
	
	class Solution:
	    def removeElement(self, nums, val):
	        """
	        :type nums: List[int]
	        :type val: int
	        :rtype: int
	        """
	        n = len(nums)
	        i = 0
	        while i < n:
	            if nums[i] == val:
	                nums[i] = nums[n-1]
	                n -= 1
	            else:
	                i += 1
	        return n              
                              
## 6.下一排序

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。

	1,2,3 → 1,3,2
	3,2,1 → 1,2,3
	1,1,5 → 1,5,1

**字典排序：**

对于数字1、2、3......n的排列，不同排列的先后关系是从左到右逐个比较对应的数字的先后来决定的。例如对于5个数字的排列 12354和12345，排列12345在前，排列12354在后。按照这样的规定，5个数字的所有的排列中最前面的是12345，最后面的是 54321。

### 一遍扫描

![](https://i.imgur.com/tuXJeop.gif)

**代码：**

	class Solution:
	    def nextPermutation(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: void Do not return anything, modify nums in-place instead.
	        """
	        i = len(nums) - 2
	        w hile (i >= 0) and nums[i] >= nums[i+1]:
	            i -= 1
	        if i >= 0:
	            j = len(nums) - 1
	            while (j >= 0) and nums[j] <= nums[i]:
	                j -= 1
	            nums[i], nums[j] = nums[j], nums[i]
	        
	        k = len(nums) - 1
	        i = i + 1
	        while (i < k):
	            nums[i], nums[k] = nums[k], nums[i]
	            i += 1
	            k -= 1


## 7.搜索旋转排序数组

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 O(log n) 级别。

示例 1:

	输入: nums = [4,5,6,7,0,1,2], target = 0
	输出: 4
示例 2:

	输入: nums = [4,5,6,7,0,1,2], target = 3
	输出: -1

**代码**

	class Solution:
	    def search(self, nums, target):
	        """
	        :type nums: List[int]
	        :type target: int
	        :rtype: int
	        """
	        k = range(len(nums)) 
	        d = dict(zip(nums,k))
	        if target in d.keys():
	            return d.get(target)
	        else:
	            return -1

## 8.插入搜索位置 

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

**代码**

	class Solution:
    def searchInsert(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        if nums[len(nums)-1] < target:
                return len(nums)
        for i in range(len(nums)):
            if nums[i] == target:
                return i
            if target < nums[i]:
                return i
            

## 9.组合总和

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：

所有数字（包括 target）都是正整数。
解集不能包含重复的组合。 
示例 1:

	输入: candidates = [2,3,6,7], target = 7,
	所求解集为:
	[
	  [7],
	  [2,2,3]
	]
示例 2:

	输入: candidates = [2,3,5], target = 8,
	所求解集为:
	[
	  [2,2,2,2],
	  [2,3,3],
	  [3,5]
	]

**代码**

class Solution:
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """        
        if(target < 0):
            return []
        if(target == 0):
            return [[]]
        return [[candidates[i]] + left
               for i in range(len(candidates))
               for left in self.combinationSum(candidates[i:], target - candidates[i])]


## 10. 缺失的第一个正数

给定一个未排序的整数数组，找出其中没有出现的最小的正整数。

示例 1:

	输入: [1,2,0]
	输出: 3
示例 2:

	输入: [3,4,-1,1]
	输出: 2
示例 3:

	输入: [7,8,9,11,12]
	输出: 1
说明:

你的算法的时间复杂度应为O(n)，并且只能使用常数级别的空间。

**代码**
	
	class Solution:
	    def firstMissingPositive(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: int
	        """
	        if len(nums) == 0:
	            return 1
	        dict1 = dict(zip(nums, range(len(nums))))
	        for i in range(1,max(nums)+1):
	            if i in dict1.keys():
	                continue
	            return i
	        return max(nums)+1

## 11. 接雨水

给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![](https://i.imgur.com/NkFDJCn.png)

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。

示例:

	输入: [0,1,0,2,1,0,1,3,2,1,2,1]
	输出: 6


**代码**

	class Solution:
	    def trap(self, height):
	        """
	        :type height: List[int]
	        :rtype: int
	        """
	        l = len(height)
	        if l == 0:
	            return 0
	        total = 0
	        left_max = [0]*l
	        right_max = [0]*l
	        left_max[0] = height[0]
	        for i in range(1, l):
	            left_max[i] = max(height[i], left_max[i-1])
	            
	        right_max[l-1] = height[l-1]
	        for i in range(l-2, -1, -1):
	            right_max[i] = max(height[i], right_max[i+1])
	        for i in range(0,l-1):
	            total += min(left_max[i],right_max[i]) - height[i]
	        return total


## 12. 跳跃游戏Ⅱ

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。

示例:

	输入: [2,3,1,1,4]
	输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
说明:

假设你总是可以到达数组的最后一个位置。

**代码**

	class Solution:
	    def jump(self, nums):
	        """
	        Greedy implementation maximizing the length of each jump.
	        :type nums: List[int]
	        :rtype: int
	        """
	        current_index = 0
	        target_index = len(nums) - 1 
	        total_jumps = 0
	        
	        while current_index < target_index:
	            total_jumps += 1
	            jump_range = range(current_index, current_index + nums[current_index] + 1) 
	            if target_index in jump_range:
	                break
	                
	            next_index = current_index
	            for next_index_candidate in jump_range:               
	                candidate_jump_destination = next_index_candidate + nums[next_index_candidate]
	                next_jump_destination = next_index + nums[next_index]
	                if next_jump_destination < candidate_jump_destination :
	                    next_index = next_index_candidate
	
	            current_index = next_index
	
	        return total_jumps

## 13. 图像旋转

给定一个 n × n 的二维矩阵表示一个图像。

将图像顺时针旋转 90 度。

说明：

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

示例 1:
	
	给定 matrix = 
	[
	  [1,2,3],
	  [4,5,6],
	  [7,8,9]
	],
	
	原地旋转输入矩阵，使其变为:
	[
	  [7,4,1],
	  [8,5,2],
	  [9,6,3]
	]
示例 2:

	给定 matrix =
	[
	  [ 5, 1, 9,11],
	  [ 2, 4, 8,10],
	  [13, 3, 6, 7],
	  [15,14,12,16]
	], 
	
	原地旋转输入矩阵，使其变为:
	[
	  [15,13, 2, 5],
	  [14, 3, 4, 1],
	  [12, 6, 8, 9],
	  [16, 7,10,11]
	]


**代码**

	class Solution:
	    def rotate(self, matrix):
	        """
	        :type matrix: List[List[int]]
	        :rtype: void Do not return anything, modify matrix in-place instead.
	        """
	        n = len(matrix)-1
	        #交换第i行和第n-i行
	        for i in range(len(matrix)//2):
	                matrix[i][:], matrix[n-i][:] = matrix[n-i][:], matrix[i][:]
	        #将矩阵进行装置
	        for i in range(len(matrix)):
	            for j in range(i+1, len(matrix)):
	                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
	        
## 14. 最大子序和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:

	输入: [-2,1,-3,4,-1,2,1,-5,4],
	输出: 6
	解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
进阶:

如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

**C++实现**

	class Solution {
	public:
	    int maxSubArray(vector<int>& nums) {
	        int n = nums.size();
	        int *dp = new int[n];
	        dp[0] = nums[0];
	        int max = dp[0];
	        for (int i = 1; i < n; i++)
	        {
	            dp[i] = nums[i] + (dp[i-1] > 0 ? dp[i-1] : 0);
	            max = max > dp[i] ? max : dp[i];
	        }
	        return max;
	    }
	};

## 15 螺旋矩阵

给定一个包含 m x n 个元素的矩阵（m 行, n 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。

示例 1:

	输入:
	[
	 [ 1, 2, 3 ],
	 [ 4, 5, 6 ],
	 [ 7, 8, 9 ]
	]
	输出: [1,2,3,6,9,8,7,4,5]
示例 2:

	输入:
	[
	  [1, 2, 3, 4],
	  [5, 6, 7, 8],
	  [9,10,11,12]
	]
	输出: [1,2,3,4,8,12,11,10,9,5,6,7]

**思路**

一层一层输出，先输出第一行，最后一列，最后一行，第一列。然后进入第二层，采用通过的顺序输出。

![](https://i.imgur.com/RRuWyQ4.png)

**C++实现**

	class Solution {
	public:
	    vector<int> spiralOrder(vector<vector<int>>& matrix) {
	        if (matrix.empty()) return {};
	        int j = 0;
	        int r1 = 0;
	        int r2 = matrix.size()-1;
	        int c1 = 0;
	        int c2 = matrix[0].size()-1;
	        int totalnums = (r2+1) * (c2+1);
	        vector<int> p(totalnums);
	        int k = 0;
	        while (r1 <= r2 and c1 <= c2)
	        {
	            for (int i = c1; i <= c2; i++) p[k++] = matrix[r1][i];
	            for (int j = r1 + 1; j <= r2; j++) p[k++] = matrix[j][c2];
	            if (r1 < r2 && c1 < c2)
	            {
	                cout << '1' << endl;
	                for (int i = c2 - 1; i > c1; i--) p[k++] = matrix[r2][i];
	                for (int j = r2; j > r1; j--) p[k++] = matrix[j][c1];
	            }
	            r1++;
	            r2--;
	            c1++;
	            c2--;  
	        }
	        return p;
	    }
	};

## 16 跳跃游戏

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

示例 1:

	输入: [2,3,1,1,4]
	输出: true
	解释: 从位置 0 到 1 跳 1 步, 然后跳 3 步到达最后一个位置。

示例 2:

	输入: [3,2,1,0,4]
	输出: false
	解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。