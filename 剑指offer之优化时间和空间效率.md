---
title: 剑指offer-解决面试题的思路
date: 2019-09-30 13:28:50
tags:  面试题目
mathjax: true
---



剑指offer第五章的题目练习和重要知识点。<!--more-->

# 时间效率

## 数组中出现次数超过一般的数字

### 题目描述

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

### 思路分析

第一种方法：对数组进行排序，中间元素一定是出现次数超过一般的元素，当然最好还需要判断是否个数超过了一半。

第二种方法：空间换取时间，通过一个map来记录每个元素出现的次数。

第三种方法：遍历数组的时候保存两个值：一个是数组中的一个数字；另一个是次数。当我们遍历一个数字的时候，如果和下一个数字和我们之前保存的数字相同，则次数加1；如果下一个数字和我们之前保存的数字不同，则次数减1。如果次数为零，那么我们需要保存下一个数字，并把次数设为1。

### 代码实现

第二种方法：

```c++
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        if (numbers.size() == 0)
            return 0;
        if (numbers.size() == 1)
            return numbers[0];
        map<int, int> MII;
        for (int i = 0; i < numbers.size(); ++i)
        {
            MII[numbers[i]]++;
            if (MII[numbers[i]] > numbers.size()/2)
                return numbers[i];
        }
        return 0;
    }
};
```

第三种方法

```c++
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        if (numbers.size() == 0)
            return 0;
        if (numbers.size() == 1)
            return numbers[0];
        int times = 1;
        int result = numbers[0];
        for (int i = 1; i < numbers.size(); ++i)
        {
            if (times == 0)
            {
                result = numbers[i];
                times = 1;
            }else if (result == numbers[i])
            {
                ++times;
            }else
            {
                times--;
            }
        }
        if (!CheckMoreThanHalf(numbers, result))
            result = 0;
        return result;
    }
    
    bool CheckMoreThanHalf(vector<int> numbers, int target)
    {
        int count = 0;
        for (int i = 0; i < numbers.size(); ++i)
            if (target == numbers[i])
                ++count;
        return (count << 1) > numbers.size() ? true : false;
    }
};
```

## 最小的k个数

### 题目描述

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

### 思路分析

第一种方法：通过Partition函数实现，每次选择一个元素，将数组分为两部分，左侧小于所选择的元素，右侧大于所选择的元素。而Partition函数返回分界处的下标，所以当下标等于k时，左侧部分就是所求的最小的k个数。

第二种方法：通过一个容器来记录前k个最小的数，容器最好一个最大堆，如果容器中元素小于k个，那么遍历数组时，直接将元素插入到容器中，如果容器中元素达到k个，取出容器中最大的值与数组中的值比较，如果数组中的值比容器中最大的值小，则删除容器中最大的值，并将数组中的值插入。当然，容器也快车是set等容器。

第一种方法的时间复杂为O(n)，但是会修改数组。

第二种方法的时间复杂为O(nlogk)，适用于海量数据。

### 代码实现

第一种方法：

```c++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if (k <= 0 || input.size() < k || input.size() == 0)
            return res;
        int end = input.size()-1;
        int start = 0;
        int index = Partition(input, start, end);
        while(index != k - 1)
        {
            if (index > k-1)
            {
                end = index  - 1;
                index = Partition(input, start, end);
            }
            else
            {
                start = index + 1;
                index = Partition(input, start, end);
            }
        }
        for (int i = 0 ; i < k; ++i)
        {
            res.insert(res.end(),input[i]);
        }
        return res;
    }

    int Partition(vector<int>& input, int begin, int end)
    {
        int low=begin;
        int high=end;
         
        int pivot=input[low];
        while(low<high)
        {
            while(low<high&&pivot<=input[high])
                high--;
            input[low]=input[high];
            while(low<high&&pivot>=input[low])
                low++;
            input[high]=input[low];
        }
        input[low]=pivot;
        return low;
    }
};
```

第二种方法：

```c++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if (k <= 0 || input.size() < k || input.size() == 0)
            return res;
        set<int> container;
        for (int i = 0; i < input.size(); ++i)
        {
            if (container.size() < k)
                container.insert(input[i]);
            else{
                if (input[i] < *container.rbegin())
                    container.erase(*container.rbegin());
                    container.insert(input[i]);
            }
        }
        for (int i = 0 ; i < k; ++i)
        {
            res.push_back(*container.begin());
            container.erase(container.begin());
        }
        return res;
    }
};
```

