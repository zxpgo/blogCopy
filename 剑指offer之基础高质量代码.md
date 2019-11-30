---
title: 剑指offer-面试需要的基础知识
date: 2019-09-20 13:28:50
tags:  面试题目
mathjax: true

---

剑指offer第三章的题目练习和重要知识点。<!--more-->

# 代码规范性

书写

命令

布局

# 代码完整性

- 功能测试：基本的功能
- 边界测试：循环的边界条件，递归终止的边界条件
- 负面测试：各种可能的错误输入

## 数值的整数次方

### 题目描述

给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

保证base和exponent不同时为0

### 思路分析

第一种方法，将exponent分为两份计算。

第二种方法，通过递归将指数计算细分到最小的单元。

### 代码实现

```C++
class Solution {
public:
    double Power(double base, int exponent) {
        if (abs(base - 0.0) < 1e-10)
            return 0;
        double res = 1.0;
        int i;
        if (exponent < 0){
            base = 1 / base;
            exponent = -exponent;
        }
        for (i = 1; i <= exponent / 2; i++)
            res = res*base;
        res = exponent % 2 == 0 ? res * res : res * res* base;
        return res;
    }
};
```

优化方法：

```C++
class Solution {
public:
    double Power(double base, int exponent) {
        if (exponent == 0)
            return 1;
        if (exponent == 1)
            return base;
        if (exponent < 0)
        {
            exponent = -exponent;
            base = 1 / base;
        }
        double result = Power(base, exponent >> 1);
        result *= result;
        if ((exponent & 0x1) == 1)
            result *= base;
        return result;
    }
};
```

## 打印从1到最大的n位数

### 题目描述

输入数字m，按顺序打印出从1到最大的n位十进制数。比如数日3，则打印出1，2，3一直到最大的3位数999。

### 思路分析

因为数字m的大小没有限定，所以无法用int或者long long等类型表示最大的数字。所以考虑用字符串来表示数字。

### 代码实现

```C++
#include <iostream>
using namespace std;
bool Increament(char * number) {
	int length = strlen(number);
	int nTakeOver = 0;
	bool isOverflow = false;
	for (int i = length - 1; i >= 0; i--) {
		int nSum = number[i] - '0' + nTakeOver;
		if (i == length - 1)
			nSum++;
		if (nSum >= 10) {
			if (i == 0)
				isOverflow = true;
			else
			{
				nSum -= 10;
				nTakeOver = 1;
				number[i] = '0' + nSum;
			}
		}
		else {
			number[i] = '0' + nSum;
			break;
		}
	}
	return isOverflow;
}
void PrintNumber(const char * number) {
	int length = strlen(number);
	bool isBeginning0 = true;
	for (int i = 0; i < length; i++) {
		if (isBeginning0 && number[i] != '0')
			isBeginning0 = false;
		if(!isBeginning0)
			cout << number[i];
	}
	cout << endl;
}
int main()
{
	int n = 3;
	char * number = new char[n + 1];
	memset(number, '0', n);
	number[n] = '\0';
	while (!Increament(number))
		PrintNumber(number);
	delete[] number;
	return 0;
}
```

递归实现：

```C++
#include <iostream>
using namespace std;
void PrintNumber(const char * number) {
	int length = strlen(number);
	bool isBeginning0 = true;
	for (int i = 0; i < length; i++) {
		if (isBeginning0 && number[i] != '0')
			isBeginning0 = false;
		if(!isBeginning0)
			cout << number[i];
	}
	cout << endl;
}
void Print1ToMaxOfNDigitsRecursively(char * number, int length, int index) {
	if (index == length - 1) {
		PrintNumber(number);
		return;
	}
	for (int i = 0; i < 10; i++) {
		number[index + 1] = i + '0';
		Print1ToMaxOfNDigitsRecursively(number, length, index + 1);
	}

}
void Print1ToMaxOfNDigits(int n) {
	if (n <= 0)
		return;
	char * number = new char[n + 1];
	number[n] = '\0';
	for (int i = 0; i < 10; ++i) {
		number[0] = i + '0';
		Print1ToMaxOfNDigitsRecursively(number, n, 0);
	}
	delete[] number;
}
int main()
{
	Print1ToMaxOfNDigits(3);
	return 0;
}
```

## 删除链表中重复的节点

### 题目描述

在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

### 思路分析

1. 首先添加一个头节点，以方便碰到第一个，第二个节点就相同的情况.
2. 设置 pre ，last 指针， pre指针指向当前确定不重复的那个节点，而last指针相当于工作指针，一直往后面搜索。

