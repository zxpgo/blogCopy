---
title: 剑指offer-面试中的各项能力
date: 2019-11-01 13:28:50
tags:  面试题目
mathjax: true
---



剑指offer第六章的题目练习和重要知识点。<!--more-->

# 知识迁移能力

## 在排序数组中查找数字

### 题目描述

统计一个数字在排序数组中出现的次数。

### 思路分析

通过二分查找找到该数字第一次出现的位置，以及

### 代码实现

```c++
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        int first = FindFirstK(data, k);
        int last = FindLastK(data, k);
        if (first == -1 || last == -1)
            return 0;
        return last - first + 1;
    }
    int FindFirstK(const vector<int> data, int k)
    {
        int left = 0;
        int right = data.size() - 1;
        int mid;
        while (left <= right)
        {
            mid = (right + left) / 2 ;
            if (data[mid] == k)
            {
                if (mid == 0 || (mid > 0 && data[mid - 1] != k))
                    return mid;
                else
                    right = mid - 1;
            }
            else if (data[mid] > k)
                right = mid - 1;
            else 
                left = mid + 1;
        }
        return -1;
    }

    int FindLastK(const vector<int> data, int k)
    {
        int left = 0;
        int right = data.size() - 1;
        int mid;
        while (left <= right)
        {
            mid = (right + left) / 2;
            if (data[mid] == k)
            {
                if (mid == data.size()-1 || (mid < data.size() - 1 && data[mid + 1] != k))
                    return mid;
                else
                    left = mid + 1;
            }
            else if (data[mid] < k)
                left = mid + 1;
            else
                right = mid - 1;
        }
        return -1;
    }
};
```

## 0~n-1中缺失的数字

### 题目描述

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在0~n-1之内。在范围0~n-1内的n个数字有且仅有一个数字不在该数组中，请查找这个数字。

### 思路分析

最初可能想到，求出0~n-1的和，然后减去数组中所有元素的，得到的结果就是缺失的数字。但是这样没有利用数组的递增特性。

于是考虑采用二分法查找。判断中间元素与对应下标的关系。

### 代码实现

```c++
#include <iostream>
#include <vector>
using namespace std;
int FindLost(const vector<int>& vec)
{
	int start = 0;
	int end = vec.size() - 1;
	while (start <= end)
	{
		int mid = (start + end) / 2;
		if (vec[mid] != mid)
		{
			if (mid == 0 || vec[mid - 1] == mid - 1)
				return mid;
			end = mid - 1;
		}
		else
			start = mid + 1;
	}

	if (end == vec.size())
		return vec.size();
	return -1;
}
int main()
{
	vector<int> vec = {0,1,3,4};
	cout << FindLost(vec) << endl;
    
}
```

## 数组中数值和下标相等的元素

### 题目描述

假设一个单调递增的数组里的每个元素都是整数并且是唯一的。请编程实现一个函数，找出数组中任意一个数值等于其下标的元素。例如，在数组{0, 3, 4, 5, 6}中，数字0和它的下标相等。

### 思路分析

直接二分法实现，当遇到当前下标和当前的值相等，直接返回。

如果下标大于当前值，则取后半部分；否则取前半部分。

### 代码实现

```c++
#include <iostream>
#include <vector>
using namespace std;
int FindIndexEqValue(const vector<int> & vec) {
	if (vec.size() <= 0)
		return -1;
	int start = 0;
	int end = vec.size();
	while (start <= end)
	{
		int mid = (start + end) / 2;
		if (mid == vec[mid])
			return vec[mid];
		else if (mid > vec[mid])
			start = mid + 1;
		else
			end = mid - 1;
	}
	return -1;
}
int main()
{
	vector<int> vec = {0, 3, 4, 5, 6};
	cout << FindIndexEqValue(vec) << endl;
	return 0;
}
```

## 二叉搜索树的第K个结点

### 题目描述

给定一棵二叉搜索树，请找出其中的第k小的结点。例如， （5，3，7，2，4，6，8）  中，按结点数值大小顺序第三小结点的值为4。

### 思路分析

通过中序遍历实现。

### 代码实现

```c++
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};
*/
class Solution {
public:
    TreeNode* KthNode(TreeNode* pRoot, int k)
    {
        if (pRoot == nullptr || k == 0)
            return nullptr;
        return KthNodeCore(pRoot, k);
    }
    
    TreeNode * KthNodeCore(TreeNode * pRoot, int & k)
    {
        TreeNode * target = nullptr;
        if (pRoot->left != nullptr)
            target = KthNodeCore(pRoot->left, k);
        if (target == nullptr)
        {
            if (k == 1)
                target = pRoot;
            k--;
        }
        if (target == nullptr && pRoot->right != nullptr)
            target = KthNodeCore(pRoot->right, k);
        return target;
    }
};
```