使用最大堆实现：

```c++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if (k <= 0 || input.size() < k || input.size() == 0)
            return res;
        priority_queue<int> tree;
        for (int i = 0; i < input.size(); ++i)
        {
            if (tree.size() < k)
                tree.push(input[i]);
            else{
                if (input[i] < tree.top())
                {
                    tree.pop();
                    tree.push(input[i]);
                }
            }
        }
        for (int i = 0 ; i < k; ++i)
        {
            res.insert(res.begin(),tree.top());
            tree.pop();
        }
        return res;
    }
};
```

## 数据流中的中位数

### 题目描述

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

###  思路分析

通过堆来实现，一个最大堆记录中间元素左侧的所有元素值，一个最小堆记录中间元素右侧的所有元素的值。

如果两个堆中的元素数量为偶数，并且就插入到最小堆；否则插入最大堆。

但是如何保证最大堆中的元素都小于最小堆中的元素？

每次元素插入最大堆后，将最大堆的根节点的值取出插入到最小堆中，就保证了最大堆的元素总是小于最小堆元素。

### 代码实现

```c++
class Solution {
public:
    void Insert(int num)
    {
        if (((min.size() + max.size()) & 1) == 0)
        {
            if (max.size() > 0 && num < max[0])
            {
                max.push_back(num);
                push_heap(max.begin(), max.end(), less<int>());
                
                num = max[0];
                
                pop_heap(max.begin(), max.end(), less<int>());
                max.pop_back();
            }
            min.push_back(num);
            push_heap(min.begin(), min.end(), greater<int>());
        }else
        {
            if (min.size() > 0 && min[0] < num)
            {
                min.push_back(num);
                push_heap(min.begin(), min.end(), greater<int>());
                
                num = min[0];
                
                pop_heap(min.begin(), min.end(), greater<int>());
                min.pop_back();
            }
            max.push_back(num);
            push_heap(max.begin(), max.end(), less<int>());
        }
    }

    double GetMedian()
    { 
        int size = min.size() + max.size();
        double median = 0;
        if ((size & 1) == 1)
           median = min[0];
        else
           median = (double)((min[0] + max[0]))/2;
        return median;
          
    }
private:
    vector<int> min;
    vector<int> max;
};
```



## 连续子数组的最大和

### 题目描述

HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)

### 思路分析

分析数组发现，如果当前元素之前的元素之和加上当前元素大于当前元素，就表示这一连续段是当前最大的和。

如果当前元素之前元素之和加上当前元素小于当前元素，表示之前元素起到反作用，所以舍弃前面的元素之和，将最大和设置为当前元素。

可以考虑采用动态规划，利用一个数组来记录每个元素最大的连续和。

### 代码实现

```c++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if (array.size() <= 0)
            return INT_MIN;
        int greatestSum = INT_MIN;
        int len = array.size();
        int maxDp[len];
        maxDp[0] = array[0];
    
        for (int i = 1; i < len; ++i)
            maxDp[i] = max(maxDp[i-1] + array[i], array[i]);
        for (int i= 0; i < len; ++i)
            greatestSum = max(greatestSum, maxDp[i]);
        
        return greatestSum;
    }
};
```

另一种实现方法：

```c++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if (array.size() <= 0)
            return INT_MIN;
        int greatestSum = INT_MIN;
        int len = array.size();
        int nCurSum =  0;
    
        for (int i = 0; i < len; ++i)
        {
            if (nCurSum <= 0)
                nCurSum = array[i];
            else
                nCurSum += array[i];
            greatestSum  = max(greatestSum, nCurSum);
        }

        return greatestSum;
    }
};
```



## 整数中1出现的次数

### 题目描述

求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

### 思路分析

最简单的方法，就是求每个数中1出现的次数，然后将每个数中1的个数相加，这样时间复杂度为O(nlogn)。

第二种方法：

链接：https://www.nowcoder.com/questionTerminal/bd7f978302044eee894445e244c7eee6?f=discussion

当n = 3141592时:

| m    | a       | b    | ones                              |
| ---- | ------- | ---- | --------------------------------- |
| 1    | 3141592 | 0    | (3141592+8)/10*1+0=314160         |
| 10   | 314159  | 2    | (314159+8)/10*10+0=314160         |
| 100  | 31415   | 92   | (31415+8)/10*100+0=314200         |
| 1000 | 3141    | 592  | (3141+8)/10*1000+1*(592+1)=314593 |

当然后面还有m=10000,100000,1000000三种情况，对应着万位，十万位， 百万位为1时的情况

下面说下a+8的意义：

当考虑个位，十位，百位这三位为1的情况时：

个位 2 ，当个位取值1时，前面的六位数字可由0~314159组成，即314160种情况

十位9，当十位取值1时，前面的五位数字可由0~31415组成，十位之后的一位可由0~9组成，组合情况31416*10=314160种情况

百位5，当百位取值为1时，前面的四位数字可由0~3141组成，百位之后的两位可由0~99组成，组合情况为3142*100=314200种情况

------

注意：当考虑千位1时：

千位1，千位取值即1，前面的三位数字可由0~314组成，但是当前面的值为314时，后面的三位只有0~592种情况(特殊情况)，其余的情况即为前面的值为0~313,后面三位有0~999,情况数为314*1000，所以总情况数为314*1000   + 593=314593种情况

这时可发现和代码中的公式算的情况是吻合的，a+8的巧妙之处在于当a的最后一位(当前分析位)为0或1时，加8不产生进位，这是为需要单独算的特殊情况做准备，而当前分析位为2~9时，不需要考虑特殊情况，所以允许加8产生的进位。

### 代码实现

```c++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int number = 0;
        for (int i = 1; i <= n;  ++i)
            number += NumberOf1(i);
        return number;
    }
    
    int NumberOf1(int n)
    {
        int count = 0;
        while(n)
        {
            if (n % 10 == 1)
                ++count;
            n = n / 10;
        }
        return count;
    }
};
```

第二种方法：

```c++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int ones = 0;
        for (int m = 1; m <= n; m *= 10) {
            int a = n/m, b = n%m;
            if(a%10 == 0)
                ones += a / 10 * m; //a最后一位为0，则前面数的范围是从1~a
            else if(a%10 == 1) //特殊情况
                ones += (a/10*m) + (b+1);
            else//a的最后一位位2~9，则前面数的范围从0~a
                ones += (a/10+1)* m;
        }
        return ones;
    }
};

//或者如下写法
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int ones = 0;
        for (int m = 1; m <= n; m *= 10) {
            int a = n/m, b = n%m;
            ones += (a + 8) / 10 * m + (a % 10 == 1) * (b + 1);
        }
        return ones;
    }
};
```

## 数字序列中某一位的数字

### 题目描述

数字以0123456789101112131415...的格式序列化到一个字符序列中。在这个序列中（从0开始计数），第5位是5，第13位是1，等等。请写一个函数，求任意第n位对应的数字。

### 思路分析

思路具体见代码实现的注释。

### 代码实现

````c++
#include<iostream>  
#include<string>  
using namespace std;

//得到m位的数字公有多少个，例如digits=2, 则返回两位数的个数90,digits=3,则返回三位数的个数900
int countOfIntegers(int digits)
{
	if (digits == 1)
		return 10;

	int count = (int)std::pow(10, digits - 1);
	return 8 * count;
}
//m位数的第一个数字，例如一个两位数是10，第一个三位数是100
int beginNumber(int digits)
{
	if (digits == 1)
		return 0;
	return (int)pow(10, digits - 1);
}
//当我们知道那一位数位于某m位数之中后，就可以通过如下函数找出那一位数
int digitAtIndex(int index, int digits)
{
	int number = beginNumber(digits) + index / digits;
	int indexFromRight = digits - index % digits;
	for (int i = 1; i < indexFromRight; ++i)
		number /= 10;
	return number % 10;
}

int digitAtIndex(int index)
{
	if (index < 0)
		return -1;
	int digits = 1; //表示位数
	while (true)
	{
		int numbers = countOfIntegers(digits);  //计算digits位数的数字个数，例如digits=2，则返回10
		if (index < numbers * digits) //判断index是否在digits位的数字中，如果在这通过下面的函数在digits位数中找出该数字
			return digitAtIndex(index, digits);
		index -= digits * numbers;
		++digits;
	}
	return -1;
}


int main()
{
	std::cout << digitAtIndex(10) << endl;
}
````

## 把数组排成最小的数

