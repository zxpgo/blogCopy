---
title: 剑指offer-面试需要的基础知识
date: 2019-08-22 13:28:50
tags:  面试题目
mathjax: true
---



剑指offer第二章的题目练习和重要知识点。<!--more-->

# 编程语言

编程语言的题目分为三种：

- 语言中的关键字理解
- 分析一段代码，释放出错或者结果多少
- 编写一个重要的函数：比如构造函数、赋值构造函数、复制构造函数、析构函数、重载运算符等等

面试中经常会问到C++一些关键字，比如类型转换关键字，sizeof。

- `static_const`: 主要用于C++内置类型之间的转换，没有运行时类型的检测来保证转换的安全性。
  - 用于子类和基类之间的指针或引用之间的转换，这种转换把子类转的指针或引用转换为基类是安全的；进行向下转换，把基类的指针或引用转换为子类时，由于没有动态类型检测，所以是不安全的。
  - 把void类型的指针转换成目标类型的指针，同样不安全
  - 不能用于两个不相关的类型转换
  - 不能把`const`对象转换成非`const`对象

- `const_cast`: 编译时执行，作用：可以使一个本来不是const类型的数据转换成const类型，或者把const属性去掉；不能用于不同的类型间转换。
- `reinterpret_cast`: 可以把任何的内置数据类型转换为其他的类型，同时也可以把任何类型的指针转换其他在新的指针；它的机理是对二进制进行重新解释，不会改变原来的格式。
- `dynamic_cast<type>(expression)`: 该运算符间expresssion转换成type类型的对象，type类型必须是类的指针、类型的引用或者空类型的指针。
  - 如果type是一个指针类型，那么expression也必须是指针类型，如果type是一个引用类型，那么expression也必须是一个引用类型。
  - type是一个空类型的指针，在运行的时候，就会检测expression的实际类型，结果是一个由expression决定的指针类型。
  - 在运行的时候决定真正的类型，如果向下转换是安全的，就会返回一个转换后的指针，若不安全，就会返回一个空指针。
  - 主要用于上下之间的转换，也可以用于类型之间的转换。上行转换时和static_cast效果一样，下行转换时，具有检测功能，比`static_cast`安全。   

## 赋值运算符函数

如下为类型CMyString的声明，为该类型添加赋值运算符。

```C++
class CMyString{
public:
	CMyString(char * pData=nullptr);
    CMyString(const CMyString & str);
    ~CMyString();
private:
    char * m_pData;
};
```

初级程序员解法：

```C++
	CMyString & CMyString::operator=(const CMyString & str){
       	if (this != &str){
        	delete []m_pData;
            m_pData = nullptr;
            m_pData = new char[strlen(str.m_pData)+1];
            strcpy(m_pData, str.m_pData);
        }
        return *this;
    }
```

高级程序员：

如果赋值过程中内存不足，导致new char抛出异常，则m_pData将是一个空指针，这样非常容易导致程序崩溃。一旦赋值运算符内部出现异常，CMString的实例就不再有效。

有两种办法接近，一种是在delete之前先用new分配一块内存。另一种就是先创建一个临时变量，再交换临时变量与原来的实例。

```C++
  CMyString & operator=(const CMyString & str){
       	if (this != &str){
        	CMyString strTemp(str);
            
            char *pTemp = strTemp.m_pData;
            strTemp.m_pData = m_pData;
            m_pData= pTemp;
        }
        return *this;
    }
```

## 实现单例模式

```C++
class Singleton{
private:
    Singleton(){}
    static Singleton * instance;
public:
    static Singleton * get(){
        if(instance == nullptr){
            instance = new Singleton();
        }
        return instance;
	}
};
Singleton * Singleton::instance = nullptr;
```

上述实现版本，线程不安全。

```C++
Singleton * Singleton::get() {
	Lock lock;
	if (instance == nullptr) {
		instance = new Singleton();
	}
	return instance;
}
```

上述版本版本实现了线程安全，但是锁的代价太大。于是考虑采用双检查锁

```C++
Singleton * Singleton::get() {
	if (instance == nullptr) { //双检查锁
		Lock lock;
		if (instance == nullptr){
			instance = new Singleton();
		}
	}
	return instance;
}
```

但是双检查锁存在问题：m_instancee= new Singleton();在指令级别，可以分为三个步骤： 分配内存空间，调用构造函数初始化内存空间，将内存地址赋给m_instance。而reorder的顺序可能是：分配内存空间，将内存地址赋给m_instance,调用构造函数初始化内存空间。线程A执行到将内存空间赋给m_instance,切换到线程B，此时m_instance不为nullptr，所以可以返回m_instance，但此时还是无效的。于是出现了如下版本（采用原子操作）：

```C++
//C++ 11版本之后的跨平台的实现(volatile)

std::atomic<Singleton*> Singleton::m_instance;
std::mutex Singleton::m_mutex;

Singleton * Singleton::getInstance() {
	Singleton * tmp = m_instance.load(std::memory_order_relaxed);
	std::atomic_thread_fence(std::memory_order_acquire);
	if (tmp == nullptr) {
		tmp = new Singleton;
		std::atomic_thread_fence(std::memory_order_release);
		m_instance.store(tmp, std::memory_order_relaxed);
	}
	return tmp;
}
```



# 数据结构

## 数组

数组名是一个指针，指向数组的第一个车元素。我们可以用一个指针来访问数组，但是C/C++没有记录数组的大小，因此再用指针访问数组时，要确保没有超出边界。下面是一个例子来了解数组和指针的区别。

