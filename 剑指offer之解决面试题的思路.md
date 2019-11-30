---
title: 剑指offer-解决面试题的思路
date: 2019-09-30 13:28:50
tags:  面试题目
mathjax: true

---

剑指offer第四章的题目练习和重要知识点。<!--more-->

# 画图让抽象问题形象化

## 二叉树的镜像

### 题目描述

操作给定的二叉树，将其变换为源二叉树的镜像。

**输入描述:**

```
二叉树的镜像定义：源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5
```

### 思路分析

递归实现。直接分析最小问题，交换一个节点的左右子树，然后递归处理该节点的左右子树。

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
    void Mirror(TreeNode *pRoot) {
        if (pRoot == nullptr)
            return;
        if(pRoot->left == nullptr && pRoot->right == nullptr)
            return;
        
        TreeNode* temp = pRoot->left;
        pRoot->left = pRoot->right;
        pRoot->right = temp;
        if(pRoot->right)
            Mirror(pRoot->right);
        if(pRoot->left)
            Mirror(pRoot->left);
        return;
    }
};
```



## 对称的二叉树

### 题目描述

请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

### 思路分析



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
class Solution {                                                                         public:
    bool isSymmetrical(TreeNode* pRoot)
    {
        return isSymmetrical(pRoot, pRoot);
    }
    
    bool isSymmetrical(TreeNode* pRoot1, TreeNode* pRoot2)
    {
        if(nullptr == pRoot1 &&  nullptr == pRoot2)
            return true;
        if(nullptr == pRoot1 || nullptr == pRoot2)
            return false;
        if (pRoot1->val != pRoot2->val)
            return false;
        return isSymmetrical(pRoot1->left, pRoot2->right)
            && isSymmetrical(pRoot1->right, pRoot2->left);
    }
};
```

非递归栈实现：

```C++
/*
* DFS使用stack来保存成对的节点
* 1.出栈的时候也是成对成对的 ，
1.若都为空，继续；
2.一个为空，返回false;
3.不为空，比较当前值，值不等，返回false；
* 2.确定入栈顺序，每次入栈都是成对成对的，如left.left， right.right ;left.rigth,right.left
*/
class Solution {
public:
    bool isSymmetrical(TreeNode* pRoot)
    {
        if(nullptr == pRoot)
            return true;
        stack<TreeNode*> s;
        s.push(pRoot->left);
        s.push(pRoot->right);
        while(!s.empty())
        {
            TreeNode* right = s.top();
            s.pop();
            TreeNode* left = s.top();
            s.pop();
            if(nullptr == right && nullptr == left)
                continue;
            if(nullptr == right || nullptr == left)
                return false;
            if (right->val != left->val)
                return false;
            s.push(left->left);
            s.push(right->right);
            s.push(left->right);
            s.push(right->left);
        }
        return true;
    }

};
```

非递归队列实现：

```c++
/*
* BFS使用Queue来保存成对的节点，代码和上面极其相似
* 1.出队的时候也是成对成对的
1.若都为空，继续；
2.一个为空，返回false;
3.不为空，比较当前值，值不等，返回false；
* 2.确定入队顺序，每次入队都是成对成对的，如left.left， right.right ;left.rigth,right.left
*/
class Solution {
public:
    bool isSymmetrical(TreeNode* pRoot)
    {
        if(nullptr == pRoot)
            return true;
        queue<TreeNode*> q;
        q.push(pRoot->left);
        q.push(pRoot->right);
        while(!q.empty())
        {
            TreeNode* left = q.front();
            q.pop();
            TreeNode* right = q.front();
            q.pop();
            if (nullptr == left && nullptr == right)
                continue;
            if (nullptr == left || nullptr == right)
                return false;
            if (left->val != right->val)
                return false;
            q.push(left->left);
            q.push(right->right);
            q.push(left->right);
            q.push(right->left);
        }
        return true;
    }

};
```

## 顺时针打印矩阵

### 题目描述

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

### 思路分析

第一步：判断需要循环多少圈

