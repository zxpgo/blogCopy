---
title: Python实现：8种排序算法
date: 2018-08-22 22:28:50
tags: 
		- Python
		- 算法
categories: "算法"
---
介绍8中排序算法，包括算法的基本思路和算法的python实现。<!--more-->

- 选择排序-简单选择排序
- 选择排序-堆排序
- 插入排序-直接插入排序
- 插入排序-希尔排序
- 交换排序-快速排序
- 交换排序-冒泡排序
- 归并排序
- 桶排序/基数排序

经典文章：[好文](https://mp.weixin.qq.com/s?__biz=MzA5ODUxOTA5Mg==&mid=2652562087&idx=1&sn=81e7ad2394d6a1d0af4087a0dcfb062d&chksm=8b7e13fcbc099aeae18d2f6ee4d9d60ff93a4db614471abb05871c96609199022de54dd9619d&mpshare=1&scene=1&srcid=09273MllkF1rreSLV97ARxnF#rd)

# 选择排序

## 简单选择排序

每一趟从待排序的数据元素中选出最小（最大）的元素，顺序放在待排序的数列最前，直到全部待排序的数据元素全部排完。

例子：</br>
[4, 2, 3] 找出最小的：2,与第一个元素交换</br>
[2, 4, 3] 找出最小的：3，与第二个元素交换</br>
[2, 3, 4] </br>

百度百科： [简单选择排序](https://baike.baidu.com/item/%E7%AE%80%E5%8D%95%E9%80%89%E6%8B%A9%E6%8E%92%E5%BA%8F)

**算法实现**

	def j_sort(a):
	      l = len(a)
	      temp = 0
	      
	      for j in range(0,l-1):
	            count = j  #记录最小元素下标
	            #每次找出最小元素
	            for i in range(j,l-1):
	                  if a[count] > a[i+1]:
	                        count = i+1
	            #交换最小元素和待排序元素中最前一个
	            '''if count != j:
	                  temp = a[j]
	                  a[j] = a[count]
	                  a[count] = temp '''
	            a[j], a[count] = a[count], a[j] #实现跟上述代码一样
	      for i in range(0,l):
	            print(a[i])
	
	if __name__ == "__main__":
	      a = [10, 2, 5, 1, 3, 7, 3]
	      j_sort(a)

## 堆排序

堆排序的基本思想是：将待排序序列构造成一个大顶堆，此时，整个序列的最大值就是堆顶的根节点。将其与末尾元素进行交换，此时末尾就为最大值。然后将剩余n-1个元素重新构造成一个堆，这样会得到n个元素的次小值。如此反复执行，便能得到一个有序序列了

堆排序是利用堆这种数据结构而设计的一种排序算法，堆排序是一种选择排序，它的最坏，最好，平均时间复杂度均为O(nlogn)，它也是不稳定排序。首先简单了解下堆结构。

**堆**</br>
堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆；或者每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。如下图：

![](https://i.imgur.com/JpLbFCa.png)

转自：[https://www.cnblogs.com/chengxiao/p/6129630.html](https://www.cnblogs.com/chengxiao/p/6129630.html)

百度百科： [堆排序](https://baike.baidu.com/item/%E5%A0%86%E6%8E%92%E5%BA%8F)

**算法实现**

	def d_sort(arr):
	      l = len(arr)
	      for i in range(int(l/2-1), -1, -1):
	            adjustHead(arr,i,l)
	      # 交换堆顶和最后一个元素，并调整堆结构
	      for j in range(l-1, 0, -1):
	            arr[0], arr[j] = arr[j], arr[0] #将堆顶元素和末尾元素进行交换
	            adjustHead(arr, 0, j) #重新对对进行调整
	      for k in range(0,l):
	            print(arr[k])
	
	#构造大顶堆            
	def adjustHead(a, i, l):
	      temp = a[i] #取出当前元素
	      k = 2*i + 1 #从左子节点开始，即2*i+1
	      while k < l:
	            if k+1 < l & a[k] < a[k+1]: #若果左子节点小于右子节点，k指向右子节点
	                  k=k+1
	            if a[k] > temp: #如果子节点大于父节点，将子节点赋值给父节点，并将子节点下标记录下来，后面将父节点值赋值给该子节点
	                  a[i] = a[k]
	                  i = k
	            else:
	                  break
	            k = 2*k + 1 #把该节点当作父节点，继续操作
	      a[i] = temp #将父节点值赋值给该子节点
	                  
	if __name__ == "__main__":
	      a = [10,2,4,5]
	      d_sort(a)

# 插入排序
插入排序思想：每一趟将一个待排序元素，按其排序码大小插入到前面已经排好序的一组元素的适当位置上，直到所有待排序元素元素全部插入为止

## 直接插入排序

直接插入排序是假定前i个构成的子序列是处于已排序的情况下进行排序的，然后将第i个元素与前i个构成的子序列逆序进行比较，如果是要升序排序，则比较第i个元素是否比j=i-1(i-1需要>=0)的元素大，如果是则第i个元素的位置（即j+1的位置上）保持不动，反之则将j=i-1的元素放置到i的位置，再进行第i个元素与j=i-2(i-2需要>=0)的,依次进行，如果第i个元素刚好比j=i-3大，则将第i个元素插入到j=i-2（即j+1的位置）上！

百度百科： [直接插入排序](https://baike.baidu.com/item/%E7%9B%B4%E6%8E%A5%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F)

**算法实现**

	def zc_sort(a):
	      l = len(a)
	      j=0
	      for i in range(1,l):
	            temp = a[i]
	            for j in range(i-1, -1, -1):
	                  if  temp < a[j]:  #如果第i个元素大于前i个元素中的第j个
	                        a[j+1] = a[j] #则第j个元素先后移1位
	                  else:   #如果第i个元素小于等于前i个元素中的第j个则结束循环
	                        break
	            a[j+1] = temp #将i个元素赋值给空着的位置
	      for i in range(0,l):
	            print(a[i])
	
	if __name__ == "__main__":
	      a = [1, 10, 5, 2, 6, 7, 2]
	      zc_sort(a)

## 希尔排序

转自：[https://www.cnblogs.com/chengxiao/p/6104371.html](https://www.cnblogs.com/chengxiao/p/6104371.html)

百度百科： [希尔排序](https://baike.baidu.com/item/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F)

**算法实现**

	def shell_sort(a):
	     l = int(len(a)/2)
	     #生成增量列表
	     gap_list = []
	     while l > 0:
	          gap_list.append(l)
	          l = int(l/2)
	     print(gap_list)
	     for gap in gap_list:#增量gap，并逐步缩小增量 
	          #print(gap)
	          for i in range(gap, len(a)):#从第gap个元素，逐个对其所在组进行直接插入排序操作
	               j = i
	               while j -gap >= 0 and a[j-gap] > a[j]:
	                    swap(a, j, j-gap)  #交换两个元素
	                    j = j - gap
	                              
	     for i in range(0,len(a)):
	          print(a[i])
	
	def swap(arr, a, b):
	     arr[a] = arr[a] + arr[b]
	     arr[b] = arr[a] - arr[b]
	     arr[a] = arr[a] - arr[b]
	
	
	if __name__== "__main__":
	      a = [1,0,4,-1,2,7,9,8,10, 3,6,5,18]
	      shell_sort(a)


# 交换排序

## 快速排序
任意选取一个数据（通常选用数组的第一个数）作为关键数据，然后将所有比它小的数都放到它前面，所有比它大的数都放到它后面，这个过程称为一趟快速排序。</br>
一趟快速排序的算法是：</br>
1）设置两个变量i、j，排序开始的时候：i=0，j=N-1；</br>
2）以第一个数组元素作为关键数据，赋值给key，即key=A[0];</br>
3）从j开始向前搜索，即由后开始向前搜索(j--)，找到第一个小于key的值A[j]，将A[j]和A[i]互换；</br>
4）从i开始向后搜索，即由前开始向后搜索(i++)，找到第一个大于key的A[i]，将A[i]和A[j]互换；</br>
5）重复第3、4步，直到i=j； (3,4步中，没找到符合条件的值，即3中A[j]不小于key,4中A[i]不大于key的时候改变j、i的值，使得j=j-1，i=i+1，直至找到为止。找到符合条件的值，进行交换的时候i， j指针位置不变。另外，i==j这一过程一定正好是i+或j-完成的时候，此时令循环结束）。

**注意：快速排序不是一种稳定的排序算法，也就是说，多个相同的值的相对位置也许会在算法结束时产生变动。**

百度百科： [快速排序](https://baike.baidu.com/item/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95?fromtitle=%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F&fromid=2084344)

**算法实现**
递归

	def quick_sort(arr, low, high):
	     #temp = a[0]
	     i = low
	     j = high
	     if i >= j:
	          return arr
	     temp = arr[i]
	     while i < j:
	          while i < j and arr[j] >= temp:
	               j = j-1
	          arr[i] = arr[j]
	          while i < j and arr[i] <= temp:
	               i = i + 1
	          arr[j] = arr[i]
	     arr[i] = temp
	     quick_sort(arr, low, i-1)
	     quick_sort(arr, j+1, high)
	     return arr
	
	
	if __name__ == "__main__":
	     a = [10, 3, -3, 6, 0, 1, 4, 5, 11, 8]
	     quick_sort(a, 0, len(a)-1)
	     print(a)


## 冒泡排序

冒泡排序算法的原理如下：

- 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
- 对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
- 针对所有的元素重复以上的步骤，除了最后一个。
- 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较

百度百科： [冒泡排序](https://baike.baidu.com/item/%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F)


	def bubble_sort(arr):
	     length = len(arr)
	     while length > 0:   
	          for i in range(length-1):
	               if arr[i] > a[i+1]:
	                    arr[i] = arr[i] + arr[i+1]
	                    arr[i+1] = arr[i] - arr[i+1]
	                    arr[i] = arr[i] - arr[i+1]
	          length -= 1
	
	if __name__ == "__main__":
	     a = [10, 3, -3, 6, 0, 1, 4, 5, 11, 8]
	     bubble_sort(a)
	     print(a)


# 归并排序（递归）

本文中的**快速排序**就是一种递归排序
</br>先递归，在排序
首先考虑下如何将将二个有序数列合并。这个非常简单，只要从比较二个数列的第一个数，谁小就先取谁，取了后就在对应数列中删除这个数。然后再进行比较，如果有数列为空，那直接将另一个数列的数据依次取出即可。

解决了上面的合并有序数列问题，再来看归并排序，其的基本思路就是将数组分成二组A，B，如果这二组组内的数据都是有序的，那么就可以很方便的将这二组数据进行排序。如何让这二组组内数据有序了？

可以将A，B组各自再分成二组。依次类推，当分出来的小组只有一个数据时，可以认为这个小组组内已经达到了有序，然后再合并相邻的二个小组就可以了。这样通过先递归的分解数列，再合并数列就完成了归并排序。

转自：[https://www.cnblogs.com/lulushow/p/6820727.html](https://www.cnblogs.com/lulushow/p/6820727.html)

百度百科： [归并排序](https://baike.baidu.com/item/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F/1639015)

**算法实现**

	def Merge_Sort(lists):
	    if len(lists) <= 1:
	        return lists
	    num = int(len(lists)/2)
	    left = Merge_Sort(lists[:num]) #将列表从中间分为两部分
	    right = Merge_Sort(lists[num:])
	    return Merge(left, right) #合并两个列表
	
	def Merge(left,right):
	    r, l=0, 0
	    result=[]
	    while l<len(left) and r<len(right):
	        if left[l] < right[r]:
	            result.append(left[l])
	            l += 1
	        else:
	            result.append(right[r])
	            r += 1
	    result += left[l:]
	    result += right[r:]
	    return result
	
	if __name__ == "__main__":
	     a = [10, 7, 4, 2, 8, 5, 1, 3]
	     a = Merge_Sort(a)
	     print(a)



# 基数排序

基数排序又称为“桶子法”，从低位开始将待排序的数按照这一位的值放到相应的编号为0~9的桶中。等到低位排完得到一个子序列，再将这个序列按照次低位的大小进入相应的桶中，一直排到最高位为止，数组排序完成。

![](https://i.imgur.com/XVIjAFa.jpg)



转：[https://blog.csdn.net/double_happiness/article/details/72452243](https://blog.csdn.net/double_happiness/article/details/72452243)

百度百科： [基数排序](https://baike.baidu.com/item/%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F/7875498?fr=aladdin)

**算法实现**

	import math
	
	def radix_sort(arr):
	     radix = 10 #基数
	     k = int(math.ceil(math.log(max(arr),radix)))#k可以表示任意整数
	     #math.log对arr中最大的数取对数，log(max(arr),10),并对其取整得到最大值的位数
	     bucket =[[] for i in range(radix)] 
	     for i in range(1, k+1): 
	          for  value in arr:
	               bucket[int(value%(radix**i)/(radix**(i-1)))].append(value) #析取整数第k位数字（从低到高）10**2位10的二次方
	          del arr[:]
	          for each in bucket:
	               arr.extend(each) #桶合并
	          bucket = [[]for i in range(radix)]
	
	if __name__ == "__main__":
	     a = [10, 2, 13, 44, 22, 33, 100, 612, 333, 262]
	     radix_sort(a)
	     print(a)