## 二叉树的深度

### 题目描述

 输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。 

### 思路分析

递归实现。

### 代码实现

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if (nullptr == pRoot)
            return 0;
        return max(TreeDepth(pRoot->left), TreeDepth(pRoot->right)) + 1;
    }
};
```

## 平衡二叉树

### 题目描述

 输入一棵二叉树，判断该二叉树是否是平衡二叉树。 

### 思路分析

求出左子树和右子树的深度，然后判断两者之差是否小于1。如果大于1，则直接返回false，否则递归判断左子树和右子树。

### 代码实现

```c++
class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        if (nullptr == pRoot)
            return true;
        int left = DepthOfTree(pRoot->left);
        int right = DepthOfTree(pRoot->right);
        if (abs(left - right) > 1)
            return false;
        return IsBalanced_Solution(pRoot->left) && IsBalanced_Solution(pRoot->right);
    }
    
    int DepthOfTree(TreeNode * pRoot)
    {
        if (nullptr == pRoot)
            return 0;
        return max(DepthOfTree(pRoot->left), DepthOfTree(pRoot->right)) + 1;
    }
};
```

上面的方案需要重复遍历同一个结点。如果采用后序遍历的方式遍历二叉树的每个结点，那么在遍历到一个结点之前我们就已经遍历了它的左右子树。只要在遍历的时候记录它的深度，我们就可以一边遍历一边判断每个结点是不是平衡的。

```c++
class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        if (nullptr == pRoot)
            return true;
        int depth = 0;
        return IsBalanced(pRoot, depth);
    }
    
    bool IsBalanced(TreeNode * pRoot, int & depth)
    {
        if (nullptr == pRoot)
        {
            depth = 0;
            return true;
        }
        
        int left=0, right=0;
        if (IsBalanced(pRoot->left, left) && IsBalanced(pRoot->right, right))
        {
            if (abs(left - right) <= 1)
            {
                depth = 1 + (left > right ? left : right);
                return true;
            }
        }
        return false;
    }
};
```

## 数组中数字出现的次数

### 题目描述

一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

### 思路分析

首先，求整个数组中所有数字的异或，相同的数字异或等于0，所以得到的结果就是只出现一次的两个数字的异或。

然后，求出异或结果中第一个为1位，该位为1，表示两个数字中对应位，一个为1，一个为0，借此就可以区分出两个只出现一次的数字。注意，如果有两个数该位都为1，下一步继续异或就会抵消掉。

最后，根据第二步中求出的可以区分两个数字的那个位数（n），将每个元素右移n，然后判断是否为1，如果等于1，则将其与num1异或，否则与num2异或。对于出现两次的元素，会同时跟num1或者num2异或两次，因此会抵消。

### 代码实现

```c++
class Solution {
public:
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        if (data.size() <= 0)
            return ;
        
        int res = data[0];
        for (int i = 1; i < data.size(); ++i)
            res ^= data[i];
       
        int count = 0;
        while(((res & 1) == 0) && (count < 8 * sizeof(int)))
        {
            res = res >> 1;
            ++count;
        }
        
        *num1 = 0, *num2 = 0;
        for (int i = 0; i < data.size(); ++i)
        {
            int temp = data[i] >> count;
            if (temp & 1 == 1)
                *num1 ^= data[i];
            else
                *num2 ^= data[i];
        }
    }
};
```

## 数组中唯一只出现一次的数字

### 题目描述

在一个数组中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

### 思路分析

统计数组中所有数字在每个位上1出现的次数，如果只出现3次，表示只出现一次的数字该位我0，如果某位出现了4次，或者1次，则表示只出现一次的数字该为1。

首先统计每位出现的次数，通过一个数组来记录，然后将每位进行模3运算，并随着位运算的继续不断将其左移。

### 代码实现

```c++
#include <iostream>
#include <vector>
using namespace std;
bool FindNumberAppearingOnce(vector<int> data, int & res)
{
	if (data.size() <= 0)
		return false;
	int bitSum[32] = { 0 };
	for (int i = 0; i < data.size(); ++i)
	{
		int bitMask = 1;
		for (int j = 31; j >= 0; --j)
		{
			int bit = data[i] & bitMask;
			if (bit != 0)
				bitSum[j] += 1;
			bitMask = bitMask << 1;
		}
	}

	res = 0;
	for (int i = 0; i < 32; ++i)
	{
		res = res << 1;
		res += bitSum[i] % 3;
	}
	return true;
}
int main()
{
	vector<int> vec = {31,31,31,2};
	int res = 0;
	FindNumberAppearingOnce(vec, res);
	cout << res << endl;
	return 0;
}