```C++
void GetSize(int data[])
{
    return sizeof(data);
}

int main(){
    int data1[] = {1,2,3,4,5};
    int size1 = sizeof(data1);
    
    int * data2 = data1;
    int size2 = sizeof(data2);
    
    int size3 = GetSize(data1);
    
    printf("%d, %d, %d", size1, size2, size3);
}
```

输出结果为： 20，4，4。data1是一个数组名，sizeof(data1)是求数组大小。data2是一个指针，所以sizeof(data2)是4个字节。在C++中，**当数组作为函数的参数进行传递时，数组就自动退化为同类型的指针。**因此尽管GetSize窜参数data被声明为数组，但它退化为指针，所以size3的结果是4。

对于C++中，为了节省数组开销，可以使用STL的vector容器。但是由于vector的实现，如果数组的大小经常变动的话，会来时间性能上的影响。vector当大小不够时，会成倍增长，并将原来的元素复制到新的内存中。

### 数组中重复得数字

#### 题目描述

在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

#### 思路分析

看到题目，可能想到的是通过一个set容器来记录出现过的元素，每次判断元素是否在set中，如果发现实现在set中，就直接返回true。

上面是最基本的方法，一个更好的方法是：根据数组的特点，数组长度为n，元素在0到n-1之间，所以可以利用现有数组设置标志，当一个数字被访问过后，可以设置对应位上的数 + n，之后再遇到相同的数时，会发现对应位上的数已经大于等于n了，那么直接返回这个数即可。

#### 代码实现

```C++
//基础版本
class Solution {
public:
    // Parameters:
    //        numbers:     an array of integers
    //        length:      the length of array numbers
    //        duplication: (Output) the duplicated number in the array number
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    bool duplicate(int numbers[], int length, int* duplication) {
        //map<int, int> map_ii;
        set<int> set_i;
        for(int i = 0; i < length; i++){
            if (set_i.find(numbers[i]) != set_i.end())
            {
                *duplication = numbers[i];
                return true;
            }
            else
                set_i.insert(numbers[i]);
        }
        return false;
    }
};

//进阶版本
class Solution {
public:
    // Parameters:
    //        numbers:     an array of integers
    //        length:      the length of array numbers
    //        duplication: (Output) the duplicated number in the array number
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    bool duplicate(int numbers[], int length, int* duplication) {
        for(int i = 0; i < length; i++){
            int index = numbers[i];
            if (index >= length)
                index -= length;
            if (numbers[index] >= length){
                *duplication = index;
                return true;
            }
            numbers[index] = length + numbers[index];
        }
        return false;
    }
};
```

### 二维数组的查找

#### 题目描述

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

#### 思路分析

`i=0,j=column-1` 对于选定的元素`array[i][j]`，如果大于target，则最后一列中不可能存在target元素，此时`j--`；

如果小于target，则第一行中不可能存在target元素，`i++`；

继续判断`array[i][j]`与target的大小，重复以上步骤。

#### 代码实现

```C++
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        int row = array.size();
        int column = array[0].size();
       if (row > 0 && column > 0 && array[row-1][column-1] >= target){
           int i = 0;
           int j = column-1;
           while (i < row && j >= 0){
               if (array[i][j] == target){
                   return true;
               }else if(array[i][j] > target){
                   --j;
               }else{
                   ++i;
               }
           }
       }
        return false;
    }
};
```

## 字符串

C/C++中每个自身都以'\0'作为结尾，这样方便找到字符串尾部。

```C++
char str[10];
strcpy(str, "0123456789");//Error
```

先声明了一个长度为10的字符数组，然后把字符串"0123456789"复制到数组中。而"0123456789"这个字符串看上去只有10个字节，但实际上它的末尾有一个'\0'字符，因此它的实际长度为11字节。要正确复制该字符串，至少需要一个长度11字节的数组。

还有一点需要注意，为了节省内存，C/C++把常量字符串放到单独的一个内存区域。当几个指针赋值给相同的常量字符串时，它们实际上会指向相同的内存地址。但是用常量内存初始化数组，情况却有所不同。下面是一个例子说明了这一点：

```C++
char str1[] = "hello world";
char str1[] = "hello world";

char * str3 = "hello world";
char * str4 = "hello world";

cout << (str1 == str2) << endl;  //false
cout << (str3 == str4) << endl;  //true
```

str1和str2是两个字符串数组，会为它们分配两个长度为12字节的空间，并把"hello world"的内容分别复制到数组中去。这两个初始化地址不同的数组，因此str1和str2的值也不同。

str3和str4是两个指针，无须为它们分配内存已存储字符串的内存，而只需要把它们指向"hello world"在内存中的地址就可以了。因为"hello world"是字符串常量，它在内存中就一个拷贝，因此str3和str4指向的是同一个地址。

### 替换空格

#### 题目描述

请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

#### 思路分析

看到题目，想到直接通过一个for循环和一个string字符串实现，如果遇到空格将`%20`加入到string中，否则将字符直接加到string字符串中，最后将string转换为char *。

第二种方法，在牛客网上看到的（也是剑指offer书上给出的答案），首先通过一个while循环，得到原有字符串的长度和空格的个数。然后将空格的数量乘以2加上原有字符串的长度得到最后结果字符串的长度。最后，将原有字符串从尾部开始，一个一个判断，如果遇到空给将'0','2','%'添加到结果字符串中（注意从尾部开始往字符串头部）。

#### 代码实现