### 代码实现

```C++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead)
    {
        if (!pHead || !(pHead->next) )
            return pHead;
        ListNode head(0);
        head->next = pHead;
        ListNode * pPre = &head, * pLast = (&head)->next;
        while(pLast != nullptr){
           if(pLast->next != nullptr && pLast->val == pLast->next->val){
               while(pLast->next != nullptr && pLast->val == pLast->next->val)
                   pLast = pLast->next;
                pPre->next = pLast->next;
                pLast = pLast->next;
           }else{
               pPre = pPre->next;
               pLast = pLast->next;
           }
        }
        return (&head)->next;
    }
};
```

## 正则表达式匹配

### 题目描述

请实现一个函数用来匹配包括'.'和`'*'`的正则表达式。模式中的字符'.'表示任意一个字符，而`'*'`表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串`"aaa"`与模式`"a.a"`和`"ab*ac*a"`匹配，但是与`"aa.a"`和`"ab*a"`均不匹配。

### 思路分析

- 模式中第二个字符不为`*`，直接判断模式字符和字符串字符是否相等，或者模式字符是否为`.`
- 模式中第二个字符为`*`
  - 模式上指针后移两个字符，这相等于`*`和它前面的字符被忽略了
  - 如果模式字符和字符串字符相等，则字符串上指针向后移动一个字符，而模式上有两种选择
    - 模式上向后移动两个字符
    - 模式上的指针不动

### 代码实现

```c++
class Solution {
public:
    bool match(char* str, char* pattern)
    {
        if (str == nullptr || pattern == nullptr)
            return false;
        return matchCore(str, pattern);
    }
    bool matchCore(char * str, char * pattern){
        if (*str == '\0' && *pattern == '\0')
            return true;
        if (*str != '\0' && *pattern == '\0')
            return false;
        if (*(pattern+1) == '*'){
            if (*pattern == *str || *pattern == '.' && *str != '\0')
                return matchCore(str+1, pattern) 
                || matchCore(str+1, pattern+2)
                || matchCore(str, pattern+2);
            else
                return matchCore(str, pattern+2);
        }else{
            if (*pattern == *str || *pattern == '.' && *str != '\0')
                return matchCore(str+1, pattern+1);
        }
        return false;
    }
};
```

## 表示数值的字符串

### 题目描述

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

### 思路分析

表示数字的字符串应该匹配模式`A[.B][e|EC]`，比如，-12.35e-4，其中A表示-12，B表示35，C表示-4。B部分和C部分可以不存在。

- 首先判断A部分是否满足：直接判断A是否为一个负整数或者正整数即可
- 然后判断B是否满足：判断B是否唯一个正整数
- 最后判断C是否满足：判断C是否唯一个负整数或正整数



### 代码实现

```c++
class Solution {
public:
    bool isNumeric(const char* str)
    {
        if (str == nullptr)
            return false;
        bool numeric = scanInteger(&str);
        //A
        if (*str == '.'){
            ++str;
            //使用||的原因
            //1. .3232 没有整数部分是满足条件的
            //2. 3444. 没有小数部分也是满足条件的
            //3. 23.44 整数和小数部分都有
            numeric = scanUnsignedInteger(&str) || numeric;
        }
        if (*str == 'e' || *str == 'E'){
            ++str;
            //使用&&的原因
            //e 后面的部分必须是一个整数，而且e前面的部分也必须满足条件
            // e或E前面部分没有数字时，字符串不能表示数字，比如 .e1,e1
            //e或E后面部分没有没有整数时，字符串也不能表示数字，比如23e, 12e+5.4
            numeric = numeric && scanInteger(&str);
        }
        return numeric && *str == '\0';
    }
    
    bool scanInteger(const char ** str){
        if (**str == '+' || **str == '-')
            ++(*str);
        return scanUnsignedInteger(str);
    }
    
    bool scanUnsignedInteger(const char ** str){
         const char * before = * str;
        while(**str != '\0' && **str >= '0' && **str <= '9')
            ++(*str);
        return *str > before; //str中存在大于1个0~9数字时，返回true
    }

};
```

## 调整数组顺序使奇数位于偶数前面

### 题目描述

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

### 思路分析

第一种方法：将偶数放到一个临时数组中，并且将原始数组中的奇数往前移动，这时候需要两个指针，第一个指针指向可以覆盖的地方，第二个指针往后遍历所有的元素，判断释放为奇数。