```

## 和为S的数字

### 题目描述

输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

注意：

```
对应每个测试案例，输出两个数，小的先输出。
```

### 思路分析

声明两个指针，分别指向数组的开头和末尾，如果两者之和大于S，则末尾的指针往前移动；如果两者之和小于S，则开头的指针先后移动；否则两者之等于S，则将结果放入数组中，并结束循环。

### 代码实现

```c++
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        vector<int> res;
        if (array.size() < 2)
            return res;
        int start = 0, end = array.size()-1;
        while (start < end)
        {
            int temp = array[start] + array[end];
            if (temp < sum)
                ++start;
            else if (temp > sum)
                --end;
            else
            {
                res.push_back(array[start]);
                res.push_back(array[end]);
                break;
            }
        }
        return res;
    }
};
```

## 和为S的连续正数序列

### 题目描述

小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

注意：

```
输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序
```

### 思路分析

跟上一题思路相同，定义两个指针，一个指向序列开头start，初始化为1，另一个指向序列末尾end，初始化为2。并定一个变量记录当前的和curSum。

第零步：判断是否start小于S的一半(S+1)/2。

第一步：如果当前序列的和curSum等于S，则将结果加入结果集中；

第二步：如果序列的和大于S，则将开头start，往后移动，并将每次跳过的start值从curSum减去，每次都判断是否curSum的值是否等于S；

第三步：否则将末尾的指针end，向后移动，并且将每次新的end值加入curSum，转到第零步。

### 代码实现

```c++
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int> > res;
       
        int start = 1;
        int end = 2;
        int curSum = start + end;
        int middle = (sum + 1) / 2;
        while (start < middle)
        {
            if (curSum == sum)
                pushInVector(start, end, res);
            while (curSum > sum && start < middle)
            {
                curSum -= start;
                start++;
                if (curSum == sum)
                    pushInVector(start, end, res);
            }
            end++;
            curSum += end;
        }
        return res;

    }
    void pushInVector(int start, int end, vector<vector<int> > & res)
    {
        vector<int> temp;
        for (int i = start; i <= end; ++i)
        {
            temp.push_back(i);
        }
        res.push_back(temp);
    }
};
```

## 翻转字符串

### 体面描述

 牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？ 

### 思路分析

首先将整个字符串进行翻转，然后对每个单词进行翻转。

### 代码实现

```c++
class Solution {
    public:
    void swapChar(char& a, char& b)
    {
        char temp;
        temp = a;
        a = b;
        b = temp;
    }
    string ReverseSentence(string str) {
        int len = str.size();
        string res;
        if (len <= 0)
            return res;
        for (int i = 0; i < (len + 1) / 2; ++i) //对整个字符串进行翻转
            swap(str[i], str[len - i - 1]);
        int start = 0, end = 0;
        while (end-1 != len)  //对每个单词再一次进行翻转
        {
            if (str[start] == ' ')
            {
                ++start;
                ++end;
            }
            else if (str[end] == ' ' || str[end] == '\0')
            {
                for (int i = 0; i <  (end - start + 1) / 2 ; ++i)
                    swapChar(str[i+start], str[end - i - 1]);
                start = ++end;
            }
            else {
                ++end;
            }
        }
        return str;
    }
};

```

## 左旋转字符串

### 题目描述

 汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！ 

### 思路分析

首先，对整个字符串进行翻转。

然后，对前len-n个字符串进行翻转。（len为整个字符串的长度）

最后，对后n个字符串进行翻转。

### 代码实现

```c++
class Solution {
public:
    void Swap(char& lhs, char& rhs)
    {
        char temp;
        temp = lhs;
        lhs = rhs;
        rhs = temp;
    }

    void Rotate(string& str, int len)
    {
        char temp;
        for (int i = 0; i < (len + 1) / 2; ++i)
        {
            Swap(str[i], str[len - i - 1]);
        }
    }