第二步：执行两行和两列的遍历。

### 代码实现

```c++
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        vector<int>res;
        res.clear();
        int row=matrix.size();//行数
        int column=matrix[0].size();//列数
        //计算打印的圈数
        int circle = ((row < column ? row : column) -1) / 2 + 1;
        for (int i = 0; i < circle; ++i)
        {
            //从左到右打印
            for(int j = i; j < column-i; ++j)
                res.push_back(matrix[i][j]);
            //从上到下打印
            for(int k = i+1; k < row-i; ++k)
                res.push_back(matrix[k][column-i-1]);
            //从右到左打印
            for(int j = column-i-2; j>=i&&(row-i-1)!=i; --j) 
                //row-i-1!=i防止只有一行的时候重复打印
                res.push_back(matrix[row-i-1][j]);
            //从下到上打印
            for(int k = row-i-2; k>i&&(column-i-1)!=i; --k)
                //column-i-1!=i防止一列的时候重复打印
                res.push_back(matrix[k][i]);
        }
        return res;
    }
};
```

# 举例让抽象问题具体化

## 包含min函数的栈

###  题目描述

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

### 思路分析



### 代码实现

```c++
class Solution {
public:
    stack<int> output;
    stack<int> input;
    void push(int value) {
        if(output.empty() || output.top()>value)
            output.push(value);
        input.push(value);
    }
    void pop() {
        if (input.top() == output.top())
        {
            output.pop();
            input.pop();
        }
        else
        {
            input.pop();
        }
    }
    int top() {
        return input.top();
    }
    int min() {
        return output.top();
    }
};
```

## 栈的压入、弹出序列

### 题目描述

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

### 思路分析

利用一个栈，首先将压入序列第一个元素压入栈中，然后判断栈顶元素是否等于压栈序列的第一个元素。

然后，如果相等，就将栈顶元素弹出，压栈序列往后移动一个元素，直到栈顶为空。

依次，将压入序列压入栈中，并回到第一步。（代码实现见最后一个版本）

### 代码实现

```C++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        int lenPush = pushV.size();
        int lenPop = popV.size();
        if (lenPush != lenPop || lenPush == 0)
            return false;
        int i = 0;
        int j = 0;
        stack<int> sta;
        while(i < lenPush || !sta.empty()) //栈为空且压入序列已经遍历完，结束循环
        {
            while (i < lenPush && pushV[i] != popV[j]) //如果压入序列对应的元素与弹出序列的元素不相等，则将元素序列元素压入栈中
            {
                sta.push(pushV[i++]);
            }
            while(i < lenPush && j < lenPush && pushV[i] == popV[j])//当压入序列的元素和弹出序列的元素相等，两个同步往后移动
            {
                i++;
                j++;
            }
            //压入序列到达了末尾，但是栈不为空时
            while (i == lenPush && !sta.empty())
            {
                if (j < lenPush && sta.top() == popV[j])//判断栈顶元素是否与弹出序列中的元素是否相等
                {
                    sta.pop();
                    j++;
                }
                else      //不相等时，直接返回false
                    return false;
           }
           if (i == lenPush && j != lenPush) //如果压入序列到达了末尾，但是弹出序列还没有到达末尾，则说明不相等
               return false;
        }
        return true;
    }
};
```

优秀的代码：

``` c++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        int lenPush = pushV.size();
        int lenPop = popV.size();
        if (lenPush != lenPop || lenPush == 0)
            return false;
        stack<int> sta;
        int j = 0;
        for (int i = 0; i < lenPush; ++i){
            sta.push(pushV[i]);
            while(!sta.empty() && sta.top() == popV[j])
            {
                sta.pop();
                ++j;
            }
        }
        return sta.empty();
    }
};
```

## 从上往下打印二叉树

### 题目描述

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

### 思路描述