第二种方法：直接将偶数从数组中删除，并且尾部进行添加，这个需要利用vector的特性。

### 代码实现

基本方法：

```c++
class Solution {
public:
    void reOrderArray(vector<int> &array) {
        if (array.size() <= 1)
            return;
        int j = 0;
        vector<int> tmp;
        for (int i = 0; i < array.size(); i++) {
            if (array[i] % 2 == 1)
            {
                array[j] = array[i];
                j++;
            }
            else {
                tmp.push_back(array[i]);
            }
        }
        for (int i = j; i < array.size(); i++)
        {
            array[i] = tmp[i-j];
        }
    }
};
```

第二种方法：

```c++
class Solution {
public:
    void reOrderArray(vector<int> &array) {
        if (array.size() <= 1)
            return;
        auto cur = array.begin();
        int size = array.size();
        while(size)
        {
            if (0 == (*cur & 0x1))
            {
                int tmp = * cur;
                cur = array.erase(cur);
                array.push_back(tmp);
            }
            else{
                cur++;
            }
            size--;
        }
    }
};
```





两个字符串对应数字的加法：

```C++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

string addTwoString(const string str1, const string str2)
{
    int lenStr1 = str1.size();
    int lenStr2 = str2.size();
    vector<char> vec;
    string res;
    int k = 0;
    int temp;
    while(lenStr1 != 0 || lenStr2 != 0)
    {
        int temp = 0;
        if (lenStr1 == 0)
            temp += 0;
        else
            temp += str1[lenStr1-1] - '0';
        if (lenStr2 == 0)
            temp += 0;
        else
            temp += str2[lenStr2-1] - '0';
        temp += k;
        if (temp > 9)
        {
            vec.insert(vec.begin(), '0'+temp%10);
            k = 1;
        }else
        {
            vec.insert(vec.begin(),'0' + temp);
        }
        --lenStr1;
        --lenStr2;
    }
    for (int i = 0; i < vec.size(); ++i)
        res += vec[i];
    return res;
}

int main()
{
    string str1;
    string str2;
    cin >> str1;
    cin >> str2;
    string res = addTwoString(str1, str2);
    cout << res << endl;
    return 0;
}
```

## 链表中倒数第k个结点

### 题目描述

输入一个链表，输出该链表中倒数第k个结点。

### 思路分析

双指针法。

### 代码实现

```C++
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
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if (pListHead == nullptr || k == 0)
            return nullptr;
        ListNode* pFirstHead = pListHead;
        int i;
        for (i = 0; i < k && pFirstHead != nullptr; ++i)
            pFirstHead = pFirstHead->next;
        if (pFirstHead == nullptr && i != k) //第一个指针到达末尾，并且i还没有等于k
            return nullptr;
        ListNode* pSecondHead = pListHead;
        while(pFirstHead != nullptr)
        {
            pFirstHead = pFirstHead->next;
            pSecondHead = pSecondHead->next;
        }
        return pSecondHead;
    }
};
```

## 链表中环的入口节点

### 题目

给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

### 思路分析

- 第一步：通过双指针法，判断是否存在环
- 第二步：如果存在环，两个指针相遇，然后创建一个新的指针走一圈回到相遇点，来计算环的大小n。
- 第三步：利用双指针法，将第一个指针先走n步，然后两个指针同步走，相遇处就是链表中环的入口。

### 代码实现

```c++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        if (pHead == nullptr || pHead->next == nullptr)
            return nullptr;
        //判断是否有环
        ListNode* pFastHead = pHead->next->next;
        ListNode* pLowHead = pHead->next;
        while(pLowHead != pFastHead && pFastHead != nullptr)
        {
            pLowHead = pLowHead->next;
            pFastHead = pFastHead->next->next;
        }
        if (pFastHead == nullptr)
            return nullptr;
        //计算环的大小
        ListNode* cycle = pFastHead;
        int count = 1;
        while(cycle->next != pFastHead)
        {   
            cycle = cycle->next;
            ++count;
        }
        //寻找环的入口点
        ListNode* pNode1 = pHead;
        for (int i = 0; i < count; ++i)
            pNode1 = pNode1->next;
        ListNode* pNode2 = pHead;
        while(pNode1 != pNode2)
        {
            pNode1 = pNode1->next;
            pNode2 = pNode2->next;
        }
        return pNode1;
    }
};
```

## 链表反转

### 题目描述

输入一个链表，反转链表后，输出新链表的表头。

### 思路分析