    string LeftRotateString(string str, int n) {
        string res;
        if (str.size() <= 0)
            return res;
        if (n == 0)
            return str;
        Rotate(str, str.size());  //对整个字符串进行翻转
        Rotate(str, str.size()-n); //对除了后n个字符的前面所有字符进行翻转
        for (int i = 0; i < n/2; ++i)  //对后面的n个字符进行翻转
        {
            Swap(str[i+str.size()-n], str[str.size()-i-1]);
        }
        //Rotate(str, str.size() - n);
        return str;
    }
};
```

## 队列的最大值

### 题目一：滑动窗口的最大值

#### 题目描述

给定一个数组和滑动窗口的大小，请找出所有滑动窗口里的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小为3，那么一共存在6个滑动窗口，它们的最大值分别为{4，4，6，6，6，5}。

#### 思路分析

第一种方法：死办法，直接求出每个窗口中的最大值。

第二种办法，通过一个队列实现。队列记录当前最大值的下标。

以{2,3,4,2,6,2,5,1}为例：

首先，将2的下标添加队列中，即0；下标指向3时，3大于2，将2对应的下标从队列中删除，并将3的下标1加入队列中，同样下标指向4时，4大于3，将3对应的下标从队列中删除，并将4的下标2加入队列中。这是完成了第一个窗口操作。

然后，将第一个窗口的最大值，即当前队列中最前面下标对应的值，添加到结果数组中。继续，判断如果下标指向的元素是否大于队列最末尾的值。即2小于4，将2对应的下标加入队列，并将队列的最前面下标对应的元素添加到结果数组中；继续6大于2，所以将2对应的下标(即3)从队列中删除，继续4小于6，将4对应的下标(即2)从队列中删除，此时队列为空，将6对应的下标加入到队列中，并将队列最前面的下标对应的元素（即6）加入到结果数组中。依此类推。其中还要注意每次判断是否队列中最前面的元素和当前下标之差是否大于size（窗口大小），如果大于要将队列的最前面元素pop出去，这是为了保证窗口大小固定为size。

#### 代码实现

```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;
vector<int> maxInWindow(vector<int> arr, int size)
{
	vector<int> res;
	int max = INT_MIN;
	if (arr.size() <= 0 || size == 0)
		return res;
	if (arr.size() <= size)  //当数组长度小于窗口大小时，直接获取数组中的最大值返回
	{
		for (int i = 0; i < arr.size(); ++i)
			max = max < arr[i] ? arr[i] : max;
		res.push_back(max);
		return res;
	}
	int i = 0;
	for  (i = 0; i < size; ++i)   //获取第一个窗口的最大值
		max = max < arr[i] ? arr[i] : max;
	res.push_back(max);
	while (i < arr.size())
	{
		if (arr[i] > max)      //当新加入窗口的值大于前一个窗口的最大值，则更新
			max = arr[i];
		else if (arr[i - size] == max) //当新的窗口的最大值是之前离开窗口的那个值，则在新的窗口中重新计算最大值
		{
			max = INT_MIN;
			for (int j = i-size+1; j <= i; ++j)
				max = max < arr[j] ? arr[j] : max;
		}
		res.push_back(max);  //将当前窗口最大值添加到结果集中
		++i;
	}
	return res;
}
int main()
{
	vector<int> res = maxInWindow({2,4}, 1);
	for (int i = 0; i < res.size(); ++i)
		cout << res[i] << endl;
	return 0;
}
```

进阶版：

```c++
#include <iostream>
#include <vector>
#include <string>
#include <deque>
using namespace std;
vector<int> maxInWindow(vector<int> arr, int size)
{
	vector<int> res;

	if (arr.size() >= size && size >= 1)
	{
		deque<int> index;
		for (int i = 0; i < size; ++i)
		{
			while (!index.empty() && arr[i] >= arr[index.back()])
				index.pop_back();
			index.push_back(i);
		}
		for (int i = size; i < arr.size(); ++i)
		{
			res.push_back(arr[index.front()]);

			while (!index.empty() && arr[i] >= arr[index.back()])
				index.pop_back();
			if (!index.empty() && (index.front() <= (i - size)))
				index.pop_front();

			index.push_back(i);
		}
		res.push_back(arr[index.front()]);
	}
	return res;
}
int main()
{
	vector<int> res = maxInWindow({2,3,4},2);
	for (int i = 0; i < res.size(); ++i)
		cout << res[i] << endl;
	return 0;
}
```

### 题目二： 队列的最大值

#### 题目描述

请定义一个队列并实现函数max得到队列里的最大值，要求函数`max、push_back、pop_front`的时间复杂度都是o(1)。

#### 思路分析

在类中定义两个deque，一个记录放入队列的值，另一个目前队列的最大值。

#### 代码实现

```c++
#include <iostream>
#include <deque>
using namespace std;

template<typename T>
class QueueWithMax
{
public:
	QueueWithMax() : currentIndex(0) {}