```C++
class Solution {
public:
    void replaceSpace(char *str, int length) {
        string  temp;
        int j = 0, i;
        for (i = 0; i < length; ++i) {
            if (str[i] == ' ') {
                temp += "%20";
            }
            else
                temp += str[i];
        }
        strcpy(str, temp.c_str());
    }
};

//进阶版本
class Solution {
public:
void replaceSpace(char *str,int length) {
        if (str == nullptr || length == 0)
            return;
        int oldLength = 0;   //str字符串长度
        int placementLength = 0;//空格数量
        int i = 0;
    	//计算字符串和空格数量
        while(str[i] != '\0'){
            oldLength++;
            if (str[i] == ' ')
                placementLength++;
            i++;
        }
    	//新字符串长度，注意不要减一，因为最后有一个'\0'
        int newLength = oldLength + placementLength * 2;
        if (newLength > length) //如果length长度小于结果字符串长度，则直接结束
            return ;
        while(oldLength >=0 && newLength > oldLength){
            if (str[oldLength] == ' '){
                str[newLength--] = '0';
                str[newLength--] = '2';
                str[newLength--] = '%';
            }
            else
                str[newLength--] = str[oldLength];
            oldLength--;
        }
        return ;
    }
};
```

## 链表

链表的操作包括：创建、插入节点、删除节点、判断是否有环等等。

```C++
struct ListNode {
	int m_nValue;
	ListNode * m_pNext;
};


//在链表的末尾添加一个节点
void AddToTail(ListNode **pHead, int value) {
	ListNode * pNew = new ListNode();
	pNew->m_nValue = value;
	pNew->m_pNext = nullptr;

	if (*pHead == nullptr) {
		*pHead = pNew;
	}
	else {
		ListNode * pNode = *pHead;
		while (pNode->m_pNext != nullptr)
			pNode = pNode->m_pNext;
		pNode->m_pNext = pNew;
	}
}
```

注意，函数的第一个参数pHead是一个指向的指针。当我们往第一个空链表插入一个节点时，新插入的节点就是链表的头指针。由于此时会改动头指针，因此需要把pHead参数设为指向指针的指针，否则出了这个函数pHead仍然是一个空指针。

由于链表中的内存不是一次性分配的，因为我们无法保证链表的内存和数组一样是连续的。因此，如果现在链表中找到第i个节点，那么我们需要从头节点开始，沿着指向下一个节点的指针遍历链表，它的时间效率为o(n)。下面是在链表中找到第一个含有某值得节点并删除该节点的代码：

```c++

struct ListNode {
	int m_nValue;
	ListNode * m_pNext;
};

void RemoveNode(ListNode ** pHead, int value) {
	if (pHead == nullptr || *pHead == nullptr)
		return;
	ListNode * pToBeDeleted = nullptr;
	if ((*pHead)->m_nValue == value) {
		pToBeDeleted = *pHead;
		*pHead = (*pHead)->m_pNext;
	}
	else {
		ListNode * pNode = *pHead;
		while (pNode->m_pNext != nullptr && pNode->m_pNext->m_nValue != value) {
			pNode = pNode->m_pNext;
		}

		if (pNode->m_pNext != nullptr && pNode->m_pNext->m_nValue == value) {
			pToBeDeleted = pNode->m_pNext;
			pNode->m_pNext = pNode->m_pNext->m_pNext;
		}
	}

	if (pToBeDeleted != nullptr) {
		delete pToBeDeleted;
		pToBeDeleted = nullptr;
	}
}
```

### 从头到尾打印链表

#### 题目描述

输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。

#### 思路分析

在C++中的vector容器，允许从开头插入元素，所以从头遍历链表，然后每次将元素在vector的头部插入。

如果不借助vector，还可以使用容器stack，从头到尾把元素放入栈中，最后从栈顶开始读取元素，这样就实现了从尾到头的遍历。

#### 代码实现

```C++
/**
*  struct ListNode {
*        int val;
*        struct ListNode *next;
*        ListNode(int x) :
*              val(x), next(NULL) {
*        }
*  };
*/
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> vec;
        while(head != nullptr){
            vec.insert(vec.begin(), head->val);
        	head = head->next;
        }
        return vec;
    }
};


//栈实现
/**
*  struct ListNode {
*        int val;
*        struct ListNode *next;
*        ListNode(int x) :
*              val(x), next(NULL) {
*        }
*  };
*/
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> vec;
        stack<int> sta;
        while(head != nullptr){
            sta.push(head->val);
            head = head->next;
        }
        while(!sta.empty()){
            vec.insert(vec.end(),sta.top());
            sta.pop();
        }
        return vec;
    }
};
```

## 树

树的几种遍历： 

- 前序遍历：先访问根节点，再访问左子节点，最后访问右子节点。
- 中序遍历：先访问左子节点，再访问根节点，最后访问右子节点。
- 后序遍历：先访问左子节点，再访问右子节点，最后访问根节点。

这三种遍历都有递归实现和循环实现。

- 宽度优先遍历：先访问树的第一层节点，再访问树的第二层节点……一直到访问最下面一层节点。

二叉树中有很多特例：

- 二叉搜索树：左子节点总是小于或等于根节点，而右子节点总是大于或冬雨根节点。我们可以评价在O(longn)的时间内根据数值在二叉搜索树中找到一个节点。
- 堆：堆分为最大堆和最小堆。在最大堆中根节点的值最大，在最小堆中根节点的值最小。快速找到最小值或最大值的问题都可以用堆来解决。
- 红黑树：红黑树把树中的节点定义为红、黑两种颜色，并通过规则确保从根节点到叶节点的最长路径的长度不超过最短路径的两倍。在C++的STL中，set,map,multiset,multimap等数据结构都是基于红黑树实现的。

### 重建二叉树

#### 题目描述

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

#### 思路分析