### 题目描述

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

### 思路分析

首先将整型数组转换成string数组，然后将string数组排序，最好将排序好的自身穿拼接起来。

排序规则如下：

若ab > ba，则a > b

若ab < ba，则a < b

若ab = ba，则a = b

解释说明：比如 "3" < "31"，但是”331“ > "313"，所以 ”3“ > "31"

### 代码实现

```c++
class Solution {
public:
    static bool compare(const string & str1, const string & str2)
    {
        string s1 = str1 + str2;
        string s2 = str2 + str1;
        return s1 < s2;
    }
    string PrintMinNumber(vector<int> numbers) {
        string result;
        if (numbers.size() <= 0)
        {
            return result;
        }
        vector<string> stringStream;
        for (int i = 0 ; i < numbers.size(); ++i)
        {
            stringStream.push_back(to_string(numbers[i]));
        }
        
        sort(stringStream.begin(), stringStream.end(), compare);
        for (int i = 0; i < numbers.size(); ++i)
            result += stringStream[i];
        return result;
    }
};
```

## 把数字翻译成字符串

### 题目描述

给定一个数字，我们按照如下规则把它翻译为字符串；0翻译成a，1翻译成b，……，11翻译成l，25翻译成z。一个数字可能有多个翻译。例如，12258有5种不同的翻译，分别是bccfi，bwfi，bczi，mcfi，mzi。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

### 思路分析

首先将数字转化成字符串。

通过动态规划实现。

### 代码实现

```c++
int GetTranslationCount(const string & number)
{
	int len = number.length();
	int * counts = new int[len];
	int count = 0;
	for (int i = len- 1; i >= 0; --i)
	{
		count = 1;
		if (i < len-1)
			count = counts[i + 1];
		else
			count = 1;
		if (i < len - 1)
		{
			int digit1 = number[i] - '0';
			int digit2 = number[i+1] - '0';
			int coverted = digit1 * 10 + digit2;
			if (coverted <= 25 && coverted >= 10)
			{
				if (i < len - 2)
					count += counts[i + 2];
				else
					count += 1;
			}
		}
		counts[i] = count;
	}
	count = counts[0];
	delete[] counts;
	return count;
}
	
int GetTranslation(int number)
{
	if (number < 0)
		return 0;

	string numberInString = to_string(number);
	return GetTranslationCount(numberInString);
}

int main()
{
	std::cout << GetTranslation(12258) << endl;
}

```



## 礼物的最大价值

### 题目描述

在一个m×n的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于0）。你可以从期盼的左上角开始拿各自里的礼物，并每次向左或向右移动一格，直到到达期盼的右上角。给定一个期盼及其上面的礼物，请计算你最多能拿到多少价值的礼物？

### 思路分析

这是一个典型的动态规划问题。

第一种方法，采用一个二维动态规划数组实现，实现简单。

第二种方法，采用一个一维数组实现，因为当前位置的最大价值只依赖于上方和左方的两个格子，可以通过两个临时变量来存储这两个值。实现一层一层的遍历。

### 代码实现

```c++
#include<iostream> 
#include <vector>
using namespace std;

int maxValue(int values[][4], int row, int column)
{
    if (values == nullptr || row <= 0 || column <= 0)
		return 0;
	vector<vector<int> > dp(row, vector<int>());
	for (int i = 0; i < row; i++) {
		dp[i].resize(column);
	}
	dp[0][0] = values[0][0];
	for (int i = 1; i < column; ++i)
		dp[0][i] = values[0][i] + dp[0][i-1];
	for (int i = 1; i < row; ++i)
		dp[i][0] = values[i][0] + dp[i-1][0];
	for (int i = 1; i < row; ++i)
		for (int j = 1; j < column; j++)
			dp[i][j] = values[i][j] + ((dp[i-1][j] > dp[i][j - 1]) ? dp[i-1][j] : dp[i][j-1]);
	return dp[row - 1][column - 1];
}
int main()
{
	int values[4][4] = { 1,10,3,8,12,2,9,6,5,7,4,11,3,7,16,5 };
	
	cout << maxValue(values, 4, 4) << endl;
	return 0;
}
```

方法二：

````c++
#include<iostream> 
#include <vector>
using namespace std;