通过队列实现广度优先遍历。

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
    vector<int> PrintFromTopToBottom(TreeNode* root) {
         vector<int> result;
        if (nullptr == root)
            return result;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty())
        {
            TreeNode* temp = que.front();
            que.pop();
            result.push_back(temp->val);
            if (temp->left)
                que.push(temp->left);
            if (temp->right)
                que.push(temp->right);
        }
        return result;
    }

};
```



## 把二叉树打印成多行

### 题目描述

从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

### 思路分析

通过队列实现，不过每次需要计算每层节点的数量。

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
        vector<vector<int> > Print(TreeNode* pRoot) {
            vector<vector<int> > result;
            if(!pRoot)
                return result;
            queue<TreeNode*> que;
            que.push(pRoot);
            while(!que.empty())
            {
                int n = que.size(); //计算该层有多少节点
                vector<int> line;
                while(n--)
                {
                    TreeNode* temp = que.front();
                    que.pop();
                    line.push_back(temp->val);
                    if(temp->left)
                        que.push(temp->left);
                    if(temp->right)
                        que.push(temp->right);
                }
                result.push_back(line);
            }
            return result;
        }
};
```



## 按之字顺序打印二叉树

### 题目描述

请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

### 思路分析

通过两个栈实现。一个栈存储奇数层的节点，另一个栈存储偶数层的节点。对于奇数层的节点，先将左节点压入栈中，然后再将右节点压入栈中。而对于偶数层的节点，先将右节点压入栈中，然后再将左节点压入栈中。

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
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int> > result;
        stack<TreeNode*> sta1;
        stack<TreeNode*> sta2;
        int level = 1;
        if(pRoot)
        {
            sta1.push(pRoot);
        }
        while(!sta1.empty() || !sta2.empty())
        {
            vector<int> line;
            int n = sta1.empty() ? sta2.size() : sta1.size();
            while(n--)
            {
                if (level % 2 == 1)
                {
                    TreeNode * temp = sta1.top();
                    sta1.pop();
                    line.push_back(temp->val);
                    if(temp->left)
                        sta2.push(temp->left);
                    if(temp->right)
                        sta2.push(temp->right);
                }
                else{
                    TreeNode * temp = sta2.top();
                    sta2.pop();
                    line.push_back(temp->val);
                    if(temp->right)
                        sta1.push(temp->right);
                    if(temp->left)
                        sta1.push(temp->left);
                }
            }
            ++level;
            result.push_back(line);
        }
        return result;
    }
    
};
```

## 二叉搜索树的后序遍历序列

### 题目描述

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

### 思路分析

对于二叉搜索树后序遍历，最后一个元素是根节点。

所以对于序列，前面部分的数小于最后一个元素，后面部分大于最后一个元素。

而对于前面部分，也是一个二叉搜索树的后序遍历，通过上面的思路继续分析，后面部分同样。

### 代码实现

```c++
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        int n = sequence.size();
        int i = 0;
        for(i = 0; i < n-1; i++)
        {
            if (sequence[i] > sequence[n-1])
                break;
        }
        int j = i;
        for (; j < n-1; j++)
        {
            if(sequence[j] < sequence[n-1])
                break;
        }
        if (j != n-1)
            return false;
        bool left = true;
        if (i > 0)
            left = VerifySquenceOfBST(vector<int>(sequence.begin(),sequence.begin()+i));
        bool right = true;
        if(i < n - 1)
            right = VerifySquenceOfBST(vector<int>(sequence.begin()+i,sequence.begin()+n-1));
        return (left && right);
    }
};
```

## 二叉树中和为某一值的路径

### 题目描述

输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

### 思路分析

首先注意一点，路径必须是从根节点到叶子节点的完整路径之和为某一值。

通过递归实现，每次判断当前节点的值是否等于某一值，如果不等于，则继续判断左子树和右子树，此时的某一值需要减去当前节点的值。

如果当前节点的值等于某一值，并且当前节点是叶子节点（左右子树都为空），则该路径满足要求，放入结果集中。

注意，为了保存结果，需要将最后的结果放入参数中，并且设为引用。

但是，一个路径的结果（path）不能设为引用。

### 代码实现

````c++
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
    vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
        vector<vector<int> > res;
        vector<int> path;
        FindPathHelper(root, expectNumber, path, res);
        return res;
    }
    
    void FindPathHelper(TreeNode* root, int expectNumber, vector<int> path, vector<vector<int> > & res)
    {
        if(nullptr == root)
            return;
        path.push_back(root->val);
        if (expectNumber == root->val && root->left == nullptr && root->right == nullptr)
        {
            res.push_back(path); 
        }

        FindPathHelper(root->left, expectNumber - root->val, path, res);
        FindPathHelper(root->right, expectNumber -root->val, path, res);
    }
        
};
````

# 分解让复杂问题简单化

## 复杂链表的复制

### 题目描述

输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

### 思路分析

第一种方法：分为三个步骤：

- 将每个节点复制一份，并插入在当前节点的后面
- 将复制节点的随机指针指向前一个节点（拷贝之前的节点）随机指针指向的节点的下一个节点
- 将复制节点和原先节点进行分离

第二种方法：用一个map实现，存储复制节点和原先节点的对应关系，用于复制随机指针



### 代码实现

```c++
/*
struct RandomListNode {
    int label;
    struct RandomListNode *next, *random;
    RandomListNode(int x) :
            label(x), next(NULL), random(NULL) {
    }
};
*/
class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead)
    {
        if (nullptr == pHead)
            return nullptr;
        RandomListNode* copy;
        RandomListNode* p = pHead;
        
        //每个节点复制
        while(nullptr != p)
        {
            copy = new RandomListNode(p->label);
            copy->next = p->next;
            p->next = copy;
            p = copy->next;
        }
        //随机指针复制
        p = pHead;
        while(nullptr != p)
        {
            copy = p->next;
            copy->random = p->random ? p->random->next : nullptr;
            p = copy->next;
        }
      
        //链表分割
        
        RandomListNode* pCloneHead = pHead->next;
        RandomListNode* old = pHead;
        copy = pHead->next;
        while(nullptr != copy->next)
        {
            old->next = copy->next;
            old = old->next;
            copy->next = old->next;
            copy = copy->next;
        }
        copy->next = nullptr;
        old->next = nullptr;
        return pCloneHead;
    }
};
```

第二方法：

```c++
class Solution {
public:
    RandomListNode* Clone(RandomListNode* pHead)
    {
        if (nullptr == pHead)
            return nullptr;
        RandomListNode* copy = new RandomListNode(0);
        RandomListNode* pCopyHead = copy;
        RandomListNode* p = pHead;
        map<RandomListNode*, RandomListNode*> copyToRaw;
        while(p){
            copy->next = new RandomListNode(p->label);
            copy = copy->next;
            copyToRaw[p] = copy;
            p = p->next;
        }
        
        copy = pCopyHead->next;
        p = pHead;
        while(copy)
        {
            if (nullptr != p->random)
                copy->random = copyToRaw[p->random];
            p = p->next;
            copy = copy->next;
        }
        return pCopyHead->next;
    }
};
```

## 二叉搜索树与双向链表

### 题目描述

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

### 思路分析

```
1.将左子树构造成双链表，并返回链表头节点。
2.定位至左子树双链表最后一个节点，用一个变量记录最后一个节点，c++中使用引用的参数或者指针的指针。
3.如果左子树链表不为空的话，将当前root追加到左子树链表。
4.将右子树构造成双链表，并返回链表头节点。
5.如果右子树链表不为空的话，将该链表追加到root节点之后。
6.根据左子树链表是否为空确定返回的节点。
```

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
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        if (nullptr == pRootOfTree)
            return nullptr;
        
        TreeNode *pre = nullptr;
        convertHelper(pRootOfTree, pre);
        
        TreeNode * res = pRootOfTree;
        while (nullptr != res->left)
            res = res->left;
        return res;
    }
    
    void convertHelper(TreeNode* cur, TreeNode*& pre)
    {
        if(nullptr == cur)
            return ;
        convertHelper(cur->left, pre);
        
        cur->left = pre;
        if (pre) pre->right = cur;
        pre = cur;
        
        convertHelper(cur->right, pre);
    }
};
```