本题的实现可以采用递归实现，每次前序遍历的第一个元素为根节点，然后从中序遍历找到根节点所在的位置，该位置前面的元素位于左子树，而右边的元素位于右子树。将位于根节点之前的元素放入一个左子树的中序遍历的数组中，同时将对应的前序遍历的元素放入左子树的前序遍历的数组中；右子树的数组同样处理，不过是在根节点的之后的元素。然后通过递归构造左子树和右子树。

#### 代码实现

```c++
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        if (vin.size() == 0)
            return nullptr;
        TreeNode * pTreeNode = new TreeNode(pre[0]);
        int j = 0;
        for (int i = 0; i < vin.size(); i++){
            if (vin[i] == pre[0]){
                j = i;
                break;
            }
        }
        vector<int> pre_left, pre_right, vin_left, vin_right;
        for (int i = 0; i < j; i++){
            pre_left.push_back(pre[i+1]);
            vin_left.push_back(vin[i]);
        }
        for (int i = j + 1; i < vin.size(); i++){
            pre_right.push_back(pre[i]);
            vin_right.push_back(vin[i]);
        }
        pTreeNode->left = reConstructBinaryTree(pre_left, vin_left);
        pTreeNode->right = reConstructBinaryTree(pre_right, vin_right);
        return pTreeNode;
    }
};
```

### 二叉树的下一个节点

#### 题目描述

给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

#### 思路分析

分三种情况讨论：

- 节点有右子树，则它的下一个节点就是右子树的最左子节点；
- 节点没有右子树，并且节点是其父节点的左子节点，则它的下一个节点就是它的父节点；
- 节点没有右子树，并且节点是其父节点的右子节点，这种情形比较复杂。我们可以沿着指向父节点的指针一直向上遍历，直到找到一个是它父节点的左子节点的节点。如果这样的节点存在，那么这个节点的父节点就是我们要找的下一个节点。

#### 代码实现

```c++
/*
struct TreeLinkNode {
    int val;
    struct TreeLinkNode *left;
    struct TreeLinkNode *right;
    struct TreeLinkNode *next;
    TreeLinkNode(int x) :val(x), left(NULL), right(NULL), next(NULL) {
        
    }
};
*/
class Solution {
public:
    TreeLinkNode* GetNext(TreeLinkNode* pNode)
    {
        if (pNode == nullptr) 
            return nullptr;
        TreeLinkNode * pNextNode = nullptr;
        if(pNode->right != nullptr){//节点有右子树的情形
            TreeLinkNode * pRightNode = pNode->right;
            while(pRightNode->left != nullptr){
                pRightNode = pRightNode->left;
            }
            pNextNode = pRightNode;
        }else if(pNode->next != nullptr){//节点没有右子树的情形，包括在父节点的左子树和不在父节点的左子树上两种情形
            TreeLinkNode * pCurrentNode = pNode;
            TreeLinkNode * pParentNode = pNode->next;
            while(pParentNode != nullptr &&  pCurrentNode == pParentNode->right){ //如果节点是父节点的右子节点
                pCurrentNode = pParentNode;
                pParentNode = pParentNode->next;
            }
            pNextNode = pParentNode;
        }
        return pNextNode;
    }
};
```

## 栈和队列

栈：后进先出

队列：先进先出

操作系统会给每个线程创建一个栈用来存储函数调用时各个函数的参数、返回地址及临时变量。

### 用两个栈实现队列

#### 题目描述

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

#### 思路分析

对于Push动作，直接将元素插入stack1即可。

对于Pop操作，我们需要删除stack1栈底的元素，此时需要将所有元素移动到stack2中，这样stack1栈底的元素变成了stack2栈顶的元素，直接删除stack2栈顶的元素即可，为了保证下一次可以继续进行插入或删除，所以重新将stack2中的元素放入到stack1中，并清空stack2。

#### 代码实现

```c++
class Solution
{
public:
    void push(int node) {
        stack1.push(node);
    }

    int pop() {
        while(!stack1.empty()){
            stack2.push(stack1.top());
            stack1.pop();
        }
        int res = 0;
        if (!stack2.empty()){
            res = stack2.top();
            stack2.pop();
        }
        while(!stack2.empty()){
            stack1.push(stack2.top());
            stack2.pop();
        }
        return res;
    }

private:
    stack<int> stack1;
    stack<int> stack2;
};
```

### 用两个队列实现栈

方式跟用两个栈实现队列相似，记住栈是后进先出，队列是先进先出就行。



#  算法和数据操作

很多算法都有递归和循环两种不同得实现方式。通常基于递归得实现方法代码比较简洁，但性能不如基于循环的实现方法。

通常排序和查找是面试时考察的算法重点。我们应该掌握二分查找、归并排序和快速查找。

如果面试题要求在二维数组（具体表现为迷宫或者棋盘等）删搜索路径，那么我们可以尝试回溯法。通常回溯法很适合用递归的代码实现。只有当面试官限定不能使用递归实现时，再考虑用栈来模拟递归的过程。

如果面试题是求某个问题的最优解，并且该问题可以分为多个子问题，那么我们可以尝试用动态规划。在用自上而下的递归思路去分析动态规划问题的时候，我们会发现子问题之间存在重叠的更小的子问题。为了避免不必要的重复计算，我们用自下而上的循环代码来实现，也就是把子问题的最优解先算出来并用数组保存下来，接下来基于子问题的解计算大问题的解。

如果我们告诉面试官动态规划的思路之后，面试官还在提醒说在分解子问题的时候是不是存在某个特殊的选择，如果采用这个特殊的选择将一定能得到最优解，那么通常面试官这样的提示意味着该面试题可能适用于贪婪算法。