int maxValue(int values[][4], int row, int column)
{
    if (values == nullptr || row <= 0 || column <= 0)
		return 0;
	vector<int> dp(column);
	for (int i = 0 ; i < row; ++i)
		for (int j = 0; j < column; ++j)
		{
			int left = 0;
			int top = 0;
			if (i > 0)
				top = dp[j];
			if (j > 0)
				left = dp[j - 1];

			dp[j] = ((left > top) ? left : top) + values[i][j];
		}

	return dp[column - 1];
	
}
int main()
{
	int values[4][4] = { 1,10,3,8,12,2,9,6,5,7,4,11,3,7,16,5 };
	
	cout << maxValue(values, 4, 4) << endl;
	return 0;
}
````

## 最长不含重复字符的字符串

### 题目描述

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长字符串的长度。假设字符串只包含‘a’~‘z’的字符。例如，在字符串“arabcacfr”中，最长的不含重复字符的字符串是“acfr”，长度为4。

### 思路分析

动态规划实现。

通过判断每个元素之前有没有重复元素。如果没有重复的元素，在上个值上加1。如果有重复的，找到离该元素最近的重复元素，计算出两者之间的距离，如果距离大于上个元素的最大长度，则仍然在上一个元素的最大长度上加1；如果距离小于等于上个元素的最大长度，则该元素的最长长度等于该距离。具体公式如下：
$$
dp[0] = 0 \\
\begin{equation}
dp[i] = \begin{cases}
dp[i-1] + 1, & \text{if d > dp[i-1] 或者之前没有重复元素} \\
d, & \text{if d > dp[i-1]}
\end{cases}
\end{equation}\\
d为当前元素和最近重复元素的距离。
$$
第一种方法，通过两层循环实现，第一层遍历数组，第二层判断该元素释放在之前出现过。

另一种方法，原理跟上面一样，只不过采用的形式不同。通一个大小为26的数组记录每个元素最近出现的位置。该位置初始为-1，如果为-1，表示之前没有出现过。判断每个元素时，首先读取该元素在数组中的值，判断是否为-1，或者是否当前位置与该数组中值的差和当前长度的关系。

### 代码实现

```c++
#include<iostream> 
#include <vector>
using namespace std;
int longestSubStrWithoutDup(const string & str)
{
	if (str.empty())
		return 0;
	int maxLen = 0;
	int len = str.size();
	vector<int> dp(len);
	dp[0] = 1;
	for (int i = 1; i < len; ++i)
	{
		int d = 0;
		for (int j = i-1; j >= 0; --j)
		{
			if (str[i] == str[j])
			{
				d = i - j;
				break;
			}
		}
		if (d == 0)
			dp[i] = dp[i - 1] + 1;
		else
			dp[i] = (dp[i - 1] >= d ? d : dp[i-1]+1);
		maxLen = (dp[i] > maxLen ? dp[i] : maxLen);
	}
	return maxLen;
}
int main()
{
	cout << longestSubStrWithoutDup("arabacacf") << endl;
	return 0;
}
```

第二种方法：

````c++
#include <vector>
using namespace std;
int longestSubStrWithoutDup(const string & str)
{
	if (str.empty())
		return 0;
	int maxLen = 0;
	int curLen = 0;
	int * position = new int[26];
	for (int i = 0; i < 26; ++i)
		position[i] = -1;
	for (int i = 0; i < str.length(); ++i)
	{
		int prevIndex = position[str[i] - 'a'];
		if (prevIndex < 0 || i - prevIndex > curLen)
			++curLen;
		else
		{
			if (curLen > maxLen)
				maxLen = curLen;
			curLen = i - prevIndex;
		}
		position[str[i] - 'a'] = i;
	}
	if (curLen > maxLen)
		maxLen = curLen;
	delete[] position;
	return maxLen;
	
	return maxLen;
}
int main()
{
	cout << longestSubStrWithoutDup("arabacacf") << endl;
	return 0;
}
````



# 时间效率与空间的平衡

## 丑数

### 题目描述

 把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。 

### 思路分析

第一种方法，从头到尾计算每个数，判断是否为丑数，然后对丑数进行计数。

第二种方法，对丑数进行排序，然后判断丑数的个数是否满足要求。这样降低了复杂度，不用对每个字都去判断一遍是否为丑数。具体思路如下：