略

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
    ListNode* ReverseList(ListNode* pHead) {
        if (pHead == nullptr || pHead->next == nullptr)
            return pHead;
        ListNode* pFirst = pHead;  
        ListNode* pSecond = pHead->next;
        ListNode* pThrid = pSecond->next;
        pFirst->next = nullptr;
        pSecond->next = pFirst;
        while(pThrid != nullptr)
        {
            pFirst = pSecond;
            pSecond = pThrid;
            pThrid = pThrid->next;
            pSecond->next = pFirst;
        }
        return pSecond;
    }
};
```

## 合并两个排序的链表

### 题目描述

输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

### 思路分析

两个指针指向两个链表相互比较。

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
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if(pHead1 == nullptr && pHead2 == nullptr)
            return nullptr;
        if(pHead1 == nullptr)
            return pHead2;
        if(pHead2 == nullptr)
            return pHead1;
        ListNode* pHead = new ListNode(0); //注意这里最好不要这样做，因为在堆上申请内存，还需要释放
        ListNode* res = pHead;
        while(pHead1 && pHead2)
        {
            if(pHead1->val > pHead2->val)
            {
                pHead->next = pHead2;
                pHead2 = pHead2->next;
            }
            else
            {
                pHead->next = pHead1;
                pHead1 = pHead1->next;
            }
            pHead = pHead->next;
        }
        if(pHead1 == nullptr)
            pHead->next = pHead2;
        if(pHead2 == nullptr)
            pHead->next = pHead1;
        return res->next;
    }
};
```

递归版本：

```c++
class Solution {
public:
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if(pHead1 == nullptr && pHead2 == nullptr)
            return nullptr;
        if(pHead1 == nullptr)
            return pHead2;
        if(pHead2 == nullptr)
            return pHead1;
        ListNode* pHead = nullptr;
        if(pHead1->val > pHead2->val)
        {
            pHead= pHead2;
            pHead->next = Merge(pHead1, pHead2->next);
        }
        else
        {
            pHead = pHead1;
            pHead->next = Merge(pHead1->next, pHead2);
        }
        return pHead;
    }
};
```

## 树的子结构

### 题目描述

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

![img](https://uploadfiles.nowcoder.com/images/20160729/216169_1469778977815_2A1C26140A10B4B41AD6325E67FDCBA0)

左侧的树是右侧树的一个子树。

### 思路分析

通过两层递归实现。

第一层递归实现在树A中找到与根节点的值一样的节点。

第二层递归实现判断树A中以R为根节点的子树是不是和树B具有相同的结构。



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
 
    bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        bool res = false;
        if (nullptr != pRoot2 && nullptr != pRoot1)
        {
            if(pRoot1->val == pRoot2->val)
                res = DoesTree1HaveTree2(pRoot1, pRoot2);
            if(!res)
                res = HasSubtree(pRoot1->left, pRoot2);
            if(!res)
                res = HasSubtree(pRoot1->right, pRoot2);
        }
        return res;
    }
    bool DoesTree1HaveTree2(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        if(pRoot2 == nullptr)
            return true;
        if(pRoot1 == nullptr)
            return false;
        if (pRoot1->val != pRoot2->val)
            return false;
        return DoesTree1HaveTree2(pRoot1->left, pRoot2->left) 
            && DoesTree1HaveTree2(pRoot1->right, pRoot2->right);
    }

};
```

为了确保代码的完整、正确。我们应该确保能够通过各种测试用例。如果树节点的值不为整数，而是double类型。上述代码就无法得到正确的结果。于是有了如下代码：

```C++
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
 
    bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        bool res = false;
        if (nullptr != pRoot2 && nullptr != pRoot1)
        {
            if(Equal(pRoot1->val, pRoot2->val))
                res = DoesTree1HaveTree2(pRoot1, pRoot2);
            if(!res)
                res = HasSubtree(pRoot1->left, pRoot2);
            if(!res)
                res = HasSubtree(pRoot1->right, pRoot2);
        }
        return res;
    }
    bool DoesTree1HaveTree2(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        if(pRoot2 == nullptr)
            return true;
        if(pRoot1 == nullptr)
            return false;
        if (!Equal(pRoot1->val, pRoot2->val))
            return false;
        return DoesTree1HaveTree2(pRoot1->left, pRoot2->left) 
            && DoesTree1HaveTree2(pRoot1->right, pRoot2->right);
    }
    bool Equal(double num1, double num2)
    {
        if((num1 - num2) < 1e-8 && (num1-num2) > -1e-8)
            return true;
        else
            return false;
    }
};
```