位运算可以看成一类特殊的算法，它是把数字表示成二进制之后对0和1的操作。由于位运算的对象位二进制数字，所以不是很值观，但掌握不难，因为总共只有与、或、异或、左移和右移5种位运算。

## 递归和循环	

递归是在一个函数的内部调用这个函数自身。而循环则是通过设置计算的初始值及终止条件，在一个范围内重复运算。比如求1+2+...+n，对应代码如下：

```C++
int AddFrom1ToN_Recursive(int n){
    return n <= 0 ? 0 : n + AddFrom1ToN(n-1);
}
int AddFrom1ToN_Iterative(int n){
    int result = 0;
    for (int i = 1; i <= n; i++)
        result += i;
    return result;
}
```

递归的代码简洁，面试中如果没有特别的要求，则应聘者可以尽量采用递归的方法编程。

递归由于是函数的自身调用，而函数调用是有时间和空间的消耗的：每次函数调用，都需要在内存栈种分配空间以保存蚕食、返回地址和临时变量，而且往栈里压入数据和弹出数据都需要时间。这就不难理解递归实现的效率不如循环。

另外，__递归中有可能很多计算都是重复的__，从而对性能带来很大的负面影响 。递归的本质是把一个问题分解为两个或者多个小问题。如果多个小问题存在相互重叠的部分，就存在重复的计算。

通常应用动态规划解决问题时我们都是用递归的思路分析问题，但是由于递归分解的子问题存在大量的重复，因此我们总是用自下而上的循环来实现代码。

除了效率之外，递归还有可能引发更严重的问题：__调用栈溢出。__每一次函数调用在内存栈中分配空间，而每个进程栈的容量是有限的。当递归调用的层级太多时，就会超出栈的容量，从而导致调用栈溢出。

### 斐波那契数列

#### 题目描述

大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。

n<=39

#### 思路分析

- 循环实现
- 递归实现

#### 代码实现

```c++
//循环实现
class Solution {
public:
    int Fibonacci(int n) {
        if (n == 0 || n == 1)
            return n;
        int a = 0, b = 1;
        int temp = 0;
        for(int i = 2; i <= n; i++){
            temp = a + b;
            a = b;
            b = temp;
        }
        return b;
    }
};

//递归实现
class Solution {
public:
    int Fibonacci(int n) {
        if (n <= 0)
            return 0;
        if (n == 1)
            return 1;
        return Fibonacci(n-1) + Fibonacci(n-2);
    }
};
```

除了斐波那契数列的编程实现，不少面试题还可以看成斐波那契数列的应用。比如青蛙跳台阶的数量问题。

### 青蛙跳台阶问题

#### 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

#### 思路分析

- 递归实现
- 动态规划实现

#### 代码实现

```C++
//递归
class Solution {
public:
    int jumpFloor(int number) {
        if (number <= 0)
            return 0;
        if (number == 1 || number == 2)
            return number;
        return jumpFloor(number-1) + jumpFloor(number-2);
    }
};

//DP
class Solution {
public:
    int jumpFloor(int number) {
        if (number == 1 || number == 2)
            return number;
        vector<int> dp;
        dp.push_back(1);
        dp.push_back(2);
        for (int i = 2; i < number; i++)
            dp.push_back(dp[i - 1] + dp[i - 2]);
        return dp[number-1];
    }
};
```

扩展：如果一只青蛙可以跳1级台阶，也可以跳两级台阶，……它也可以跳上n级台阶。此时该青蛙跳上一个n级的台阶有多少种跳法？

我们用数学归纳法可以证明：f(n)=2^(n-1)。

### 变态跳台阶

#### 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

#### 代码实现

```C++
class Solution {
public:
    int jumpFloorII(int number) {
        //return pow(2, number-1);
        return 1<<(number-1); //右移操作速度更快
    }
};
```



### 矩形覆盖

#### 题目描述

我们可以用2×1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2×1的小矩形无重叠地覆盖一个2×n的大矩形，总共有多少种方法？

#### 思路分析

我们先把2×n的覆盖方法记为f(n)。用第一个车2×1的小矩形去覆盖大矩形的最左边时有两种选择：竖着放和横着放。当竖着放的时候，右边剩下2×(n-1)的区域，这种情形下的覆盖方法记为f(n-1)。接下来考虑横着放的情形，当2×1的小矩形放在左上角的时候，右下角必须和横着放一个2×1的小矩形，右边剩下2×(n-2)的区域，这种情形下的覆盖方法记为f(n-2)。因此f(n)=f(n-1)+f(n-2)。

#### 代码实现

```C++
class Solution {
public:
    int rectCover(int number) {
        if (number == 1 || number == 2)
            return number;
        vector<int> dp;
        dp.push_back(1);
        dp.push_back(2);
        for (int i = 2; i < number; i++)
            dp.push_back(dp[i-1] + dp[i-2]);
        return dp[number-1];
    }
};
```

## 查找和排序

查找相对而言简单，不外乎顺序查找、二分查找、哈希表查找和二叉排序树 查找。在面试的时候，不管是用递归和循环，面试官都期待应聘者信手拈来写出完整正确的二分查找。

__提示：__如果面试题中要求在排序的数组（或者部分排序的数组）中查找一个数字或者统计某个数字出现的次数，那么我们都可以尝试用二分查找算法。

哈希表和二叉排序树查找的重点在于考察对应的数据结构而不是算法。哈希表最主要的优点是我们利用它能够在O(1)时间内找到某一元素，是效率最高的查找方式；当其缺点就是需要额外的空间来实现哈希表。与二叉排序树查找算法对应的数据结构是二叉搜索树。