​      这种思路的关键在于怎样确保数组里面的丑数是排好序的。假设数组中已经有若干个丑数排好序后放在数组中，并且把 **已有最大的丑数记做M** ，我们接下来分析如何生成下一个丑数。该丑数肯定是前面某一个丑数乘以2、3或者5的结果，所以我们首先考虑把已有的每个丑数乘以2。在乘以2的时候，可以得到多干个小于等于M的结果。由于是按顺序生成的，小于或者等于M肯定已经在数组中了，我们不需要再考虑；还会得到若干个大于M的结果，但我们只需要第一个大于M的结果，因为我们希望丑数是按照从小到大的顺序生成的，其他更大的结果以后再说。我们把得到的第一个乘以2后大于M的丑数记为M2。同样，我们把已有的每一个丑数乘以3和5，能得到第一个大于M的结果M3和M5。那么下一个丑数应该是M2、M3和M5这三个数的最小者了    。   

​       前面分析的时候，提到把已有的丑数分别乘以2、3和5。事实上这不是必须的，因为已有的丑数是按照顺序放在数组中的。对乘以2而言，肯定存在某一个丑数T2，排在它之前的每一个乘以2得到的结果都会小于已有最大的丑数，在它之后的每一个丑数乘以2得到的结果都会太大。我们只需要记下这个丑数的位置，同时每次生成新的丑数的时候，取更新这个这个T2.对于乘以3和5而言，也存在着同样的T3和T5。      

### 代码实现

```c++
class Solution {
public:
    bool IsUgly(int number)
    {
        while (number % 2 == 0)
            number /= 2;
        while (number % 3 == 0)
            number /= 3;
        while (number % 5 == 0)
            number /= 5;
        return (number == 1) ? true : false;
    }
    int GetUglyNumber_Solution(int index) {
        if (index == 1)
            return index;
        int number = 0;
        int uglyFound = 0;
        while (uglyFound < index)
        {
            ++number;
            if (IsUgly(number))
                ++uglyFound;
        }
        return number;
    }
```

第二种方法：

```c++
class Solution {
public:
    int Min(int num1, int num2, int num3)
    {
        int min = (num1 < num2) ? num1 : num2;
        min = (min < num3) ? min : num3;
        return min;
    }
    int GetUglyNumber_Solution(int index) {
        if (index <= 0)
            return 0;
        if (index == 1)
            return index;
        int * pUglyNumbers = new int[index];
        pUglyNumbers[0] = 1;
        int nextUglyIndex = 1;
        
        int *pMultiply2 = pUglyNumbers;
        int *pMultiply3 = pUglyNumbers;
        int *pMultiply5 = pUglyNumbers;
        
        while (nextUglyIndex < index)
        {
            int min = Min(*pMultiply2 * 2, *pMultiply3 * 3, *pMultiply5 * 5);
            pUglyNumbers[nextUglyIndex] = min;
            while (*pMultiply2 * 2 <= pUglyNumbers[nextUglyIndex])
                ++pMultiply2;
            while (*pMultiply3 * 3 <= pUglyNumbers[nextUglyIndex])
                ++pMultiply3;
            while (*pMultiply5 * 5 <= pUglyNumbers[nextUglyIndex])
                ++pMultiply5;
            ++nextUglyIndex;
        }
        int ugly = pUglyNumbers[nextUglyIndex - 1];
        delete [] pUglyNumbers;
        return ugly;
    }
};
```



## 第一个只出现一次的字符

### 题目描述

 在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）. 

### 思路分析

通过一个大小为128的数组来记录字符出现的情况，数组初始化为-1。如果一个字符对应的位置为-1，则将数组中该位置的值设置为该字符在字符串中的位置；如果字符对应的位置不为-1和-2，则将数组中的值设为-1；如果字符对应的位置数组值为-1，表示该元素在字符串中是第三次出现，什么操作也不做，继续保持-2。

### 代码实现

```c++
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if (str.size() == 0)
            return -1;
        vector<int> alp(128, -1);
        for (int i= 0; i < str.size(); ++i)
        {
            int index = str[i]-'0';
            if (alp[index] == -1)
                alp[index] = i;
            else if (alp[index] == -2)
                continue;
            else
                alp[index] = -2;
        }
        int res = 129;
        for (int i = 0; i < 128; ++i)
        {
            if (alp[i] != -1 && alp[i] != -2)
            {
                if (res > alp[i])
                    res = alp[i];
            }
        }
        return res;
    }
};
```