	T max() {
		if (maxinums.empty())
			throw new exception("queue is empty");

		return maxinums.front().number;
	}

	void pop_front(){
		if (maxinums.empty())
			throw new exception("queue is empty");

		if (data.front().number == maxinums.front().number)
			maxinums.pop_front();
		data.pop_front();
	}

	void push_back(T number) {
		while (!maxinums.empty() && number >= maxinums.back().number)
			maxinums.pop_back();

		InternalData internalData = { number, currentIndex };
		data.push_back(internalData);
		maxinums.push_back(internalData);
		++currentIndex;
	}
	
private:

	struct InternalData {
		T number;
		int index;
	};
	deque<InternalData> data;
	deque<InternalData> maxinums;
	int currentIndex;
};
int main()
{
	QueueWithMax<int> q;
	q.push_back(4);
	q.push_back(1);
	cout << q.max() << endl;
	q.push_back(2);
	cout << q.max() << endl;
	q.pop_front();
	cout << q.max() << endl;

	return 0;
}
```

# 抽象建模能力

## n个骰子的点数

### 题目描述

把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出来所有可能的值出现的概率。

### 思路分析

两种实现方式，基于递归和基于循环的实现。

对于基于递归的实现，通过递归计算处每个点数出现的次数。

对于基于循环的实现，声明两个数组。在一轮中，第一个数组中的第n个数表示骰子和为n的出现次数。在下一轮中，我们加上上一个新的骰子，此时和为n的骰子出现的次数应该等于上一轮循环中骰子点数为n-1,n-2,n-3,n-4,n-5,n-6的次数总和。

### 代码实现

基于递归的实现：

````c++
#include <iostream>
using namespace std;
const int g_maxvalue = 6;

void Probability(int number, int * pProbabilities);
void Probability(int original, int current, int sum, int * pProbabilities);

void PrintProbability(int number)
{
	
	if (number < 1)
		return;
	int maxSum = g_maxvalue * number;
	int * probabilities = new int[maxSum - number + 1];
	for (int i = 0; i < maxSum - number + 1; ++i)
		probabilities[i] = 0;
	Probability(number, probabilities);
	int total = pow((double)(g_maxvalue), number);
	for (int i = 0; i < maxSum - number + 1; ++i) {
		double ratio = (double)(probabilities[i]) / total;
		cout << i + number << ": " << ratio << endl;
	}
}

void Probability(int number, int * pProbabilities)
{
	for (int i = 1; i <= g_maxvalue; ++i)
		Probability(number, number, i, pProbabilities);
}

void Probability(int original, int current, int sum, int * pProbabilities) {
	if (current == 1)
		pProbabilities[sum - original]++;
	else
		for (int i = 1; i <= g_maxvalue; ++i)
			Probability(original, current - 1, i + sum, pProbabilities);
}


int main()
{
	PrintProbability(2);
	return 0;
}
````

基于循环的实现：

```c++
#include <iostream>
using namespace std;

void PrintProbability(int number)
{
	int g_maxvalue = 6;
	int sum = g_maxvalue * number;
	int * probabilities[2];
	probabilities[0] = new int[sum + 1];
	probabilities[1] = new int[sum + 1];
	for (int i = 0; i < sum + 1; ++i) {
		probabilities[0][i] = 0;
		probabilities[1][i] = 0;
	}

	int flag = 0;	
	for (int i = 1; i <= g_maxvalue; ++i)
		probabilities[flag][i] = 1;

	for (int k = 2; k <= number; ++k)
	{
		for (int i = 0; i < k; ++i) //将前面k-1个元素初始化为0，因为有k个骰子时，不会出现小于k的点数
			probabilities[1 - flag][i] = 0;

		for (int i = k; i <= g_maxvalue * k; ++i)  //从点数为k开始到，6*k
		{
			probabilities[1 - flag][i] = 0;       //将每个点数初始化为0
			for (int j = 1; j <= i && j <= g_maxvalue; ++j) { //计算上一轮中n-1,n-2,n-3,n-4.n-5,n-6出现的次数总和
				probabilities[1 - flag][i] += probabilities[flag][i - j];
			}
		}
		flag = 1 - flag;
	}

	int total = pow((double)(g_maxvalue), number);
	for (int i = number; i <= sum; ++i) {
		double ratio = (double)(probabilities[flag][i]) / total;
		cout << i  << ": " << ratio << endl;
	}
	delete[] probabilities[0];
	delete[] probabilities[1];

}