排序要比查找更复杂一下。需要掌握的比较插入排序、冒泡排序、归并排序、快速排序等不同算法的优劣。一定要对各种排序算法的特定烂熟于胸，能够从存储空间、平均时间复杂度和最差时间复杂度等方法去比较它们的优缺点。需要特别强调的是，很多公司的面试需要应聘者写出快速排序的代码。

实现快速排序算法的关键点在于先在数组中选择一个数字，接下来吧数组中的数字分为两部分，比选择的数字小的数字移动到数组的左边，比选择的数字大的数字移动到数组的右边。函数Partition实现如下。接下来可以用递归的思想分别对每次选中的数字的左右两边进行排序。QuickSort函数就是递归实现**快速排序**的代码：

```c++
#include "pch.h"
#include <iostream>
using namespace std;

int RandomInRange(int start, int end) {
	return rand() % (end - start) + start;;
}

int Partition(int data[], int length, int start, int end) {
	if (data == nullptr || length <= 0 || start < 0 || end >= length)
		throw new std::exception("Invalid Parameters");
	int index = RandomInRange(start, end);
	swap(data[index], data[end]);
	int small = start - 1;
	for (index = start; index < end; ++index) {
		if (data[index] < data[end]) {
			++small;
			if (small != index)
				swap(data[index], data[small]);
		}
	}
	++small;
	swap(data[small], data[end]);
	return small;    
}

void QuickSort(int data[], int length, int start, int end) {
	if (start == end)
		return;
	int index = Partition(data, length, start, end);
	if (index > start) 
		QuickSort(data, length, start, index - 1);	
	if (index < end)
		QuickSort(data,length, index + 1, end);
}


int main()
{
	const int length = 6;
	int data[length] = { 5,3,2,6,10,4 };
	QuickSort(data, length, 0, length-1);
	for (int i = 0; i < length; i++)
		cout << data[i] << " ";
	cout << endl; 
}
```

函数RandomInRange用来生成一个在start和end之间的随机数，函数swap是用来交换两个数字。

不同的排序算法适用的场合也不尽相同。快速排序虽然总体的平均效率最好的，但也不是任何实话都是最优的算法。比如数组本身已经排好序，而每一轮的时候都以最后一个数字作为比较的标准，此时快速排序的效率只有$O(n^2)$。

### 旋转数组的最小数字

#### 题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

#### 思路分析

二分查找法。按照题目中旋转的规则，第一个元素大于或者等于最后一个元素。接着找到数组中间的元素，如果该中间元素位于前面的递增子数组，那么它应该大于或者等于第一个指针指向的元素。此时数组中最小的元素应该位于该中间元素的后面。我们可以把第一个指针指向该中间元素，这样可以缩小范围。移动之后的第一个指针仍然位于前面的递增子数组。

同样，如果中间元素位于后面的递增子数组，那么它应该小于或者等于第二个指针指向的元素。此时该数组中最小的元素应该位于该中间元素的前面。我们可以把第二个指针指向该中间元素，这样也可以缩小寻找的范围。

#### 代码实现

```c++
class Solution {
public:
    int minNumberInRotateArray(vector<int> rotateArray) {
        if (rotateArray.size() <= 0)
            return 0;
        int start = 0, end = rotateArray.size()-1;
        while(rotateArray[start] >= rotateArray[end]){
            if (end - start == 1)
                return rotateArray[end];
            int mid = (start + end) / 2;
            if (rotateArray[mid] >= rotateArray[start])
                start = mid;
            if (rotateArray[mid] <= rotateArray[end])
                end = mid;
        }
        return rotateArray[0];//处理将前面的0个元素搬到后面的情形
    }
};
```

还有一种特殊情况需要讨论，比如数组为{0,1,1,1,1}，这样{1,0,1,1,1}和{1,1,1,0,1}都是数组的旋转数组。那么这两种情况下中间元素和最前面和最后面元素都相等，此时无法判断是将第一个指针还是最后一个指针移动的中间元素。此时就需要采用顺序查找来实现：

````c++
class Solution {
public:
    int minNumberInRotateArray(vector<int> rotateArray) {
        if (rotateArray.size() <= 0)
            return 0;
        int start = 0, end = rotateArray.size()-1;
        while(rotateArray[start] >= rotateArray[end]){
            if (end - start == 1)
                return rotateArray[end];
            int mid = (start + end) / 2;
            if (rotateArray[mid] == rotateArray[start] && rotateArray[mid] == rotateArray[end])
                return MinInOrder(rotateArray, start, end);
            if (rotateArray[mid] >= rotateArray[start])
                start = mid;
            if (rotateArray[mid] <= rotateArray[end])
                end = mid;
        }
        return rotateArray[0];
    }
    //顺序查找
    int MinInOrder(vector<int> rotateArray, int start, int end){
        int result = rotateArray[start];
        for (int i = start; i <= end; i++)
            if (result > rotateArray[i]){
                result = rotateArray[i];
            }
        return result;
    }
};
````

## 回溯法

回溯法可以看成蛮力方法的升级版，它从解决问题每一步的所有可能选项里系统地选择出一个可行的解决方案。回溯法非常适合由多个步骤组成的问题，并且每个步骤都有多个选项。当我们在某一步选择了其中一个选项时，就进入下一步，然后又面临新的选项。就这样重复选择，直到到达最终的状态。

用回溯法解决的问题的所有选项可以形象地用树状结构表示。