## 字符流中第一个只出现一次的字符

### 题目描述

请实现一个函数，用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符“go”时，第一个只出现一次的字符是‘g’；当从该字符中读取钱6个字符“google”时，第一个只出现一次的字符是‘l’。

### 思路分析

思路跟上面题一样，只不过用来封装，并且通过函数来插入字符。每次插入一个字符，更新存储字符信息的数组。

### 代码实现

````c++
// Homework_1.cpp : 定义控制台应用程序的入口点。
//

#include "pch.h"
#include<iostream> 
#include <vector>
using namespace std;
class CharStatistics
{
public:
	CharStatistics() : index(0)
	{
		for (int i = 0; i < 256; ++i)
			occurrence[i] = -1;
	}
	void Insert(char ch)
	{
		if (occurrence[ch] == -1)
			occurrence[ch] = index;
		else if (occurrence[ch] >= 0)
			occurrence[ch] = -2;
		index++; //后面出现字符index变大，有利于后面找出第一次出现的仅出现一次字符
	}

	char FirstApperingOnce()
	{
		char ch = '\0';
		int minIndex = numeric_limits<int>::max();
		for (int i = 0; i < 256; ++i)
		{
			if (occurrence[i] < minIndex && occurrence[i] >= 0)
			{
				ch = (char)i;
				minIndex = occurrence[i];
			}
		}
		return ch;
	}
	
private:
	int occurrence[256];
	//occurrence[i] = -1; 字符没有出现过
	//occurrence[i] = -2; 字符出现的次数超过1次
	//occurrence[i] >= 0; 字符仅仅出现了一次
 	int index;
};

int main()
{
	CharStatistics chsta;
	chsta.Insert('g');
	cout << chsta.FirstApperingOnce() << endl;
	chsta.Insert('o');
	cout << chsta.FirstApperingOnce() << endl;
	chsta.Insert('o');
	cout << chsta.FirstApperingOnce() << endl;
	chsta.Insert('g');
	cout << chsta.FirstApperingOnce() << endl;
	chsta.Insert('l');
	cout << chsta.FirstApperingOnce() << endl;
	chsta.Insert('e');
	cout << chsta.FirstApperingOnce() << endl;
	return 0;
}
````

## 数组中的逆序对

### 题目描述

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

输入描述:

```
题目保证输入的数组中没有的相同的数字数据范围：	对于%50的数据,size<=10^4	对于%75的数据,size<=10^5	对于%100的数据,size<=2*10^5
```

示例1

输入

```
1,2,3,4,5,6,7,0
```

输出

[复制](javascript:void(0);)

```
7
```

### 思路分析