int main()
{
	PrintProbability(2);
	return 0;
}
```

## 扑克牌中的顺子

### 题目描述

从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2~10为数字本身，A为1，J为11，Q为12，K为13，而大、小王可以看成任意数字。

### 思路分析

通过比较数组中0的个数和空缺数的个数。如果两者相等，则表示可以组成顺子。否则无法组成顺子。

比如：[2,4,5,6,0]，首先对数组进行排序，得到[0,2,4,5,6]，其中0的个数为1，2~6少了一个数字3，所以空缺数的个数为1，两者相等，则可以组成顺子，即将0替换为3。

### 代码实现

```c++
class Solution {
public:
    bool IsContinuous( vector<int> numbers ) {
        if (numbers.size() < 5)
            return false;
        int len = numbers.size();
        sort(numbers.begin(), numbers.end());
        int numberOfZero = 0;
        int numberOfGap = 0;
        for (int i = 0; i < len && numbers[i] == 0; ++i)
        {
            numberOfZero += 1;
        }
        
        int start = numberOfZero;
        int end = start + 1;
        while (end < len)
        {
            if (numbers[end] == numbers[start])
                return false;
            numberOfGap += numbers[end] - numbers[start] - 1;
            start = end;
            end++;
        }
        
        return (numberOfGap > numberOfZero) ? false : true;
    }
};
```

## 圆卷中最后剩下的数字

### 题目描述

0,1,...,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

### 思路分析

首先，构造一个链表，然后每次从链表中删除第m个数字。此处采用链表list，因为list的删除元素的效率最高。删除直到链表中只有一个元素，此时就是剩下的最后一个数字。

本题就是有名的约瑟夫环问题。既然题目有一个数字圆圈，很自然的想法是我们用一个数据结构来模拟这个圆圈。在常用的数据结构中，我们很容易想到用环形列表。我们可以创建一个总共有m个数字的环形列表，然后每次从这个列表中删除第m个元素。

在参考代码中，我们用STL中std::list来模拟这个环形列表。由于list并不是一个环形的结构，因此每次跌代器扫描到列表末尾的时候，要记得把跌代器移到列表的头部。这样就是按照一个圆圈的顺序来遍历这个列表了。

这种思路需要一个有n个结点的环形列表来模拟这个删除的过程，因此内存开销为O(n)。而且这种方法每删除一个数字需要m步运算，总共有n个数字，因此总的时间复杂度是O(mn)。当m和n都很大的时候，这种方法是很慢的。

接下来我们试着从数学上分析出一些规律。首先定义最初的n个数字（0,1,…,n-1）中最后剩下的数字是关于n和m的方程为f(n,m)。

在这n个数字中，第一个被删除的数字是(m-1)%n，为简单起见记为k。那么删除k之后的剩下n-1的数字为0,1,…,k-1,k+1,…,n-1，并且下一个开始计数的数字是k+1。相当于在剩下的序列中，k+1排到最前面，从而形成序列k+1,…,n-1,0,…k-1。该序列最后剩下的数字也应该是关于n和m的函数。由于这个序列的规律和前面最初的序列不一样（最初的序列是从0开始的连续序列），因此该函数不同于前面函数，记为f’(n-1,m)。最初序列最后剩下的数字f(n,m)一定是剩下序列的最后剩下数字f’(n-1,m)，所以f(n,m)=f’(n-1,m)。

接下来我们把剩下的的这n-1个数字的序列k+1,…,n-1,0,…k-1作一个映射，映射的结果是形成一个从0到n-2的序列：

k+1  ->  0
k+2  ->  1
…
n-1  ->  n-k-2
0  ->  n-k-1
…
k-1  ->  n-2

把映射定义为p，则p(x)= (x-k-1)%n，即如果映射前的数字是x，则映射后的数字是(x-k-1)%n。对应的逆映射是p-1(x)=(x+k+1)%n。

由于映射之后的序列和最初的序列有同样的形式，都是从0开始的连续序列，因此仍然可以用函数f来表示，记为f(n-1,m)。根据我们的映射规则，映射之前的序列最后剩下的数字f’(n-1,m)= p-1 [f(n-1,m)]=[f(n-1,m)+k+1]%n。把k=(m-1)%n代入得到f(n,m)=f’(n-1,m)=[f(n-1,m)+m]%n。

经过上面复杂的分析，我们终于找到一个递归的公式。要得到n个数字的序列的最后剩下的数字，只需要得到n-1个数字的序列的最后剩下的数字，并可以依此类推。当n=1时，也就是序列中开始只有一个数字0，那么很显然最后剩下的数字就是0。我们把这种关系表示为：
$$
f(n,m)=\begin{cases}  0, n=1 \\ [f(n-1,m)+m]\%n, n>1 \\ \end{cases}
$$
尽管得到这个公式的分析过程非常复杂，但它用递归或者循环都很容易实现。最重要的是，这是一种时间复杂度为O(n)，空间复杂度为O(1)的方法，因此无论在时间上还是空间上都优于前面的思路。

### 代码实现

```c++
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if (n < 1 || m < 1)
            return -1;
        list<int> arr;
        for (int i = 0; i < n; ++i){
            arr.push_back(i);
        }
        
        auto cur = arr.begin();
        while(arr.size() > 1)
        {
            for (int i = 1; i < m; ++i)
            {
                cur++;
                if (cur == arr.end())
                    cur = arr.begin();
            }
            auto next = ++cur;
            if (next == arr.end())
                next = arr.begin();
            arr.erase(--cur);
            cur = next;
        }
        return *(arr.begin());
    }
};
```

方法二：

```c++
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if (n < 1 || m < 1)
            return -1;
        int last = 0;
        for (int i = 2; i <= n; i++)
            last = (last + m) % i;
        return last;
    }
};
```

## 股票的最大利润

### 题目描述

假设把某股票的价格按照时间先后顺序存储在数组中，请问卖该股票依次可能获得的最大利润是多少？例如，一只股票在某些时间节点的价格为{9，11，8，5，7，12，16，14}。如果我们能在价格为5的时候买入并在价格为16时卖出，则能收获最大的利润11。

### 思路分析

寻找数组中的最大差距。可以看成一个找谷底和谷峰的问题。但是谷峰必须出现在谷底之后。

首先，通过一个变量存储当前中的最小值，初始化为第一个元素，并声明一个值存储最大差距，初始化为第二哥元素减去第一个元素。

然后，循环判断从2开始，前一个元素小于当前存储的最小值，如果小于，则更新最小值，并判断当前值减去最小值是否大于最大距离值，如果大于，则更新最大距离值。

### 代码实现

```c++
#include <iostream>
#include <vector>
using namespace std;

