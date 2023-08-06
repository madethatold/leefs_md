# sort

## 常见拍序总结

915这道题目中将常见排序都总结了一遍 ：
**快排+归并排序+堆排序+选择排序+插入排序**

## 指定Comparator的排序

[56. 合并区间](https://leetcode.cn/problems/merge-intervals/)

# Hash

## offer060

很常见的HashMap计数，比较好的点在于这里的sort

## 398

常见的HashMap方式，存储key与index序列的关系

## 以哈希的思路解题

41缺失的第一个正数

这里限制了空间复杂度 因此原本的hashset用不了，就用当前的数组作为哈希表







# LinkedList

## 翻转链表

25hard&206easy都是翻转链表的题目

这里需要注意一个一直以来的忽略点，尾插法翻转链表时，head_pre结点先断链。

92翻转中间某一部分的链表，思路没什么区别。

## 快慢指针

143重排链表：快慢指针+翻转链表+归并两个链表。这种题目最好画图或者脑子里想一下断链和拼接的先后顺序

## 合并链表

21 合并两个有序链表easy

23 合并K个有序链表hard：

这里的思路是，对于每一个list我与其每次找一下k个里面的最小，不如直接就用优先队列，减少了每次找的过程。那么就是每一次从优先队列里，取出最小节点，尾插到我们的结果中。这个节点后续还有的话，把next放回队列。



# DP

## 42接雨水

这个hard且经典，需要多多回看

## 300递增子序列问题





# search

## 有序数组二分查找

33题非常有意思，难点在于找到二分查找旋转点

## x的平方根竟然用到了二分查找

69x的平方根

这里的注意在于注意溢出。另一方面，这里是不一定能找到的，那么res怎么一步步更新到最接近答案的整数，这里很巧妙。



# Array

## 双指针

### 下一个排列31

推导过程：

- 下一个数 比当前数大，这样才满足 “下一个排列” 的定义。因此只需要 将后面的「大数」与前面的「小数」交换，就能得到一个更大的数。

- 下一个数的增幅尽可能小，这才是真正的下一个排列，因此：

  将尽可能小的大数和前面的小数进行交换

举例子：

**123465**:

首先交换4和5进行交换->123564

然后将5之后的数重置为升序->123546

实现过程：

从后向前查找满足 `A[i] < A[j]`的位置，**[j,end]是降序的**，这个**A[i]就是我们要交换的小数**

从后向前查找**第一个**满足`A[k]>A[i]`的位置，**A[k]就是我们要交换的大数**

交换`A[i]-A[k]`

对i之后的元素排序，使之升序

```java
		public void nextPermutation(int[] nums) {
            int i = nums.length - 1, j = nums.length - 1;
            //从后向前找第一次出现邻近升序的对儿 A[j-1] < A[j]
            while (j > 0 && nums[j] <= nums[j - 1]) {
                j--;
            }
            //本身就是最后一个排列（全部降序）， 把整体整个翻转变升序进行返回
            if (j == 0) {
                reverse(nums,0,nums.length-1);
                return;
            }
            j--;//调整j到要被交换的小数位置
            //从后向前找第一个令A[j] < A[i]的 i值,也就是大数的位置
            while (nums[i] <= nums[j]) {
                i--;
            }
            swap(nums, i, j);
            //nums[j+1,end]是降序 改为升序
            reverse(nums, j + 1, nums.length - 1);
        }

        public void reverse(int[] nums, int l, int r) {
            //双指针升序
            while (l < r) {
                swap(nums, l, r);
                l++;
                r--;
            }
        }

        public void swap(int[] nums, int i, int k) {
            int tmp = nums[i];
            nums[i] = nums[k];
            nums[k] = tmp;
        }
```



# Tree

## 层序遍历

199二叉树的右视图，本质上就是层序遍历，记录每一层的最右边节点值

429N叉树的层序遍历

102二叉树的层序遍历

107二叉树的层序遍历，这里就直接reverse就好

103锯齿形层序遍历，记录层数，遇到双层reverse一下

637层序遍历过程中计算每一层的平均值

## 从先/中/后序遍历序列中构造二叉树

105

106

就是递归调用，搞清楚边界条件。

## 深搜/递归相关

124二叉树中的最大路径和`hard`

主要是搞清楚每一个节点的操作，递归。

101对称二叉树

# matrix

## 模拟

54螺旋矩阵

这个模拟螺旋的过程重要依赖于定义了 left,right,up,down四个变量记录边界

# 优先队列（堆）

## 合并K个链表

23 合并K个有序链表hard：

# 图相关的还没有开始看比如200