如果在叶节点的状态不满足约束条件，那么只好回溯到它的上一个节点再尝试其他的选项。如果上一个节点所有可能的选项都已经试过，并且不能满足约束条件的终结状态，则再次回溯到上一个节点。

### 矩阵中的路径

#### 题目描述

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。 例如 a b c e s f c s a d e e 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

#### 思路分析

​		第i个位置。如果路径上的第i个字符正好是ch，那么往相邻的格子寻找路径上的第i+1个字符。除在矩阵边界上的格子之外，其他格子都有4个相邻的格子。
重复这个过程直到路径上的所有字符都在矩阵中找到相应的位置。 
　　由于回朔法的递归特性，路径可以被开成一个栈。当在矩阵中定位了路径中前n个字符的位置之后，在与第n个字符对应的格子的周围都没有找到第n+1个字符，这个时候只要在路径上回到第n-1个字符，重新定位第n个字符。 
　　由于路径不能重复进入矩阵的格子，还需要定义和字符矩阵大小一样的布尔值矩阵，用来标识路径是否已经进入每个格子。 当矩阵中坐标为（row,col）的格子和路径字符串中相应的字符一样时，从4个相邻的格子(row,col-1),(row-1,col),(row,col+1)以及(row+1,col)中去定位路径字符串中下一个字符如果4个相邻的格子都没有匹配字符串中下一个的字符，表明当前路径字符串中字符在矩阵中的定位不正确，我们需要回到前一个，然后重新定位。 
　　一直重复这个过程，直到路径字符串上所有字符都在矩阵中找到合适的位置

#### 代码实现

```C++
class Solution {
public:
	bool hasPath(char* matrix, int rows, int cols, char* str)
	{
		if (matrix == nullptr || rows < 1 || cols < 1 || str == nullptr)
			return false;
		bool * isOk = new bool[rows*cols]();
		for (int i = 0; i < rows; i++) {
			for (int j = 0; j < cols; j++) {
				if (isHasPath(matrix, rows, cols, str, isOk, i, j)) {
					return true;
				}
			}
		}
		return false;
	}

	bool isHasPath(char * matrix, int rows, int cols, char * str, bool * isOk, int curx, int cury)
	{
		if (*str == '\0')
			return true;
		if (curx < 0 || curx >= rows || cury < 0 || cury >= cols)
			return false;
		if (isOk[curx * cols + cury] || *str != matrix[curx * cols + cury]) {
			return false;
		}
		isOk[curx * cols + cury] = true;
		bool sign = isHasPath(matrix, rows, cols, str + 1, isOk, curx - 1, cury)
			|| isHasPath(matrix, rows, cols, str + 1, isOk, curx + 1, cury)
			|| isHasPath(matrix, rows, cols, str + 1, isOk, curx, cury - 1)
			|| isHasPath(matrix, rows, cols, str + 1, isOk, curx, cury + 1);
		isOk[curx*cols + cury] = false;
		return sign;
	}
};
```

### 机器人的运动范围

#### 题目描述

地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

#### 思路分析

类似矩阵中的路径问题。

#### 代码实现

```C++
class Solution {
public:
    int movingCount(int threshold, int rows, int cols)
    {
        if (threshold < 0 || rows <= 0 || cols <= 0)
            return 0;
        bool * isOk = new bool[rows * cols]();
        int count = 0;
        movingCount(threshold, rows, cols, 0, 0, count, isOk);
        return count;
    }
    bool movingCount(int threshold, int rows, int cols, int i, int j, int & count, bool * isOk){
        int sum = 0;
        sum += sumij(i);
        sum += sumij(j);
        if (i >= rows || j >= cols)
            return false;
        if(sum > threshold || isOk[i * cols + j])
            return false;
        count += 1;
        isOk[i*cols + j] = true;
        bool sign = movingCount(threshold, rows, cols, i+1, j, count, isOk) || 
            movingCount(threshold, rows, cols, i, j+1, count, isOk); 
        return sign;
    }
    int sumij(int i){
        int sum = 0;
        while (i){
            sum += i % 10;
            i = i/10;
        }
        return sum;
    }
};
```

## 动态规划与贪婪算法

动态规划现在是编程面试题中的热门问题。如果面试题是求一个问题的最优解（通常是求最大值或最小值），而且该问题能够分解成若干子问题，并且子问题之间还有重叠的更小的子问题，就可以考虑用动态规划来解决这个问题。

在引用动态规划之前，要分析能否把大问题分解成小问题，分解后的每个小问题也存在最优解。

### 剪绳子

#### 题目描述

给你一根长度为n绳子，请把绳子剪成m段（m、n都是整数，n>1并且m≥1）。每段的绳子的长度记为k[0]，k[1]，…，k[m]。k[0]×k[1]×……×k[m]可能的最大乘积是多少？例如当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到最大的乘积18。

#### 思路分析

对于长度为n的绳子，设f(n)为最优剪法。则f(n)=max(f(i)×f(n-i))。

#### 代码实现