int MaxDiff(vector<int> arr)
{
	int len = arr.size();
	if (len < 2)
		return -1;
	int minValue = arr[0];
	int maxDiff = arr[1]-minValue;

	for (int i = 2; i < len; ++i)
	{
		if (arr[i - 1] < minValue)
			minValue = arr[i - 1];
		int curDiff = arr[i] - minValue;
		if (curDiff > maxDiff)
			maxDiff = curDiff;
	}

	return maxDiff;
}

int main()
{
	vector<int> arr = { 9, 21, 8, 5, 7, 20, 16, 19 };
	cout << MaxDiff(arr) << endl;
	return 0;
}
```

# 发散思维能力

## 求1+2+3+...+n

### 题目描述

求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

### 思路分析

首先，想到采用等差数列的求和公式。

第二种方法，采用构造函数来实现，声明n个对象，就会调用n次构造函数，然后将变量设为静态变量，所以对象就公用一个变量。

第三种方法，通过虚函数是来实现。基类用来终止递归，通过判断最好一个数是否为0，来调用基类的虚函数。声明一个指针数组，一个指向基类，一个指向子类。当n=0是，!!n为0，调用基类的虚函数，当n不等于0时，!!n等于1，调用子类的虚函数。

第四种方法，利用函数指针求解。

第五种方法，通过模板类型求解。

### 代码实现

````c++
class Solution {
public:
    int Sum_Solution(int n) {
        return  (1 + n)*n  / 2;
    }
};
````

第二种方法：

```c++
class Temp{
public:
    Temp(){++N; Sum+=N;}
    static void Reset(){N=0; Sum=0;}
    static int getSum(){ return Sum;}
private:
    static int N;
    static int Sum;
};
int Temp::N = 0;  //此处无法初始化，只是给出定义
int Temp::Sum = 0;

class Solution {
public:
    int Sum_Solution(int n) {
        Temp::Reset();              //一定需要首先调用Reset()函数进行初始化
        Temp * t = new Temp[n];   
        delete [] t;
        t = nullptr;
        return Temp::getSum();
    }
};

```

第三种方法：

```c++
class A;
A * Array[2];
class A{
public: 
    virtual int Sum(int n)
    {
        return 0;
    }
};

class B : public A{
public:
    virtual int Sum(int n)
    {
         return  Array[!!n]->Sum(n-1) + n;
    }
};
class Solution {
public:
    int Sum_Solution(int n) {
  
        A a;
        B b;
        Array[0] = &a;
        Array[1] = &b;
        int value = Array[1]->Sum(n);
        return value;
    }
};