链接：https://www.nowcoder.com/questionTerminal/96bd6684e04a44eb80e6a68efc0ec6c5?f=discussion来源：牛客网

   我们以数组{7,5,6,4}为例来分析统计逆序对的过程。每次扫描到一个数字的时候，我们不拿ta和后面的每一个数字作比较，否则时间复杂度就是O(n^2)，因此我们可以考虑先比较两个相邻的数字。  

   ![img](https://uploadfiles.nowcoder.com/files/20180504/7491640_1525400721676_20170710223428592) 
   

​    (a) 把长度为4的数组分解成两个长度为2的子数组；  

​    (b) 把长度为2的数组分解成两个成都为1的子数组；   

​    (c) 把长度为1的子数组  **合并、排序并统计逆序对**  ；    

​    (d) 把长度为2的子数组合并、排序，并统计逆序对；   

​     在上图（a）和（b）中，我们先把数组分解成两个长度为2的子数组，再把这两个子数组分别拆成两个长度为1的子数组。接下来一边合并相邻的子数组，一边统计逆序对的数目。在第一对长度为1的子数组{7}、{5}中7大于5，因此（7,5）组成一个逆序对。同样在第二对长度为1的子数组{6}、{4}中也有逆序对（6,4）。由于我们已经统计了这两对子数组内部的逆序对，因此需要把这两对子数组  **排序**  如上图（c）所示，  **以免在以后的统计过程中再重复统计。**     

​        接下来我们统计两个长度为2的子数组子数组之间的逆序对。合并子数组并统计逆序对的过程如下图如下图所示。   

​    我们先用两个指针分别指向两个子数组的末尾，并每次比较两个指针指向的数字。如果第一个子数组中的数字大于第二个数组中的数字，则构成逆序对，并且逆序对的数目等于第二个子数组中剩余数字的个数，如下图（a）和（c）所示。如果第一个数组的数字小于或等于第二个数组中的数字，则不构成逆序对，如图b所示。每一次比较的时候，我们都把较大的数字从后面往前复制到一个辅助数组中，确保 **辅助数组（记为copy）** 中的数字是递增排序的。在把较大的数字复制到辅助数组之后，把对应的指针向前移动一位，接下来进行下一轮比较。

   ![img](https://uploadfiles.nowcoder.com/files/20170711/7491640_1499735690500_20170711085550783)   

   过程：先把数组分割成子数组，先统计出子数组内部的逆序对的数目，然后再统计出两个相邻子数组之间的逆序对的数目。在统计逆序对的过程中，还需要对数组进行排序。如果对排序算法很熟悉，我们不难发现这个过程实际上就是归并排序。

### 代码实现

```c++
class Solution {
public:

    int InversePairs(vector<int> data) {
       int length=data.size();
        if(length<=0)
            return 0;

       vector<int> copy;
       for(int i=0;i<length;i++)
           copy.push_back(data[i]);
       long long count=InversePairsCore(data,copy,0,length-1);
  
       return count%1000000007;
    }
    long long InversePairsCore(vector<int> &data,vector<int> &copy,int start,int end)
    {
       if(start==end)
       {
           copy[start]=data[start];
           return 0;
       }
       int length=(end-start)/2;
       long long left=InversePairsCore(copy,data,start,start+length);
       long long right=InversePairsCore(copy,data,start+length+1,end); 
        
       int i=start+length;
       int j=end;
       int indexcopy=end;
       long long count=0;
       while(i>=start&&j>=start+length+1)
       {
           if(data[i]>data[j])
           {
               copy[indexcopy--]=data[i--];
               count += j-start-length;          //count=count+j-(start+length+1)+1;
           }
           else
           {
               copy[indexcopy--]=data[j--];
           }          
       }
       for(;i>=start;i--)
           copy[indexcopy--]=data[i];
       for(;j>=start+length+1;j--)
           copy[indexcopy--]=data[j];       
       return left+right+count;
    }
};
```

## 两个链表的第一个公共节点

### 题目描述

 输入两个链表，找出它们的第一个公共结点。 

### 思路分析

第一种方法：

首先，求出两个链表的长度。

然后，找出较长的那个链表，并求出两个链表的长度差diff，让较长的链表先往前走diff步。

其次，两个链表同时向前移动，如果两个链表的节点相等，表示到达了第一个公共结点。

最后，输入该结点。

第二种方法：

 用两个指针扫描”两个链表“，最终两个指针到达 null 或者到达公共结点。 

### 代码实现

```c++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        int lenghtOfHead1 = GetLengthOfList(pHead1);
        int lenghtOfHead2 = GetLengthOfList(pHead2);
        int i = 0;
        int nLenghtDif = lenghtOfHead2 - lenghtOfHead1;
        ListNode * pShortList = pHead1;
        ListNode * pLongList = pHead2;
        if (lenghtOfHead1 > lenghtOfHead2)
        {
            pShortList = pHead2;
            pLongList = pHead1;
            nLenghtDif = lenghtOfHead1 - lenghtOfHead2;
        }
         
        for (int i = 0; i < nLenghtDif; ++i)
            pLongList = pLongList->next;
        
        while (pLongList != nullptr && pShortList != nullptr && pLongList != pShortList)
        {
            pLongList = pLongList->next;
            pShortList = pShortList->next;
        }
        ListNode * res = pLongList;
        return res;
    }
    
    int GetLengthOfList(ListNode* pHead)
    {
        ListNode * p = pHead;
        int count = 0;
        while (p != nullptr)
        {
            p = p->next;
            ++count;
        }
        return count;
    }
    
};
```

第二种方法：

```c++
class Solution {
public:

    ListNode* FindFirstCommonNode( ListNode *pHead1, ListNode *pHead2) {
        ListNode *p1 = pHead1;
        ListNode *p2 = pHead2;
        while(p1!=p2){
            p1 = (p1==NULL ? pHead2 : p1->next);
            p2 = (p2==NULL ? pHead1 : p2->next);
        }
        return p1;
    }
    
};
```