## 序列化二叉树

### 题目描述

请实现两个函数，分别用来序列化和反序列化二叉树



二叉树的序列化是指：把一棵二叉树按照某种遍历方式的结果以某种格式保存为字符串，从而使得内存中建立起来的二叉树可以持久保存。序列化可以基于先序、中序、后序、层序的二叉树遍历方式来进行修改，序列化的结果是一个字符串，序列化时通过 某种符号表示空节点（#），以 ！ 表示一个结点值的结束（value!）。

二叉树的反序列化是指：根据某种遍历顺序得到的序列化字符串结果str，重构二叉树。

### 思路分析

前序遍历实现序列化二叉树，采用递归实现，每次见节点的值转换成字符串加入到一个string中，并加上一个分隔符；对于为空的节点，将#加入到string中，并且递归结束。

而对于反序列化，同样采用递归实现。递归时，主要要将char *设置为引用或者指针的指针。如果遇到字符为#，直接返回nullptr，否则计算`!`之前的字符串转换成int，利用该int创建节点，并且递归创建该节点的左子节点和右子节点。

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
    char* Serialize(TreeNode *root) {    
        if (nullptr == root)
            return nullptr;
        string str;
        SerizlizeHelper(root, str);
        char *ret = new char[str.length() + 1];
        size_t i;
        for(i = 0; i < str.length(); i++){
            ret[i] = str[i];
        }
        ret[i] = '\0';
        return ret;
    }
    void SerizlizeHelper(TreeNode* root, string& str)
    {
        if (nullptr == root)
        {
            str += '#';
            return;
        }
        str += to_string(root->val);
        str += '!';
        SerizlizeHelper(root->left, str);
        SerizlizeHelper(root->right, str);
    }
    TreeNode* Deserialize(char *str) {
        if (nullptr == str)
            return nullptr;
        
        TreeNode * root = DeserializeHelper(str);
        return root;
    }
    TreeNode* DeserializeHelper(char* & str) {
        if (*str == '#')
        {
            ++(str);
            return nullptr;
        }
        int num = 0;
        while (*str != '\0' && *str != '!'){
            num = num * 10 + (*str - '0');
            ++str;
        }
        
        TreeNode * root = new TreeNode(num);
        if (*str == '\0')
            return root;
        else
            str++;
        
        root->left = DeserializeHelper(str);
        root->right = DeserializeHelper(str);
        return root; 
    }
};
```



## 字符串的排列

### 题目描述

输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

输入描述:

```
输入一个字符串,长度不超过9(可能有字符重复),字符只包括大小写字母。
```

### 思路分析

对于字符串，每次交换两个位置的字符。具体分析如下图:

![](https://uploadfiles.nowcoder.com/images/20170705/7578108_1499250116235_8F032F665EBB2978C26C4051D5B89E90)

首先交换A和A，然后固定A；然后交换B和B；然后交换B和C。

### 代码实现

```c++
class Solution {
public:
    vector<string> Permutation(string str) {
        vector<string> vecString;
         if (0 == str.size())
            return vecString;
        Permutation(str, vecString, 0);
        sort(vecString.begin(), vecString.end());
        return vecString;
        
    }
    void Permutation(string& str, vector<string>& vecString, int begin)
    {
        if (begin == str.size()-1) //如果到达最后一个字符，直接将str放入结果集中
        {
            if (find(vecString.begin(), vecString.end(), str) == vecString.end())
                vecString.push_back(str);
        }
        else
        {
            for (int i = begin; str[i] != '\0'; ++i)
            {
                swap(str[i], str[begin]); //第一个字符和后面的字符交换
                Permutation(str, vecString, begin+1);
                swap(str[i], str[begin]); //将之前交换的交换回来，得到最开始的字符串
            }
        }
    }
    
    void swap(char& a, char& b)
    {
        char temp = a;
        a = b;
        b = temp;
    }
        
};
```