```C++
//求剪完绳子之后的最大乘积
int MaxProductAfterCutting_Solution(int length)
{
	//定义当绳长小于2时，最大的乘积
	if (length < 2)
		return 0;
	//当绳长等于2时，最大的乘积
	if (length == 2)
		return 1;
	//当绳长等于3时，最大的乘积
	if (length == 3)
		return 2;

	/*********下面考虑的是，剩下绳长的最大乘积***********/
	//当剩下的绳长分别是0/1/2的时候，最大乘积就是本身的长度，不需要再去剪,因为一刀没有不剪的乘积大
	int* product = new int[length + 1];
	product[0] = 0;
	product[1] = 1;
	product[2] = 2;
	product[3] = 3;

	//绳长的最大乘积
	int Max_Value = 0;

	/**********下面考虑的是当绳长大于等于4的时候，绳子的最大乘积***********/
	for (int i = 4; i <= length; ++i)
	{
		//每次将最大乘积清空
		Max_Value = 0;

		//计算绳长为i时的最大乘积
		//因为要计算f(i)乘以f(i-j)的最大值，j超过一般是就重复
		for (int j = 1; j <= i / 2; ++j)
		{
			int products = product[j] * product[i - j];

			//如果最大乘积比当前的最大值还要大
			if (Max_Value < products)
				//将绳长为i的最大乘积记录下来
				Max_Value = products;
		}
		//更新记录表中的最大乘积
		product[i] = Max_Value;
	}

	//将绳长的最大的乘积记录下来
	Max_Value = product[length];

	//删除辅助空间
	delete[] product;

	//返回绳长最大的乘积
	return Max_Value;
}

```

另一种动态规划的方式：

```C++
long long maxProductAfterCutting(int length) {
	if (length <= 1)
		return 0;
	if (length <= 3)
		return length - 1;
	vector<long long> dp;
	dp.push_back(1);
	dp.push_back(2);
	dp.push_back(3);
	for (int i = 3; i < length; i++) {
		dp.push_back(max(max(2 * dp[i - 2], dp[i - 1]), dp[i-3] * 3));
	}
	return dp[length - 1];
}
```

### 贪婪算法

#### 题目描述

给你一根长度为n绳子，请把绳子剪成m段（m、n都是整数，n>1并且m≥1）。每段的绳子的长度记为k[0]，k[1]，…，k[m]。k[0]×k[1]×……×k[m]可能的最大乘积是多少？例如当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到最大的乘积18。

#### 思路分析

如果我们按照如下的策略来剪绳子，则得到的各段绳子的长度的乘积将最大：当n≥5时，我们尽可能多地剪长度为3的绳子，当剩下的绳子长度为4时，把绳子剪成两段长度为2的绳子。

#### 代码实现

```C++
int maxProductAfterCutting(int length) {
	if (length <= 1)
		return 0;
	if (length <= 3)
		return length - 1;
	int timesOf3 = length / 3; //长度为3的绳子段数
	if (length - timesOf3 * 3 == 1) //剩余的绳子长度为1，此时最优的剪法是2*2，因为2*2 > 3 *1
		timesOf3--;
	int timesOf2 = (length - timesOf3 * 3) / 2;
	return (int)(pow(3, timesOf3) * (int)pow(2, timesOf2));
}
```

## 位运算

位运算把数字用二进制表示之后，对每一位上0或者1的运算。

在微软产品excel中，用A表示第1列，B表示第2列，……，AA表示第27列，AB表示第28列……以此类推。请写出一个函数输入字母表示的列号编码，输出它是第几列。

```C++
int excelCloum(char * str) {
	int length = strlen(str);
	int sum = 0;
	while(*str) {
		sum += (*str - 'A' + 1) * pow(26, length-1);
		length--;
		str++;
	}
	return sum;
}
```

---

|  与(&)  | 0 & 0 = 0  | 1 & 0 = 0  | 0 & 1 =  0 | 1 & 1 = 1 |
| :-----: | :--------: | :--------: | :--------: | :-------: |
| 或(\|)  | 0 \| 0 = 0 | 1 \| 0 = 1 | 0 \| 1 = 1 | 1 \| 1 =1 |
| 异或(^) | 0 ^ 0 = 0  | 1 ^ 0  =1  | 0 ^ 1 = 1  | 1 ^ 1 = 0 |

左移运算符m<<n表示把m左移n位。在左移n位的适合，最左边的n位丢弃，同时在最右边补上0。

右移运算符类似。

 **负数的补码是在原码的基础上除符号位外其余位取反后+1**

正数在计算机中源码表示。而负数在计算机中用补码的形式表示。

```C++
-5的原码： 1000 0101
    反码： 1111 1010
    补码： 1111 1011
```



### 二进制中1的个数

#### 题目描述

输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

#### 思路分析

第一种方法，是每次把整数n右移一位，每次判断最低位是否为1。这种方法对于负数会出现死循环，因为符数的最高位必须保证是1，所以每次右移一位后，还要将最高位设置为1来保证是一个负数，这样数字的所有位全部变成了1。

第二种方法，不是移动整数n，而是将1每次左移一位，然后跟n与，这样来判断n的每一位是否为1。该方法，对于一个32位的整数需要循环32次。

第三种方法，利用把一个整数减去1，再和原整数做与运算，会把该整数最右边的1变成0。那么一个整数的二进制表示中有多少个1，就可以进行多少次这样的操作。这样就在第二种方法的基础上提高了效率。

#### 代码实现

```C++
//可能陷入死循环
/*
    * 该解法如果输入时负数会陷入死循环,因为负数右移时，在最高位补得是1
    * 而最终目的是求1的个数，那么会有无数个1了。
*/
class Solution {
public:
     int  NumberOf1(int n) {
        int count = 0;
        while (n){
             if (n & 1)
                 count++;
             n = n >> 1;
         }
         return count;
     }
};


//正确解法
class Solution {
public:
     int  NumberOf1(int n) {
        int count = 0;
        unsigned int flag = 1;
        while (flag){
             if (n & flag)
                 count++;
             flag = flag << 1;
         }
         return count;
     }
};

//最优解法
class Solution {
public:
     int  NumberOf1(int n) {
        int count = 0;
        while(n){
            ++count;
            n = n & (n-1);
        }
        return count;
     }
};
```