```

第四种方法：

```c++
class Solution {
public:
    typedef int (*fun)(int);
    
    static int Solution3_Teminator(int n)
    {
        return 0;
    }

    static int Sum_Solution(int n) {
        static fun f[2] = {Solution3_Teminator,Sum_Solution};
        return n + f[!!n](n-1);
    }
};
```

第五种方法：

```c++
template<int n> struct Sum_Solution4
{
    enum Value{N = (Sum_Solution4<n-1>::N) + n};
};

template<> struct Sum_Solution4<1>
{
    enum Value{N=1};
};

Sum_Solution4<10>::N; //只能通过常量，不能带入n等变量
```

## 不用加减乘除做加法

### 题目描述

写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

### 思路分析

首先看十进制是如何做的： 5+7=12，三步走 

- 第一步：相加各位的值，不算进位，得到2。
- 第二步：计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。 
- 第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。

同样我们可以用三步走的方式计算二进制值相加： 5-101，7-111 
- 第一步：相加各位的值，不算进位，得到010，二进制每位相加就相当于各位做异或操作，101^111。 
- 第二步：计算进位值，得到1010，相当于各位做与操作得到101，再向左移一位得到1010，(101&111)<<1。
-  第三步重复上述两步， 各位相加 010^1010=1000，进位值为100=(010&1010)<<1。 继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。  

### 代码实现

```c++
class Solution {
public:
    int Add(int num1, int num2)
    {
        int sum, carry;
        do
        {
            sum = num1 ^ num2;
            carry = (num1 & num2) << 1;
            num1 = sum;
            num2 = carry;
        }while(num2 != 0);
        return num1;
    }
};
```

## 构建乘积数组

### 题目描述

给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。

### 思路分析

第一种方法，连乘n-1个数，这样的时间复杂度为$o(n^2)$。

第二种方法，链接：https://www.nowcoder.com/questionTerminal/94a4d381a68b47b7a8bed86f2975db46?f=discussion来源：牛客网

 **B[i]的值可以看作下图的矩阵中每行的乘积。** 

  下三角用连乘可以很容求得，上三角，从后向前也是连乘。 

  因此我们的思路就很清晰了，先算下三角中的连乘，即我们先算出B[i]中的一部分，然后倒过来按上三角中的分布规律，把另一部分也乘进去。 

  ![img](https://uploadfiles.nowcoder.com/images/20160829/841505_1472459965615_8640A8F86FB2AB3117629E2456D8C652)

### 代码实现

```c++
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        vector<int> B;
        for (int i = 0; i < A.size(); ++i)
        {
            int temp = 1;
            for (int j = 0; j < A.size(); ++j)
            {
                if (i != j)
                    temp *= A[j];
            }
            B.push_back(temp);
        }
        return B;
    }
};
```

第二种方法：

```c++
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        vector<int> B(A.size(),1);
        B[0] = 1;
        for (int j = 1; j < A.size(); ++j)
        {
            B[j] = B[j-1] * A[j-1];
        }
     
        int temp = 1;
        for (int i = A.size()-2; i >= 0; --i)
        {
            temp *= A[i+1];
            B[i] *= temp;
        }
        return B;
    }
};
/*
链接：https://www.nowcoder.com/questionTerminal/94a4d381a68b47b7a8bed86f2975db46?f=discussion
来源：牛客网

解释下代码，设有数组大小为5。
对于第一个for循环
第一步：b[0] = 1;
第二步：b[1] = b[0] * a[0] = a[0]
第三步：b[2] = b[1] * a[1] = a[0] * a[1];
第四步：b[3] = b[2] * a[2] = a[0] * a[1] * a[2];
第五步：b[4] = b[3] * a[3] = a[0] * a[1] * a[2] * a[3];
然后对于第二个for循环
第一步
temp *= a[4] = a[4]; 
b[3] = b[3] * temp = a[0] * a[1] * a[2] * a[4];
第二步
temp *= a[3] = a[4] * a[3];
b[2] = b[2] * temp = a[0] * a[1] * a[4] * a[3];
第三步
temp *= a[2] = a[4] * a[3] * a[2]; 
b[1] = b[1] * temp = a[0] * a[4] * a[3] * a[2];
第四步
temp *= a[1] = a[4] * a[3] * a[2] * a[1]; 
b[0] = b[0] * temp = a[4] * a[3] * a[2] * a[1];
由此可以看出从b[4]到b[0]均已经得到正确计算。
*/
```

