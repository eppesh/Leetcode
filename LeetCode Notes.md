[toc]

# 1. Hot 100

## 1.1 Two Sum

Tags: 哈希表; 

来源: [Leetcode-Two Sum](https://leetcode-cn.com/problems/two-sum);

### 1.1.1 题目描述

Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`.
You may assume that each input would have `exactly one solution`, and you may not use the same element twice.
You can return the answer in `any order`. 

Example 1:

> Input: nums = [2,7,11,15], target = 9
> Output: [0,1]
> Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].

Example 2:

> Input: nums = [3,2,4], target = 6
> Output: [1,2]

Example 3:

> Input: nums = [3,3], target = 6
> Output: [0,1]

Constraints:

> 2 <= nums.length <= 104
> -109 <= nums[i] <= 109
> -109 <= target <= 109
> Only one valid answer exists.

Follow-up: Can you come up with an algorithm that is less than O(n2) time complexity?

### 1.1.2 解法

解法一：

暴力方法：两层循环，一个从前往后，一个由后到前，进行遍历；直到两数和为`target`。  
时间复杂度：O(n^2);  
空间复杂度：O(1);   

```c++
vector<int> twoSum(vector<int> &nums, int target) 
{
    for (int i = 0; i < nums.size(); ++i)
    {
        for (int j = nums.size() - 1; j > i; --j)	// j>i而不是j>0,可减少循环次数
        {
            if (nums[i] + nums[j] == target)
            {
                return { i,j };
            }
        }
    }
    return {};
}
```
解法二：
哈希表思想：利用`map`，`key`为`nums`中的值，`value`为该值在`nums`中的下标；如果`map`中`target-X`已经存在，则返回。为了减少循环的次数，可以循环时分别从前后两端同时开始遍历并查找。  
时间复杂度：共有`n`个元素时，对每一个元素`x`，都可以在O(1)情况下寻找到`target-x`，故：O(n);   
空间复杂度：O(n); 
```c++
vector<int> twoSum(vector<int> &nums, int target)
{
    map<int, int> hash;
    // reduce the number of cycles
    int left = 0;
    int right = nums.size() - 1;
    while (left <= right)
    {
        if (hash.find(target - nums[left]) != hash.end())
        {
            return { hash[target - nums[left]], left };
        }
        hash[nums[left]] = left;

        if (hash.find(target - nums[right]) != hash.end())
        {
            return { hash[target - nums[right]], right };
        }
        hash[nums[right]] = right;
        left++;
        right--;
    }
    return {};
}
```
### 1.1.3 知识点

- 返回时使用`{xx,xx}`或`{}`; 

  是C++11引进的用法，相当于用花括号`{12,3}`直接给`vector<int>`赋值；

- 该题中`map`比`unordered_map`效果要好，而且不需要无序；

- `map`的`find()!=end()`比`count()>0`效果要好一些；

- 尽可能地减少循环次数能提升效率；
---
## 1.2 Add Two Numbers

Tags: 单链表（singly-linked list）

来源：[Leetcode-Add Two Numbers](https://leetcode-cn.com/problems/add-two-numbers/);

### 1.2.1 题目描述

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains **a single digit**. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example 1:

![example 1](https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg)

```
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

Example 2:

```
Input: l1 = [0], l2 = [0]
Output: [0]
```

Example 3:

```
Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
```


Constraints:

- The number of nodes in each linked list is in the range [1, 100].
- 0 <= Node.val <= 9
- It is guaranteed that the list represents a number that does not have leading zeros.

### 1.2.2 解法
思路：（解法思路跟官方一致）  
由于输入的两个链表都是逆序存储数字的位数的，因此两个链表中同一位置的数字可以直接相加。  
我们同时遍历两个链表，逐位计算它们的和，并与当前位置的进位值相加。具体而言，如果当前两个链表处相应位置的数字为 `n1,n2`，进位值为`carry`，则它们的和为 `n1+n2+carry`；其中，答案链表处相应位置的数字为 `(n1+n2+carry) mod 10`，而新的进位值为`⌊(n1+n2+carry)/10⌋`。  
如果两个链表的长度不同，则可以认为长度短的链表的后面有若干个 0 。  
此外，如果链表遍历结束后，有 `carry>0`，还需要在答案链表的后面附加一个节点，节点的值为`carry`。  

时间复杂度：O(max(m,n));  
空间复杂度：O(1);  
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2)
    {
        ListNode *head = nullptr;
        ListNode *tail = nullptr;
        int carry = 0;  // 进位
        while (l1 != nullptr || l2 != nullptr)
        {
            int first_num = (l1 != nullptr) ? l1->val : 0;
            int second_num = (l2 != nullptr) ? l2->val : 0;
            int sum = first_num + second_num + carry;

            ListNode *temp = new ListNode(sum % 10);
            if (head == nullptr)
            {
                head = temp;
                tail = head;
            }
            else
            {
                tail->next = temp;
                tail = temp;        // or tail = tail->next;
            }
            if (l1 != nullptr)
            {
                l1 = l1->next;
            }
            if (l2 != nullptr)
            {
                l2 = l2->next;
            }
            carry = sum / 10;
        }
        if (carry > 0)
        {
            ListNode *temp = new ListNode(1);
            tail->next = temp;
        }
        return head;
    }
};
```
### 1.2.3 知识点
- 链表的`next`指针指向及相应赋值；
  > tail->next = temp;  
  > tail = temp;        // or tail = tail->next;
## 1.3 Longest Substring

Tags: 滑动窗口; 最长子串;

来源：[Leetcode-Longest Substring Without Repeating Characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters); 

### 1.3.1 题目描述

Given a string `s`, find the length of the longest substring without repeating characters. 

Example 1:

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

Example 2:

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

Example 3:

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```


Constraints:

- 0 <= s.length <= 5 * 104
- s consists of English letters, digits, symbols and spaces.

### 1.3.2 解法

思路：利用**滑动窗口**。滑动窗口，就是一个先进先出的队列。如例题中的`pwwkew`，刚开始该队列（窗口）为`p`（可理解为由右向左的方向入队列），不满足要求（不是最长）；再进一个元素，队列变为`pw`; 当下一个元素进来时，变为`pww`，不满足要求（有重复），此时需要移动该队列。

**如何移动？只需要把队列头部（左边）的元素移出即可**，直到满足题目要求。

一直维持这样的队列：移动后，队列变为`ww`，依旧有重复，继续移动，队列变为`w`，不再有重复元素；继续将下一个元素放入队列，队列变为`wk`，不是最长的；继续放进下一个元素，队列变为`wke`，已是最长的；当继续放进下一个元素时，队列变为`wkew`，有重复元素，开始移动队列，变为`kew`；所有元素已遍历完，求出解。  

时间复杂度：O(n);  
空间复杂度：O(m); (m是字符串中出现的不重复字符的个数)
```c++
int lengthOfLongestSubstring(string s) 
{
    if (s.empty())
    {
        return 0;
    }
    unordered_set<char> tool;
    int length = 0;
    int right = 0;
    for (int left = 0; left < s.size(); ++left)
    {
        if (left != 0)
        {
            tool.erase(s[left - 1]);
        }
        while (right < s.size() && tool.find(s[right]) == tool.end())
        {
            tool.insert(s[right]);
            ++right;
        }
        length = max(length, right - left);
    }
    return length;
}
```

### 1.3.3 知识点

- `unordered_set` 和 `unordered_multiset` 都位于`#include <unordered_set>`头文件中；
- `set`默认是有序列且unique的；若想无序，选用`unordered_set`；若想不唯一，选`multiset`；
- `set.erase()`用法注意：
  - 可以用`set.erase(iterator)`或`set.erase(myset[0])`来删除具体某个值；
  - 用`set.erase(first,last)`时，注意删除的范围是**[first,last)**，后面是**开区间**；

- **滑动窗口**：即一个（从右向左进入的）队列；满足相应条件，则进入队列；否则从队列头部移出元素，直到满足条件。

## 1.4 Median of Two Sorted Arrays

Tags: 归并; 

来源：[Leetcode-Median of two sorted arrays](https://leetcode-cn.com/problems/median-of-two-sorted-arrays); 

### 1.4.1 题目描述

Given **two sorted arrays** `nums1` and `nums2` of size `m` and `n` respectively, return the **median** of the two sorted arrays.

The overall run time complexity should be **O(log (m+n))**.

 Example 1:

```c++
Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explanation: merged array = [1,2,3] and median is 2.
```


Example 2:

```c++
Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explanation: merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.
```


Constraints:

- nums1.length == m
- nums2.length == n
- 0 <= m <= 1000
- 0 <= n <= 1000
- 1 <= m + n <= 2000
- -106 <= nums1[i], nums2[i] <= 106

### 1.4.2 解法

思路一：归并排序法；详见下面的**归并排序**知识点。

时间复杂度：遍历全部数组，O(m+n);

空间复杂度：新加了一个临时数组以保存合并后的两数组，O(m+n);

```c++
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2) 
{
    // 方法一：归并排序
    int m = nums1.size();
    int n = nums2.size();
    vector<int> tool(m + n, 0);	// 注意:这里一定要提前为vector分配 m+n 的空间
    int index = 0;
    int left = 0;
    int right = 0;
    while (left < m && right < n)
    {
        if (nums1[left] < nums2[right])
        {
            tool[index++] = nums1[left++];
        }
        else
        {
            tool[index++] = nums2[right++];
        }
    }
    // 分别遍历剩余的元素
    while (left < m)
    {
        tool[index++] = nums1[left++];
    }
    while (right < n)
    {
        tool[index++] = nums2[right++];
    }
    double result = 0.0;
    if (tool.size() % 2 == 0)
    {
        result = ((double)tool[tool.size() / 2 - 1] + (double)tool[tool.size() / 2]) / 2.0;
    }
    else
    {
        result = tool[tool.size() / 2];
    }
    return result;
}
```

思路二：双指针&移动(m+n)/2次；

用2个指针分别指向2个数组的头部，每次比较2指针对应的值后移动一次值较小的指针，当指针移动次数为`(m+n)/2`次时，即为中位数的位置。

时间复杂度：循环了(m+n)/2次，O(m+n);

空间复杂度：申请了常数个临时变量，O(1);

```c++
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2) 
{
    int m = nums1.size();
    int n = nums2.size();
    int left = 0;   // 左指针
    int right = 0;  // 右指针
    int mid_left = 0;   // (m+n)为偶数时,最中间两个数里左边那个数;(m+n)奇数时的中位数
    int mid_right = 0;

    // 循环(m+n)/2 + 1次后,中位数就是mid_right[m+n为偶数时,中位数是mid_left和mid_right]
    for (int i = 0; i <= (m + n) / 2; ++i)
    {
        mid_left = mid_right;
        if (left >= m)    // 左侧遍历完了;因为下标从0开始,故加上"等于"
        {
            mid_right = nums2[right++];
        }
        else if (right >= n)  // 右侧遍历完了
        {
            mid_right = nums1[left++];
        }
        else    // 左右都未遍历完,则比较两指针对应的值,值较小的那个指针移动
        {
            if (nums1[left] < nums2[right])
            {
                mid_right = nums1[left++];
            }
            else
            {
                mid_right = nums2[right++];
            }
        }
    }

    double result = (m + n) & 1 ? mid_right : (mid_left + mid_right) / 2.0;
    return result;
}
```

思路三：找第`k`小的数。

奇数有序数列找中位数，等同于找第`m/2`（m是数列长度）小的数；

偶数有序数列找中位数，等同于找第`m/2`（m是数列长度）小的数和第`m/2 + 1`小的数的平均数。

找第`k`小的数的算法思路：如果两个数列`n1`和`n2`中，`n1`中第`k/2`(向下取整)个数比`n2`中的第`k/2`(向下取整)个数的数值要小，则`n1`中这个数前面的数都不会是第`k`小的数。此时，直接删除这`k/2`（向下取整）个数，继续找剩余元素中第`k - (k/2)`小的数；一直到找剩余元素中第`1`小的数。边界情况，单独考虑。

时间复杂度：O(log(m+n)); 

空间复杂度：O(1); 

```c++
// 找第k小的数
double GetKth(vector<int> &nums1, vector<int> &nums2, int k)
{
    int m = nums1.size();
    int n = nums2.size();
    int left = 0;   // 左指针,表示当前左侧数组的开始位置
    int right = 0;  // 右指针,表示当前右侧数组的开始位置
    int mid = 0;    // 取k/2

    while (true)
    {
        if (left == m)
        {
            return nums2[right + k - 1];    // 由于此时k=mid,即right向右移动mid次时指向的元素;-1表示下标
        }
        if (right == n)
        {
            return nums1[left + k - 1];
        }
        if (k == 1)
        {
            return (nums1[left] < nums2[right]) ? nums1[left] : nums2[right];
        }
        mid = k / 2;
        int left_index = min(left + mid - 1, m - 1);   // 确保取值时的下标未越界
        int right_index = min(right + mid - 1, n - 1); // right+mid表示右指针的偏移量

        // 正常情况
        if (nums1[left_index] < nums2[right_index])
        {
            k = k - (left_index - left + 1);    // 正常应是k=k-mid,即删除mid个元素,mid=left_index-left+1;但有mid>m的情况
            left = left_index + 1; // left+mid-1不越界时相当于left+=mid,删除前面的元素;越界时直接left=m结束循环
        }
        else
        {
            k = k - (right_index - right + 1); // 需删除几个元素则移动几次
            right = right_index + 1;    // 移动min(mid,n)次
        }
    }
}
double findMedianSortedArrays(vector<int> &nums1, vector<int> &nums2)
{
    int m = nums1.size();
    int n = nums2.size();
    int k = (m + n) / 2;

    if (((m + n) & 1) == 0) // m+n是偶数时
    {
        return (GetKth(nums1, nums2, k) + GetKth(nums1, nums2, k + 1)) / 2.0;
    }
    else
    {
        return GetKth(nums1, nums2, k + 1);
    }
}
```

### 1.4.3 知识点

#### 1.4.3.1 归并排序（Merge Sort）

思路：先拆分成有序的小数列，再将有序数列动态合并起来；合并过程借助第三方数组最终整合成全部有序数列。

性质：时间复杂度-O(nlogn); 稳定排序；

参考：[图解排序算法（四）之归并排序](https://www.cnblogs.com/chengxiao/p/6194356.html); [百度百科之归并排序](https://baike.baidu.com/item/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F/1639015#6); 

归并的“分”而“治”之：

<img src="https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161218163120151-452283750.png" alt="归并的“分”而“治”之" style="zoom:33%;" />

图解归并过程：

<img src="https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161218194508761-468169540.png" alt="归并过程" style="zoom:33%;" />

<img src="https://images2015.cnblogs.com/blog/1024555/201612/1024555-20161218194621308-588010220.png" alt="归并过程" style="zoom: 33%;" />

C++代码：

```
// 归并排序中的合并操作
// input/output 分别为输入和输出指针
// left表示左端;right表示右端;middle表示中间；middle两边已经是有序的
void Merge(int *input, int left, int middle, int right, int *output)
{    
    int left_index = left;          // 左序列指针
    int right_index = middle + 1;   // 右序列指针
    int index = left;               // 临时数组指针
    while (left_index <= middle && right_index <= right)
    {
        if (input[left_index] <= input[right_index])
        {
            output[index++] = input[left_index++];
        } 
        else
        {
            output[index++] = input[right_index++];
        }
    }
    // 将左边剩余的元素放进output中
    while (left_index <= middle)
    {
        output[index++] = input[left_index++];
    }
    // 将右边剩余的元素放进output中
    while (right_index <= right)
    {
        output[index++] = input[right_index++];
    }
    // 将上面output中已经有序的元素拷贝到原数组中
    for (int i = left; i <= right; ++i)
    {
        input[i] = output[i];
    }
    //// 将已经有序的元素拷贝到原数组中 (另一种方法)
    //index = left;   // 特别注意:这里index不能为0,而应是left
    //while (left <= right)
    //{
    //    input[left++] = output[index++];
    //}
}

// 归并排序
void MergeSort(int *input, int left, int right, int *output)
{
    if (left < right)
    {
        int middle = (right + left) / 2;                // 将数列从中间一分为二
        MergeSort(input, left, middle, output);         // 对左边进行归并排序，使得左子序列有序
        MergeSort(input, middle + 1, right, output);    // 对右边进行归并排序，使得右子序列有序
        Merge(input, left, middle, right, output);      // 将两个有序的子序列进行合并
    }
}

int main()
{
    int input[] = { 9,72,7,22,20,33,16,20,7 };
    const int input_length = 9;
    for (int i = 0; i < input_length; ++i)
    {
        cout << input[i] << " ";
    }
    cout << endl;
    int output[input_length] = { 0 };
    MergeSort(input, 0, input_length - 1, output);
    
    for (int i = 0; i < input_length; ++i)
    {
        cout << output[i] << " ";
    }
    cout << endl;
    system("pause");
    return 0;
}
```

#### 1.4.3.2 比较函数

- C++自带比较函数

  `std::set`和`std::sort`等都默认从小到大排序，也可以通过C++提供的比较函数（如下）进行指定。

  ```c++
  // C++ 提供的四种比较函数; 位于 <functional> 头文件
  less<type>()			// 从小到大排序 <
  greater<type>()			// 从大到小排序 >
  less_equal<type>()		// 小于等于 <=
  greater_equal<type>()	// 大于等于 >=
  ```

  使用时，形如：

  ```c++
  std::set<int, std::greater<int>> my_set = {2,5,1,9,4};				// 注意这里greater<int>后没有小括号
  std::sort(my_vector.begin(),my_vector.end(),std::greater<int>());	// 这里greater<int>后必须有小括号
  ```

- 自定义比较函数

  参考：[std::sort-cplusplus.com](http://www.cplusplus.com/reference/algorithm/sort/?kw=sort); `std::sort`不是稳定排序，`std::stable_sort`是稳定排序；

  对于常见类型可以直接用C++自带比较函数即可，如果是自定义类型，可参考下面的`myfunction`和`myobject`; 

  ```c++
  // sort algorithm example
  #include <iostream>     // std::cout
  #include <algorithm>    // std::sort
  #include <vector>       // std::vector
  
  bool myfunction (int i,int j) { return (i<j); }
  
  struct myclass {
    bool operator() (int i,int j) { return (i<j);}
  } myobject;
  
  int main () {
    int myints[] = {32,71,12,45,26,80,53,33};
    std::vector<int> myvector (myints, myints+8);               // 32 71 12 45 26 80 53 33
  
    // using default comparison (operator <):
    std::sort (myvector.begin(), myvector.begin()+4);           //(12 32 45 71)26 80 53 33
  
    // using function as comp
    std::sort (myvector.begin()+4, myvector.end(), myfunction); // 12 32 45 71(26 33 53 80)
  
    // using object as comp
    std::sort (myvector.begin(), myvector.end(), myobject);     //(12 26 32 33 45 53 71 80)
  
    // print out content:
    std::cout << "myvector contains:";
    for (std::vector<int>::iterator it=myvector.begin(); it!=myvector.end(); ++it)
      std::cout << ' ' << *it;
    std::cout << '\n';
  
    return 0;
  }
  ```

  输出：

  ```c++
  myvector contains: 12 26 32 33 45 53 71 80
  ```

  实际工作中遇到的代码：

  ```c++
  static bool SortLess(const std::pair<uint32, int32> &one, const std::pair<uint32, uint32> &other)
  {
  	return one.first < other.first;
  }
  
  int FunctionName(std::vector<std::pair<uint32, uint32> > &data)
  {
  	// do something here
  	std::sort(data.begin(), data.end(), SortLess);
  	return 0;
  }
  ```

#### 1.4.3.3 是否为偶数

- 位运算：`(m&1) ? 奇 : 偶;`

  ```
  if((m & 1) == 0)	
  {
  	// m 为偶数
  }
  ```

  特别注意：

  **关系运算符的优先级高于位运算符**，因此`m&1==0`的写法等同于`m&(1==0)`，是错误的写法；

  补充：

  - 或(`|`)：有1为1，否则为0；
  - 与(`&`)：同1为1，否则为0；
  - 异或(`^`)：两相应位**不同**为1，否则为0；

- 被2整除为0时为偶数；

#### 1.4.3.4 数组中位数

某有序数组`data`长度为`m`，求其中位数时，常需要判断`m`是奇数还是偶数，一种通用的做法如下：

```c++
int left = (m+1)/2;
int right = (m+2)/2;
double median = (data[left] + data[right])/2.0;
```

如果`m`是偶数，则中位数取值为中间的两个数（左边的是left，右边的是right）的均值；

如果`n`是奇数，则`left`和`right`是同一个数，即为中位数。

## 1.5 Longest Palindromic Substring

Tags: 动态规划;

来源：[LeetCode-Longest Palindromic Substring](https://leetcode-cn.com/problems/longest-palindromic-substring); 

### 1.5.1 题目描述

Given a string `s`, return the longest palindromic substring in `s`.

 Example 1:

```c++
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

Example 2:

```
Input: s = "cbbd"
Output: "bb"
```


Constraints:

- 1 <= s.length <= 1000

- s consist of only digits and English letters.

### 1.5.2 解法

#### 1.5.2.1 动态规划法

三个步骤：

- Step 1: 定义数组元素的**含义**；

  本题问的是：找到`string s`的最长回文子串；

  `dp[i][j]`定义为：**`s`中第`i`个字符和第`j`个字符组成的子串`s[i..j]`是否是回文串**，`dp[i][j]=1`表示是，`dp[i][j]=0`表示不是。

  那么在所有`dp[i][j]=1`的`(i,j)`中，长度（`j-i+1`）最长的就是答案。

- Step 2: 找出数组元素之间的**关系式**；

  如果`s[i...j]`是回文串，那么去掉两端的两个字符后`s[i+1 ... j-1]`肯定也是回文串，即若`dp[i][j] = 1`，则有`dp[i+1][j-1] = 1`；反过来讲，如果`s[(i+1) ... (j-1)]`是回文串，并且分别向外扩展一个字符时`s[i] = s[j]`，那么`s[i ... j]`也是回文串。

  则有：

  `dp[i][j] = dp[i+1][j-1] && s[i]==s[j]`; 

- Step 3: 找出**初始值**；

  边界条件即只有1个字符和只有2个字符的情况，当只有1个字符时，肯定是回文串；当只有2个字符时，如果两个字符相同也是回文串，否则不是回文串。则有：

  `dp[i][i] = 1`;

  `dp[i][i+1] = (s[i] == s[i+1])`;

  从递推公式可知，需要先知道了`i+1`才会知道`i`，因此遍历时需要倒着遍历。

  > 对于二维数组，由于只有对角线和对角线的右侧一位有初始值，而动态规划的思想就是要利用历史数据来避免重复计算，因此在实现过程中进行循环时外层循环可采用倒序进行遍历。如果外层循环用正序，`i = {0,1,2,3,4}`，当取`[0][4]`时，需要先求`[1][3]`的值，而此时还没有循环到`[1][3]`，没有计算出它的值，那么就会出错。

时间复杂度：O(n^2);

空间复杂度：O(n^2);

参考代码：

> 该代码只是利用动态规划方法的一种较易理解的写法，还可以优化。

```c++
string longestPalindrome(string s)
{
    int m = s.size();
    if (m == 1)
    {
        return s;
    }

    // dp[i][j]表示:字符串s的第i个字符到第j个字符组成的子串s[i...j]是不是回文串,是为1,不是为0
    vector<vector<int>> dp(m + 1, vector<int>(m + 1, -1));

    // 初始化
    // 1个字符时肯定是回文串,即dp[i][i]=1;2个字符时,若两字符相同才是回文串
    for (int i = 1; i < m + 1; ++i)
    {
        dp[i][i] = 1;
        if (i < m)
        {
            dp[i][i + 1] = (s[i - 1] == s[i]) ? 1 : 0;
        }
    }

    // 迭代求解
    int max_length = 1;     // s非空时回文子串长度至少为1
    int begin_index = 0;    // 回文子串的起始位置下标
    for (int i = m - 1; i >= 1; --i)
    {
        for (int j = i + 1; j < m + 1; ++j)
        {
            // 在上面初始化中已赋值过的不再计算
            if (dp[i][j] == -1)
            {
                int temp = (s[i - 1] == s[j - 1]) ? 1 : 0;
                dp[i][j] = dp[i + 1][j - 1] * temp;
            }

            // 若s[i...j]是回文串,且该串长度更长,则更新
            if (dp[i][j] == 1 && (j - i + 1) > max_length)
            {
                max_length = j - i + 1;
                begin_index = i - 1;
            }
        }
    }
    return s.substr(begin_index, max_length);
}
```

#### 1.5.2.2 中心扩展法

回文串一定是对称的，其对称中心可以是1个字符，也可以是2个字符。可以枚举所有的对称中心，并进行左右扩展，扩展时判断左右字符是否相同即可。

时间复杂度：O(n^2);

空间复杂度：O(1);

参考代码：

```c++
// 扩展中心算法
// 已知字符串s,left和right是s上的两个下标位置;以left和right所组成的子串为中心不断向外扩展得到最长回文串
// 返回该最长回文串的开始与结束下标
pair<int, int> ExpandAroundCenter(const string &s, int left, int right)
{
    int begin = 0;  // 得到的最长回文串的开始与结束下标
    int end = 0;
    while (left >= 0 && right < s.size() && s[left] == s[right])
    {
        // 继续从中心向外扩
        --left;
        ++right;
    }
    // 只要向外扩过,则回退一步(left+1,right-1)后就是回文串的起止下标
    // 若未曾外扩过,则只有s[i]!=s[i+1],如"ab"这种情况,那么回退一步(left+1,right-1)后从(i,i+1)变成了(i+1,i)
    // 这种情况认为回文子串只有1个字符,统一取下标i对应的字符,如"ab"返回a
    begin = left + 1;
    end = right - 1;
    if (begin > end)
    {
        begin = end;
    }
    return { begin,end };
}

string longestPalindrome(string s)
{
    int m = s.size();
    if (m == 1)
    {
        return s;
    }

    // 所求的最长回文串的起始下标与结束下标
    int begin = 0;
    int end = 0;

    for (int i = 0; i < m; ++i)
    {
        auto palindrome1 = ExpandAroundCenter(s, i, i);             // 寻找以一个字符为中心的最长回文串
        auto palindrome2 = ExpandAroundCenter(s, i, i + 1);         // 寻找以两个字符为中心的最长回文串

        int length1 = palindrome1.second - palindrome1.first + 1;   // 以一个字符为中心找到的最长回文串的长度
        int length2 = palindrome2.second - palindrome2.first + 1;   // 以两个字符为中心找到的最长回文串的长度
        int length = end - begin + 1;                               // 所求的最长回文串的长度

        // 若有更长的回文串,则更新
        if (length1 > length)
        {
            length = length1;
            begin = palindrome1.first;
            end = palindrome1.second;
        }
        if (length2 > length)
        {
            length = length2;
            begin = palindrome2.first;
            end = palindrome2.second;
        }
    }

    return s.substr(begin, end - begin + 1);
}
```

#### 1.4.2.3 Manacher算法

参考：[Manacher's Algorithm](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-bao-gu/); 

时间复杂度：O(n);

空间复杂度：O(n); 

### 1.5.3 知识点

#### 1.5.3.1 动态规划

动态规划一些特点：

- 通过**避免重复节点的计算**来提升效率；（利用**历史记录**来避免重复计算）
- 使用了**字典/哈希表**来保存计算的中间结果；（可用**一维或二维数组**来保存）
- 用**空间**换**时间**；

详细说明参考另一篇单独的笔记《动态规划笔记》。

#### 1.5.3.2 拓扑排序

参考：[知乎-算法学习笔记(53):拓扑排序](https://zhuanlan.zhihu.com/p/260112913); 

**拓扑排序**是对`DAG(有向无环图)`上的节点进行排序，使得对于每一条有向边`u->v`，`u`都在`v`之前出现。

简单地说，是在不破坏节点**先后顺序**的前提下，把**DAG**拉成**一条链**。

以游戏中的科技树（如忍3的天赋点，不是树而只是DAG）举例，拓扑排序就是找到一种**可能的**点科技树的**顺序**（按某种**顺序**去点亮天赋点，结果不唯一）。

![忍3苍牙天赋点](https://img2.tapimg.com/bbcode/images/9e223d2a0012d60eab10641c3b722d11.png?imageView2/2/w/1320/h/9999/q/80/format/jpg/interlace/1/ignore-error/1)

#### 1.5.3.3 其他知识点

- std::string::substr

  ```c++
  string substr (size_t pos = 0, size_t len = npos) const;
  ```

  注意：参数1是起始位置的下标，参数2是子串的长度。

## 1.6 Regular Expression Matching

Tags: 

来源：[LeetCode - Regular Expression Matching](https://leetcode-cn.com/problems/regular-expression-matching); 

### 1.6.1 题目描述

Given an input string `s` and a pattern `p`, implement regular expression matching with support for `.` and `*` where:

- `.` Matches any single character.
- `*` Matches zero or more of the preceding element.

The matching should cover the **entire** input string (not partial). 

Example 1:

```c++
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

Example 2:

```c++
Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

Example 3:

```c++
Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```


Constraints:

- 1 <= s.length <= 20

- 1 <= p.length <= 30

- `s` contains only lowercase English letters.

- `p` contains only lowercase English letters, `.`, and `*`.

- It is guaranteed for each appearance of the character `*`, there will be a previous valid character to match.

### 1.6.2 解法

思路：动态规划；时间复杂度：O(mn); 空间复杂度：O(mn); (m,n是s和p的长度)

三个步骤：

- Step 1: 定义数组元素的**含义**；

  本题问的是：判断字符串`s`和字符串`p`是否匹配；

  `dp[i][j]`定义为：`s`的前`i`个字符是否跟`p`的前`j`个字符匹配，`dp[i][j]=true`表示匹配，`dp[i][j]=flase`表示不匹配。

  那么`dp[m][n]`的就是答案（`m,n`分别是`s,p`的长度）。

- Step 2: 找出数组元素之间的**关系式**；

  思路如下：

  1. `p` 的第`j`个字符是`*`：

     1.1 `*`号前面那个字符`p(j - 1)`与`s`的第`i`个字符匹配；分3种情况：

     - `*`前面的字符出现0次：`dp[i][j] = dp[i][j - 2]`;
     - `*`前面的字符出现1次: `dp[i][j] = dp[i - 1][j - 2]`;
     - `*`前面的字符出现2次或多次: `dp[i][j] = dp[i-1][j]`; 

     即：`dp[i][j] = dp[i][j - 2] || dp[i - 1][j - 2] || dp[i - 1][j]`;

     1.2 `*`号前面那个字符`p(j - 1)`与`s`的第`i`个字符不匹配；分3种情况：

     - `*`前面的字符出现0次: `dp[i][j] = dp[i][j - 2]`;
     - `*`前面的字符出现1次: `dp[i][j] = false`;
     - `*`前面的字符出现2次或多次: `dp[i][j] = false`;

     即：`dp[i][j] = dp[i][j - 2] || false || false = dp[i][j-2]`; 

     整合1.1和1.2的信息有：

     `dp[i][j] = dp[i][j - 2] || ( (dp[i - 1][j - 2] || dp[i - 1][j]) && p(j-1)匹配s(i) )`; 

  2. `p` 的第`j`个字符不是`*`：

     2.1 `p`的第`j`个字符跟`s`的第`i`个字符匹配: `dp[i][j] = dp[i-1][j-1]`;

     2.2 `p`的第`j`个字符跟`s`的第`i`个字符不匹配: `dp[i][j] = false`;

  即：`dp[i][j] = (dp[i-1][j-1] && p(j)匹配s(i)) || false = dp[i-1][j-1] && p(j)匹配s(i)`; 

  > p(j)匹配s(i) 即：p的第j个字符跟s的第i个字符匹配，包括两字符相同或p的第j个字符是'.' ；

  啰嗦伪代码解释见下文。

- Step 3: 找出**初始值**；

  根据题意,`*`前必须是有效字符,因此`p`第1个字符不会是`*`,且不会有两个`*`挨着出现。则有：

  `dp[0][0] = 1`;

  `dp[0][1] = 0`;

  虽然字符串长度规定了不会为0，但迭代计算时因为会有`i-1`和`i-2`，因此要把`dp[0][i]`作为初始值先求出来。

  如果`p`的第`i`个字符是`*`，则要看`p`的前`i-2`个字符是否匹配，即：

  ```c++
  for(int i=2; i<=n; ++i) // m和n实际不会为0,此处只是为了赋初值
  {
  	// 若p的第i个字符是*,则要看p的前i-2个字符是否匹配
  	if(p[i-1] == '*')
  	{
  		dp[0][i] = dp[0][i-2];
  	}
  }
  ```

啰嗦伪代码解释：

```c++
先明确:字符匹配指的是p的第j个字符跟s的第i个字符相同,或者p的第j个字符是万能匹配符`.`;
if (p的第j个字符是 * 号) 
{
    if (*号前面那个字符p(j - 1)与s的第i个字符匹配)
    {
        // 根据*前面的字符出现的次数,分三种情况:
        // 1. 出现0次: 
        s = "##abc", p = "###ac*";
        p就变成了"###a",p消去了2位,则有: dp[i][j] = dp[i][j - 2];
        
        // 2. 出现1次: 
        s = "##abc", p = "###ac*";
        p就变成了"###ac", 由于s的第i个字符跟p的第j - 1个字符相同, 可以消去; 
        此时s消去了1位, p消去了2位, 则有: dp[i][j] = dp[i - 1][j - 2];
        
        // 3. 出现2次或多次: 
        s = "##abc", p = "###ac*";
        p可以变成"###acc"或"###accc...", 当p末尾的字符c可以是多个时, 需要s往前去找是否也有多个跟s末尾字符c相同的字符相匹配,每找到一个,i往前移一位,即s消去1位,而p不消位,则有: dp[i][j] = dp[i-1][j];
        
        ///综合这三种情况有:
        dp[i][j] = dp[i][j - 2] 或 dp[i - 1][j - 2] 或 dp[i - 1][j];
    }
    else// *号前面那个字符p(j-1)与s的第i个字符不匹配
    {
        // 根据*前面的字符出现的次数,分三种情况:
        // 1. 出现0次: 
        s = "##abc", p = "###ad*";
        p就变成了"###a", p消去了2位, 则有: dp[i][j] = dp[i][j - 2];
        
        // 2. 出现1次: 
        s = "##abc", p = "###ad*";
        p就变成了"###ad", 由于s的第i个字符跟p的第j - 1个字符不相同, 则有: dp[i][j] = false;
        
        // 3. 出现2次或多次: 
        s = "##abc", p = "###ad*";
        p可以变成"###add"或"###addd...", 由于s的第i个字符跟p的第j - 1个字符不相同, 则有: dp[i][j] = false; 
        
        ///综合这三种情况有:
        dp[i][j] = dp[i][j - 2] 或 false 或 false;
    }
    
    // 用整洁的方式表示上面的情况:
    情况1. *号前面那个字符p(j - 1)与s的第i个字符匹配时: dp[i][j] = dp[i][j - 2] 或 dp[i - 1][j - 2] 或 dp[i - 1][j];
    情况2. *号前面那个字符p(j - 1)与s的第i个字符不匹配时: dp[i][j] = dp[i][j - 2] 或 false 或 false;
    
    令A表示情况1和情况2里的公共部分dp[i][j] = dp[i][j - 2]，把它提取出来；且dp[i][j]默认为false,因此false情况也可以不考虑。
    令B表示情况1里除去公共部分的结果:*号前面那个字符p(j - 1)与s的第i个字符匹配时: dp[i][j] = dp[i - 1][j - 2] 或 dp[i - 1][j];
    则变成：
    dp[i][j] =  情况1 或 情况2 = A 或 B；
}
else // p的第j个字符不是 * 号
{
    if(p的第j个字符跟s的第i个字符匹配)
    {
        s = "##abc", p = "##ac"; 
        s和p可以各消去1位,则有: dp[i][j] = dp[i-1][j-1];
    }
    else // p的第j个字符跟s的第i个字符不匹配
    {
        s = "##abc", p = "##ad";
        则有: dp[i][j] = false;
    }
    由于dp[i][j]默认为false,false情况可以不考虑；上面的情况可以简洁地写成：
    dp[i][j] = dp[i-1][j-1];
}
```

参考代码：

```c++
// *代表前面的字符可以出现0次,1次或多次;如:ab*可匹配a,ab,abb,abbb..b
// 约定:s(i)表示s的第i个字符,即s[i-1](下标为i-1);p(j)同理;    
bool isMatch(string s, string p)
{
    int m = s.size();
    int n = p.size();

    // dp[i][j]:s的前i个字符是否跟p的前j个字符匹配
    vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));

    // 初始值设置
    // 根据题意,*前必须是有效字符,因此p第1个字符不会是*,且不会有两个*挨着出现
    dp[0][0] = 1;
    dp[0][1] = 0;
    for (int i = 2; i <= n; ++i) // m和n实际不会为0,此处只是为了赋初值
    {
        // 若p的第i个字符是*,则要看p的前i-2个字符是否匹配
        if (p[i - 1] == '*')
        {
            dp[0][i] = dp[0][i - 2];
        }
    }

    // 迭代求解
    for (int i = 1; i <= m; ++i)
    {
        for (int j = 1; j <= n; ++j)
        {
            if (p[j - 1] == '*')
            {
                dp[i][j] = dp[i][j-2] || ( (p[j-1-1]==s[i-1] || p[j-1-1]=='.') && (dp[i-1][j] || dp[i-1][j-2]) );
            }
            else
            {
                dp[i][j] = dp[i-1][j-1] && (s[i-1] == p[j-1] || p[j-1] == '.');
            }
        }
    }

    return dp[m][n];
}
```

### 1.6.3 知识点

- 正则表达式中的`*`: `*`代表前面的字符可以出现0次,1次或多次;如:`ab*`可匹配`a,ab,abb,abbb..b`;

## 1.7 Container With Most Water

Tags: 双指针；

来源：[LeetCode - Container With Most Water](https://leetcode-cn.com/problems/container-with-most-water); 

### 1.7.1 题目描述

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `i th` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the maximum amount of water a container can store.

**Notice** that you may not slant the container.

Example 1:

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg" alt="pic" style="zoom: 80%;" />

```c++
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
```

Example 2:

```c++
Input: height = [1,1]
Output: 1
```


Constraints:

- n == height.length

- 2 <= n <= 10^5

- 0 <= height[i] <= 10^4



### 1.7.2 解法

思路：头尾双指针法；

二维平面上，所求的最大容量，即：面积S = 高度h * 宽度w；假定两指针分别为`i,j`，所对应的水槽的高度分别为`h[i]`和`h[j]`，此时水槽面积为`S(i,j)`；类似木桶理论，这里可容纳水的高度由**短板**决定，因此面积为：

`S = min(h[i],h[j]) * (j-i)`；

每次计算结束移动指针时，要移动哪个指针呢？

可知，无论移动哪个，**宽度**都会减少，而**高度** = `min(h[i],h[j])`，有两种情况：

- 若向内**移动长板**，水槽的短板 `min(h[i],h[j])`只会变小或不变，此时面积 **一定变小**；
- 若向内**移动短板**，水槽的短板`min(h[i],h[j])`**可能会变大**，也可能变小或不变，此时面积**可能变大**；

显然，移动长板时面积是一定变小的，此题是为了求**最大**容水量，因此移动指针时，是**向内移动短板**；

时间复杂度：O(n); n是数组长度；

空间复杂度：O(1); 

> 另一种方法是暴力两层循环法，时间复杂度O(n^2)太高了，不可取；

参考代码：

```c++
int maxArea(vector<int>& height) 
{
    int n = height.size();
    int length = 0; // 容器的长
    int width = 0;  // 容器的宽
    int area = 0;   // 容器的面积
    int i=0;        // 左指针
    int j=n-1;      // 右指针
    while(i<j)
    {
        length = j-i;            
        width = min(height[i],height[j]);
        area = max(area,length*width);
        // 每次移动高度小的指针
        if(height[i]<height[j])
        {
            ++i;
        }
        else
        {
            --j;
        }
    }
    return area;
}
```

### 1.7.3 知识点

- 双指针的思路在具体实现时也要考虑效率；如两层循环也是双指针的思路，但更像是穷举遍历所有情况。

## 1.8 3Sum

Tags: 双指针；

来源：[LeetCode - 3Sum](https://leetcode-cn.com/problems/3sum); 

### 1.8.1 题目描述

Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.

 Example 1:

```c++
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
```

Example 2:

```c++
Input: nums = []
Output: []
```

Example 3:

```c++
Input: nums = [0]
Output: []
```


Constraints:

- 0 <= nums.length <= 3000
- -10^5 <= nums[i] <= 10^5

### 1.8.2 解法

思路：排序+双指针；

- 特殊情况判断：输入数组长度`n`小于3则返回{};
- 对数组进行排序；
- 遍历排序后的数组：
  - 若`nums[i]>0`，直接返回当前结果；（因为已排好序，后面的3个数相加肯定不等于0）
  - 若存在重复元素（`nums[i]==nums[i-1]`），则跳过该次循环，避免出现重复解（如`nums = {0,0,0,0,0,0}`的情况）；
  - 令左指针`left=i+1`，右指针`right=n-1`，当`left<right`时，执行循环：
    - 若`nums[i]+nums[left]+nums[right]==0`，保存当前结果；并判断`left`和`right`是否和下一位置的元素重复，并去除重复解；最后`left`和`right`同时向内移动一个位置；
    - 若3数和小于0，说明`nums[left]`小了，需要`left`右移；
    - 若3数和大于0，说明`nums[right]`大了，需要`right`左移。

时间复杂度：O(n^2); 排序O(nlogn)，遍历数组O(n)与双指针遍历O(n)同时进行是O(n^2)，最后取最高阶的O(n^2); 

空间复杂度：O(1); 

参考代码：

```c++
vector<vector<int>> threeSum(vector<int>& nums) 
{
    if(nums.size() < 3)
    {
        return {};
    }
    vector<vector<int>> result;
    // 将数组从小到大排序
    sort(nums.begin(),nums.end());
    vector<int> repeat(nums.size(),0);  // 用以去重的辅助数组
    int left = 0;
    int right = 0;
    for(int i=0; i<nums.size(); ++i)
    {
        if(nums[i]>0)   // 3个都是大于0的数,和不可能为0,直接返回已有结果
        {
            return result;
        }
        // 重复值跳过，避免出现重复解 (如:全是0的数组)
        if(i>0 && nums[i] == nums[i-1])
        {
            continue;
        }
        left = i+1;
        right = nums.size()-1;
        while(left < right)
        {
            // 判断
            if(nums[i] + nums[left] + nums[right] == 0)
            {
                result.push_back({nums[i],nums[left],nums[right]});
                // 重复值跳过，避免出现重复解
                while(left<right && nums[left]==nums[left+1])
                {
                    left++;
                }
                while(left<right && nums[right]==nums[right-1])
                {
                    right--;
                }
                left++;
                right--;
            }
            else if(nums[i] + nums[left] + nums[right] < 0) // 当前和小了,应该向右移动找较大的数
            {
                left++;
            }
            else    // 当前和大了,应该向左移动找较小的数
            {
                right--;
            }
        }
    }
    return result;
}
```

### 1.8.3 知识点

- 求解时考虑特殊情况作为测试用例，如`nums={0,0,0,0,0,0}`; 

## 1.9 Letter Combinations of a Phone Number

Tags: 回溯；DFS；

来源：[LeetCode - Letter Combinations of a Phone Number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number); 

### 1.9.1 题目描述

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer in **any order**.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

![pic](https://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

Example 1:

```c++
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

Example 2:

```c++
Input: digits = ""
Output: []
```

Example 3:

```c++
Input: digits = "2"
Output: ["a","b","c"]
```


Constraints:

- 0 <= digits.length <= 4
- digits[i] is a digit in the range ['2', '9'].

### 1.9.2 解法

思路1：队列法；

- 先将数字对应字符存入数组（用数组下标当做数字，数组的值是字符串）；
- 遍历输入的每个数字：
  - 对第1个数字，遍历其对应的各个字符，依次放入队列；
  - 对后序的数字：
    - 遍历当前队列，取出队首元素，并依次跟当前数字对应的字符逐个组合后**进入队列**；
- 此时队列中的元素就是所求的组合；

时间复杂度：`O(3^m*4^n)`，其中m是输入中对应3个字母的数字个数（包括数字 2、3、4、5、6、8），n 是输入中对应 4 个字母的数字个数（包括数字 7、9），m+n 是输入数字的总个数。当输入包含 m 个对应 3 个字母的数字和 n 个对应 4 个字母的数字时，不同的字母组合一共有3^m*4^n种，需要遍历每一种字母组合。

空间复杂度：`O(3^m*4^n)`，一共要生成`3^m*4^n`个结果；

参考代码：

```c++
vector<string> letterCombinations(string digits) 
{
    int n = digits.size();
    if (n <= 0)
    {
        return {};
    }
    vector<string> nums = { " "," ","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz" };
    // 队列法:先将第1个数字的字符依次进队;将队列元素依次出队,并遍历下一个数字的字符,组合后再入队;
    queue<string> res;
    for (int i = 0; i < n; ++i)
    {
        int num = digits[i] - '0';
        int length = nums[num].size();
        if (i == 0)
        {
            // 遍历第1个数字
            for (int j = 0; j < length; ++j)
            {
                res.push(string(1, nums[num][j]));
            }
        }
        else
        {
            int res_len = res.size();
            for (int j = 0; j < res_len; ++j)
            {
                auto tmp = res.front();
                res.pop();
                // 遍历其他数字
                for (int k = 0; k < length; ++k)
                {
                    res.push(tmp + string(1,nums[num][k]));
                }
            }
        }
    }
    vector<string> result;
    while (!res.empty())
    {
        result.push_back(res.front());
        res.pop();
    }
    return result;
}
```

思路2：回溯法；

类似深度优先搜索的思想；

时间复杂度：`O(3^m*4^n)`; 

空间复杂度：O(m+n); 

参考代码：

```c++
vector<string> letterCombinations(string digits)
{
    if(digits.size()<=0)
    {
        return {};
    }
    vector<string> result;
    vector<string> input = { " "," ","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz" };
    int index = 0;
    string buffer = ""; // 临时变量存放中间结果
    DFS(result,input,digits,index,buffer); 
    return result;
}
// 利用Depth First Search(DFS)的思想进行回溯
void DFS(vector<string> &result, vector<string> &input, string &digits, int index, string buffer)
{
    if(index == digits.size())
    {
        result.push_back(buffer);
        return;
    }
    int num = digits[index]-'0';    // 当前数字
    int length = input[num].size(); // 当前数字对应字符串的长度
    for(int i=0; i<length; ++i)
    {
        buffer.push_back(input[num][i]);
        DFS(result,input,digits,index+1,buffer);
        buffer.pop_back();
    } 
}
```

### 1.9.3 知识点

- 

## 1.10 Remove Nth Node From End of List

Tags: 链表；双指针

来源：[LeetCode - Remove Nth Node From End of List](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list); 

### 1.10.1 题目描述

Given the `head` of a linked list, remove the `nth` node from the end of the list and return its head.

 Example 1:

![pic](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```c++
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```

Example 2:

```c++
Input: head = [1], n = 1
Output: []
```

Example 3:

```c++
Input: head = [1,2], n = 1
Output: [1]
```


Constraints:

- The number of nodes in the list is sz.
- 1 <= sz <= 30
- 0 <= Node.val <= 100
- 1 <= n <= sz

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
```

### 1.10.2 解法

思路1：暴力法；

先遍历整个链表获得链表长度`size`，则正序的第`size-n+1`个节点即为待删除节点；循环到待删节点的前一个节点，进行删除操作即可。

时间复杂度：O(n); 

空间复杂度：O(1); 

参考代码：

```c++
ListNode *removeNthFromEnd(ListNode *head, int n)
{
    int size = 0;
    ListNode *current_node = head;
    // 获取链表长度
    while (current_node != nullptr)
    {
        ++size;
        current_node = current_node->next;
    }
    current_node = head;
    int index = size - n + 1;   // 正序数第index个节点要被删除
    if (index == 1)          // 要删除节点是第1个节点
    {
        return current_node->next;
    }
    // 得到被删除节点的前驱节点,方便删除操作
    for (int i = 1; i < index - 1; ++i)
    {
        current_node = current_node->next;
    }
    current_node->next = current_node->next->next;
    return head;
}
```

思路2：栈；

先将所有节点入栈，再依次出栈，出去`n`个元素后，当前栈顶元素正好是待删除节点的前驱节点。

时间复杂度：O(n); 

空间复杂度：O(n); 

参考代码：

```c++
ListNode* removeNthFromEnd(ListNode* head, int n) 
{
    ListNode *current_node = head;
    stack<ListNode*> tool;
    // 入栈
    while(current_node!=nullptr)
    {
        tool.push(current_node);
        current_node = current_node->next;
    }
    for(int i=0; i<n; ++i)
    {
        tool.pop();
    }
    if(tool.empty())          // 要删除节点是第1个节点
    {
        return head->next;
    }
    current_node = tool.top();  // 栈顶元素即为待删除节点的前驱节点
    current_node->next = current_node->next->next;        
    return head;
}
```

思路3：双指针；

时间复杂度：O(n); 

空间复杂度：O(1); 

参考代码：

```c++
ListNode* removeNthFromEnd(ListNode* head, int n) 
{
    ListNode *dummy = new ListNode(0,head);
    ListNode *first = head;
    ListNode *second = dummy;
    for(int i=0; i<n; ++i)
    {
        first = first->next;
    }
    while(first)
    {
        first = first->next;
        second = second->next;
    }
    second->next = second->next->next;
    ListNode *res = dummy->next;
    delete dummy;
    return res;
}
```

### 1.10.3 知识点

- 哑节点（dummy node）

  在对链表进行操作时，一种常用技巧是添加一个哑节点（dummy node），它的`next`指针指向链表的头节点。这样一来，就不需要对头节点进行特殊的判断了。记得释放内存。

  ```c++
  ListNode *dummy = new ListNode(0,head);
  if(dummy == nullptr)
  {
  	return;
  }
  // do something
  // 记得释放内存
  if(dummy != nullptr)
  {
  	delete dummy;
  	dummy = nullptr;
  }
  ```


## 1.11 Valid Parentheses 

Tags: 栈；哈希表；

来源：[Valid Parentheses](https://leetcode-cn.com/problems/valid-parentheses); 

### 1.11.1 题目描述

Given a string `s` containing just the characters `'(', ')', '{', '}', '['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.


Example 1:

```c++
Input: s = "()"
Output: true
```

Example 2:

```c++
Input: s = "()[]{}"
Output: true
```

Example 3:

```c++
Input: s = "(]"
Output: false
```


Constraints:

- `1 <= s.length <= 10^4`
- `s` consists of parentheses only `'()[]{}'`.

### 1.11.2 解法

思路：栈+哈希表；

- s长度是奇数直接是false；

- 用哈希表存储左右括号的对应关系（此处用右括号作为key，对应左括号作为value）；

- 遍历s中各个字符ch：

  - 如果ch不是右括号：

    入栈；

  - 如果ch是右括号：

    - 栈空：return false;
    - 栈非空，但栈顶元素不匹配：return false; （如果s有效，那它前面那个字符（即栈顶元素）必须是对应的左括号，因为中间匹配的已经pop掉了）；
    - 栈非空，且栈顶元素匹配：pop; 

时间复杂度：O(n); 

空间复杂度：O(n); (或O(n+a))

参考代码：

```c++
bool isValid(string s)
{
    int n = s.size();
    if ((n & 1) == 1) // s长度为奇数
    {
        return false;
    }
    // 建立左右括号的对应关系,以右括号为key,对应左括号为value
    map<char, char> dict = { {')','('},{']','['},{'}','{'} };
    // 利用一个栈
    stack<char> tools;
    for (char ch : s)
    {
        // 是右括号时,要判断栈里是否有匹配的左括号
        if (dict.find(ch) != dict.end())
        {
            if (tools.empty() || tools.top() != dict[ch])
            {
                return false;
            }
            else
            {
                tools.pop();
            }
        }
        else    // 不是右括号时入栈
        {
            tools.push(ch);
        }
    }
    return tools.empty();
}
```



### 1.11.3 知识点

- 有对应关系时可以考虑哈希表；

## 1.12 Merge Two Sorted Lists

Tags: 递归；

来源：[Merge Two Sorted Lists](https://leetcode-cn.com/problems/merge-two-sorted-lists); 

### 1.12.1 题目描述

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists in a one **sorted** list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list. 

Example 1:

![pic](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```c++
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

Example 2:

```c++
Input: list1 = [], list2 = []
Output: []
```

Example 3:

```c++
Input: list1 = [], list2 = [0]
Output: [0]
```


Constraints:

- The number of nodes in both lists is in the range `[0, 50]`.
- `-100 <= Node.val <= 100`
- Both `list1` and `list2` are sorted in **non-decreasing** order.

### 1.12.2 解法

思路1：递归；

- l1为空, return l2; 
- l2为空, return l1; 
- 都不为空，比较两者的头节点大小：
  - l1 < l2, 则继续递归找 l1->next和l2 的合并有序链表, return l1;
  - l2 < l1, 则继续递归找 l2->next和l1 的合并有序链表, return l2; 

时间复杂度：O(m+n); 

空间复杂度：O(m+n);

参考代码：

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) 
    {
        if(list1 == nullptr)
        {
            return list2;
        }
        else if(list2 == nullptr)
        {
            return list1;
        }
        else
        {
            // 比较第1个节点谁更小
            if(list1->val < list2->val)
            {
                list1->next = mergeTwoLists(list1->next, list2);
                return list1;
            }
            else
            {
                list2->next = mergeTwoLists(list2->next,list1);
                return list2;
            }
        }
    }
};
```

思路2：迭代法；

类似用归并的思想将两个有序数组进行合并。

- 新建一个辅助节点；
- 循环遍历两个链表，直到任一个链表为空这：
  - 比较两链表，谁小谁链接到辅助节点上；并向后继续移动小的链表和辅助链表；
- 把非空链表的剩余部分也接到辅助链表上；
- 则辅助链表的next就是所求；（别忘了释放内存，以下代码仅为演示）

时间复杂度：O(m+n); 

空间复杂度：O(1); 

参考代码：

```c++
ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) 
{
    ListNode *dummy = new ListNode(-1); // 定义一个辅助的哑节点；别忘了释放内存
    ListNode *current = dummy;
    while(list1!=nullptr && list2!=nullptr)
    {
        if(list1->val < list2->val)
        {
            current->next = list1;
            list1 = list1->next;
        }
        else
        {
            current->next = list2;
            list2 = list2->next;
        }
        current = current->next;
    }
    // 把非空链表的剩余部分链接进来
    current->next = (list1 != nullptr) ? list1 : list2;
    return dummy->next;
}
```

## 1.13 Generate Parentheses

Tags: DFS; 递归;

来源：[Generate Parentheses](https://leetcode-cn.com/problems/generate-parentheses); 

### 1.13.1 题目描述

Given `n` pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

 Example 1:

```c++
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

Example 2:

```c++
Input: n = 1
Output: ["()"]
```


Constraints:

- `1 <= n <= 8`

### 1.13.2 解法

思路1：DFS+递归；

类似画一棵二叉树，左括号`(`是左子树，右括号`)`是右子树。共有`n`对括号，则左括号数量和右括号数量都是`n`。由于右括号必须有相应的左括号匹配，因此先画左括号，且要确保已经画出的左括号数量大于右括号数量时才画右括号。

- 左括号、右括号数量同时达到`n`时，返回“从根节点到叶节点”的这条路径；
- 否则，继续递归：
  - 如果左括号数量小于`n`: 生成左子树，左括号数量+1；
  - 如果右括号数量小于`n`，且小于当前左括号数量时：生成右子树，右括号数量+1；

示例代码：

```c++
class Solution {
public:
    vector<string> generateParenthesis(int n) 
    {
        // dfs 思路
        vector<string> result;
        string buffer = "";  // 中间结果
        DFS(result,n,buffer,0,0);
        return result;
    }

    // buffer存储中间结果; left_count,right_count分别表示左、右括号的数量
    void DFS(vector<string> &result, int &n, string buffer, int left_count, int right_count)
    {
        if(left_count==n && right_count==n)
        {
            result.push_back(buffer);
            return;
        }
        else
        {
            if(left_count<n)
            {
                DFS(result, n, buffer+"(", left_count + 1, right_count);
            }
            if(right_count<n && right_count<left_count)
            {
                DFS(result, n, buffer+")", left_count, right_count + 1);
            }
        }
    }
};
```

思路2：动态规划；

参考：[动态规划解法](https://leetcode-cn.com/problems/generate-parentheses/solution/zui-jian-dan-yi-dong-de-dong-tai-gui-hua-bu-lun-da/);  参考该思路以及下面的讨论（@lambda 的解释很能帮助理解）。

整体思路是：

1. 求`n`对括号生成的所有有效组合`f(n)`时，所有有效组合的第一个元素肯定是`"("`; 且必然会有一个右括号`")"`与之相对应；

2. 此时`n`对括号就只**剩下`n-1`对括号需要安放**；

3. 在这`n-1`对括号中，假定有`i`对括号（`0 <= j <= n-1`） 位于第1步的这对括号**内部**，那么剩下的`(n-1) - i`对括号就都位于第1步的这对括号**右侧**；则有：**`f(i) = "(" + f(j) + ")" + f(n-1-j)`**; 

   [注：当`j=0`时，相当于所有`n-1`对括号都在第1步的括号对右侧；当`j=n-1`时，相当于所有`n-1`对括号都在第1步的括号对内部；]

4. 遍历`f(i)`和`f(n-1-i)`中的所有元素（双层循环），并按第3步中的规则进行组合，便得到`f(n)`; 

动态规划法：

- `f(n) = dp[n]`;
- `dp[i]`表示`i`对括号生成的所有有效组合；则`dp[n]`就是所求；
- 初始值：`dp[0]={""}`; `dp[1]={"()"}`;
- 转移方程：`dp[i] = "(" + dp[j] + ")" + dp[n-1-j]`;

示例代码:

```c++
vector<string> generateParenthesis(int n)
{
    // 动态规划
    if (n == 0)
    {
        return { "" };
    }
    if (n == 1)
    {
        return { "()" };
    }

    vector<vector<string>> dp(n + 1);

    // 初始值
    dp[0] = { "" };
    dp[1] = { "()" };

    for(int i=2; i<=n; ++i)			// 用以求dp[i]
    {
        for(int j=0; j<i; ++j)		// 遍历 0 <= j <= n-1
    	{
        	for(auto inner : dp[j])	// 遍历f(j)中所有元素
        	{
            	for(auto outer : dp[n-1-j])	// 遍历f(n-1-j)中所有元素
            	{
                	string tmp = "(" + inner + ")" + outer;
                	dp[i].push_back(tmp);
            	}
        	}
    	}
    }
    
    return dp[n];
}
```



# 2. Linked List

其他题目。

## 2.1 Transpose Linked List

Tags: Single Linked List, Transpose;

来源：阿里面试题；

### 2.1.1 题目描述

Given the `head` of a linked list (which is the representation of a matrix), return the **transpose** of this "matrix".

给定一个单链表a，请返回单链表b（如下图所示）。

![pic](https://github.com/eppesh/Pictures/blob/main/Transpose%20Linked%20List.png)

```c++
input: 1->2->3->4->5->6->7->8->9
output: 1->4->7->2->5->8->3->6->9
```

### 2.1.2 解法

#### 2.1.2.1 数组法（一维 or 二维）；

不要局限在面试官强调的**单链表**上，因为涉及到链表的节点更换，乍一看可能没有好的思路。但数据结构的使用本来就是要考虑适配性，即总是尽量选择**合适**的数据结构来解决问题。该题中，要把“矩阵”变成它的转置，数组就是一种合适的数据结构。

方法1：一维数组；

```c++
// 输出的代码删掉了
int main()
{
    // 生成 rows * cols 的矩阵
    int rows = 2;               // 行数
    int cols = 3;               // 列数
    std::vector<int> input(rows * cols);
    for (int i = 0; i < rows * cols; ++i)
    {
        input[i] = i + 1;
    }

    // 生成转置矩阵
    std::vector<int> output(rows * cols);
    
    // 双循环写法(更易理解)
    int index = 0;
    for (int j = 0; j < rows; ++j)
    {
        for (int i = 0; i < cols; ++i)
        {
            output[i * rows + j] = input[index++];
        }
    }
    
    // 单循环写法
    /*
    for(int i=0; i<rows * cols; ++i)
    {
        output[(i%cols) * rows + (i/cols)] = input[i];
    }
    */
    return 0; 
}
```

输出：

```c++
input: 1,2,3,4,5,6
output:1,4,2,5,3,6
```

方法2：二维数组；

```c++
int main()
{
    // 生成 rows * cols 的矩阵
    int rows = 2;               // 行数
    int cols = 3;               // 列数
    std::vector<std::vector<int>> input(rows, std::vector<int>(cols));
    for (int j = 0; j < rows; ++j)
    {
        for (int i = 0; i < cols; ++i)
        {
            input[j][i] = j * rows + i + 1;
        }
    }

    // 生成转置矩阵; 注意output的行数和列数正好跟input相反
    std::vector<std::vector<int>> output(cols, std::vector<int>(rows));
    int index = 0;
    for (int j = 0; j < rows; ++j)
    {
        for (int i = 0; i < cols; ++i)
        {
            output[i][j] = input[j][i];	// 循环是按input先rows再cols遍历的
        }
    }

    return 0; 
```

输出：

```c++
input: 1,2,3,3,4,5
output:1,3,2,4,3,5
```

#### 2.1.2.2 链表法

假如面试官要求输入和输出都必须是链表呢？还是可以借助数组来实现转置，然后再按要求把转置后的结果转换成链表形式。（有更好的方法后再补充）

示例：（输出的相关代码未展示）

```c++
#include <vector>

template <class T>
class ListNode
{
public:
    ListNode() :value_(0), next_(nullptr) {}
    ListNode(T value) :value_(value), next_(nullptr) {}
    ListNode(T value, ListNode<T> *next) : value_(value), next_(next) {}
public:
    T value_;
    ListNode<T> *next_;
};

template <class T>
ListNode<T> *TransposeList(ListNode<T> *input, int rows, int cols)
{
    // 异常判断暂时省去
    // 利用一维数组先进行节点转置(但前后链接关系未变)
    std::vector<ListNode<T> *> tools;
    ListNode<T> *head = input;
    while (head != nullptr)
    {
        tools.push_back(head);
        head = head->next_;
    }
    std::vector<ListNode<T> *> out(rows * cols);
    int index = 0;
    for (int j = 0; j < rows; ++j)
    {
        for (int i = 0; i < cols; ++i)
        {
            out[i * rows + j] = tools[index++];
        }
    }
    
    // 类似新建链表的方式重新设置前后链接关系
    ListNode<T> *result = nullptr;			// 输出链表的头指针
    ListNode<T> *tail = nullptr;			// 输出链表的尾指针,在尾部依次添加节点,用于构建新的链接关系
    for (auto it : out)
    {
        if (result == nullptr)
        {
            result = it;
        }
        else
        {
            tail->next_ = it;
            // 注意：如果操作前最后一个节点的next_不为空,这里要加上`it->next_ = nullptr`来保证操作完最后一个节点的next_为空
        }
        tail = it;
    }
    return result;
}

int main()
{
    // 生成输入链表
    ListNode<int> *head = nullptr;          // 链表头节点
    ListNode<int> *tail = nullptr;          // 链表尾节点,用于在尾部插入新节点
    ListNode<int> *new_node = nullptr;      // 待插入的新节点
    int rows = 4;
    int cols = 3;
    for (int i = 0; i < rows * cols; ++i)
    {
        new_node = new ListNode<int>(i + 1);
        if (head == nullptr)
        {
            head = new_node;
        }
        else
        {
            tail->next_ = new_node;
        }
        tail = new_node;
    }

    // 转置
    ListNode<int> *output = TransposeList(head, rows, cols);
    // 释放相应内存
    return 0;
}
```

输出：

```c++
input:  1,2,3,4,5,6,7,8,9,10,11,12
output: 1,4,7,10,2,5,8,11,3,6,9,12
```

### 2.1.3 知识点

- 链表的创建

  ```c++
  ListNode<int> *head = nullptr;          // 链表头节点
  ListNode<int> *tail = nullptr;          // 链表尾节点,用于在尾部插入新节点
  ListNode<int> *new_node = nullptr;      // 待插入的新节点
  int length = 9;							// 链表长度
  for (int i = 0; i < length; ++i)
  {
      new_node = new ListNode<int>(i + 1);
  
      if (head == nullptr)
      {
          head = new_node;
      }
      else
      {
          tail->next_ = new_node;
      }
      tail = new_node;
  }
  ```

- 链表操作要避免出现环

  参考：["Heap use after free" error in LeetCode Online Judge but not Visual Studio](https://stackoverflow.com/questions/65463442/heap-use-after-free-error-in-leetcode-online-judge-but-not-visual-studio);  

  例如：链表的反转；将`1,2,3`反转成`3,2,1`；

  按照前面转置的思路（其他实现思路暂不在此讨论），利用一个**栈**，遍历链表每个节点并入栈，然后将栈内每个节点依次取出，取出的同时构建新的链表，就是所求。

  很显然，这样操作完确实也能正常输出`3,2,1`，但按照上面“链表的创建”里的代码，最后节点`1`的`next_`其实非空，而是还指向节点`2`的地址；这样就形成了一个环：`3->2->1->2->1...`

  ```c++
       --<---
       ↓    ↑
  3 -> 2 -> 1
  ```

  在Visual Studio上编译运行都正常，但在Leetcode 的Online Judge上可能出现`Heap use after free`的报错。因为OJ在程序结束前会逐个节点进行delete，遇到最后一个节点`1`时，因为其`next`不为空，会delete其`next`，也就是节点`2`；但节点`2`在前面已经删除过了，因此就会报错。

  **修改**的方法：将最后一个节点的`next`置为空（`last_node->next = nullptr;`)

## 2.2 Reverse Linked List

Tags: Linked List; 

来源：[Leetcode - Reverse Linked List](https://leetcode-cn.com/problems/reverse-linked-list/); 

### 2.2.1 题目描述

Given the `head` of a singly linked list, reverse the list, and return the reversed list.

 Example 1:

![pic](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

```c++
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

Example 2:

![pic](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)

```c++
Input: head = [1,2]
Output: [2,1]
```

Example 3:

```c++
Input: head = []
Output: []
```


Constraints:

- The number of nodes in the list is the range [0, 5000].
- -5000 <= Node.val <= 5000

### 2.2.2 解法

#### 2.2.2.1 栈

利用栈的后进先出性质将单链表反转过来。

时间复杂度：O(n);

空间复杂度：O(n);

示例：

```c++
ListNode *reverseList(ListNode *head)
{
    if (head == nullptr)
    {
        return head;
    }
    std::stack<ListNode *> tools;
    ListNode *input = head;
    // 先遍历整个链表并入栈
    while (input != nullptr)
    {
        tools.push(input);
        input = input->next;
    }
    // 出栈并构建新链表
    ListNode *output = nullptr;
    ListNode *tail = nullptr;
    while (!tools.empty())
    {
        auto cur_node = tools.top();
        if (output == nullptr)
        {
            output = cur_node;
        }
        else
        {
            tail->next = cur_node;
        }
        tail = cur_node;
        tools.pop();
    }
    if (tail != nullptr)
    {
        tail->next = nullptr;
    }
    return output;
}
```

#### 2.2.2.2 原地翻转

利用3个指针，分别表示当前节点、当前节点的前驱节点和当前节点的后置节点；

- 遍历整个链表；
  - 先保存好后置节点；
  - 再把当前节点的`next`指向前驱节点；
  - 前驱节点和当前节点依次前进一步，准备下一次循环。

- 循环结束前驱节点就是反转后的头节点。

时间复杂度：O(n);

空间复杂度：O(1);

示例：

```c++
ListNode *reverseList(ListNode *head)
{
    if (head == nullptr)
    {
        return head;
    }

    ListNode *pre = nullptr;    // 当前节点的前驱节点
    ListNode *cur = head;       // 当前节点
    ListNode *post = nullptr;   // 当前节点的后置节点

    while (cur != nullptr)
    {
        // 先保存后置节点
        post = cur->next;
        // 把前驱节点设置为next
        cur->next = pre;
        // 前驱和当前节点依次后移,准备下一次循环
        pre = cur;
        cur = post;
    }
    return pre;
}
```

# 3. Array

## 3.1 Diagonal Traverse

Tags: Matrix；

来源：[Diagonal Traverse](https://leetcode-cn.com/problems/diagonal-traverse/): 

### 3.1.1 题目描述

Given an `m x n` matrix `mat`, return an array of all the elements of the array in a **diagonal order**.

 ![pic](https://assets.leetcode.com/uploads/2021/04/10/diag1-grid.jpg)

Example 1:

```c++
Input: mat = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,4,7,5,3,6,8,9]
```

Example 2:

```c++
Input: mat = [[1,2],[3,4]]
Output: [1,2,3,4]
```


Constraints:

- m == mat.length
- n == mat[i].length
- 1 <= m, n <= 104
- 1 <= m * n <= 104
- -105 <= mat[i][j] <= 105

### 3.1.2 解法

思路：依次遍历每条对角线并取出上面的所有元素。

在每条对角线上，遍历方向分为：**“右上”**和**“左下”**两种；且第**奇数**条对角线的方向是**右上**，第**偶数**条对角线的方向是**左下**。当超过边界时，则切换方向进入下一次循环。

- 对于`m*n`的矩阵，总共有`m+n-1`条对角线，因此循环`m+n-1`次；
  - 如果是**奇数**条对角线（处理的都是该对角线上的元素，包括界外和界内元素；下同）：
    - 当前位置在界外时，则向**右上**移动一步，使之进入界内；（例如：遍历完数字6和8后，当前位置位于数字8的下方(本来是8的左下方，但出界后列数+1，因此到了8的下方)，跟9位于同一对角线上）
    - 当前位置在界内时，则取出该元素并向**右上**移动一步；并重复该步骤，一直到当前位置出界为止；
    - 当前位置行数+1，切换到下一条对角线上；
  - 如果是**偶数**条对角线：
    - 当前位置在界外时，则向**左下**移动一步，使之进入界内；（例如：遍历完数字7，5和3后，当前位置位于数字3的右方(本来是3的右上方，但出界后行数+1，因此到了3的右方)，跟6，8位于同一对角线上）
    - 当前位置在界内时，则取出该元素并向**左下**移动一步；并重复该步骤，一直到当前位置出界为止；
    - 当前位置列数+1，切换到下一条对角线上；
- 返回取到的元素即可。

示例代码：

```c++
vector<int> findDiagonalOrder(vector<vector<int>>& mat) 
{
    int m = mat.size();     // 行数
    int n = mat[0].size();  // 列数
    if(m*n==0)
    {
        return {};
    }
    vector<int> result;
    // mat[i][j] 表示mat的第i行第j列的元素
    int cur_row = 0;    // 当前行数
    int cur_col = 0;    // 当前列数
    
    // 对角线总数为m+n-1，故循环这么多次,每次循环把当前对角线上的元素取出来
    for(int i=0; i< (m+n-1); ++i)
    {
        int direction = i%2;    // 对角线方向;偶数表示为"右上",奇数表示"左下"
        // 右上
        if((direction & 1) == 0)  
        {
            if((cur_row<0||cur_row>m-1||cur_col<0||cur_col>n-1)                   // 当前位置越界
            && (cur_row-1>=0&&cur_row-1<=m-1&&cur_col+1>=0&&cur_col+1<=n-1))      // 当前位置的右上方位置不越界(这个条件可以不要，因为右上方位置肯定在界内)
            {
                // 将右上方位置当作当前位置
                cur_row = cur_row - 1;
                cur_col = cur_col + 1;
            }
            // 只要当前位置不越界,就取出当前位置的值,并朝"右上"移动一步
            while(cur_row>=0&&cur_row<=m-1&&cur_col>=0&&cur_col<=n-1)
            {
                result.push_back(mat[cur_row][cur_col]);
                // 将右上方位置当作当前位置
                cur_row = cur_row - 1;
                cur_col = cur_col + 1;
            }
            cur_row++;      // 越界后下移一行
        }            
        else    // 左下
        {
            if((cur_row<0||cur_row>m-1||cur_col<0||cur_col>n-1)                  // 当前位置越界
            && (cur_row+1>=0&&cur_row+1<=m-1&&cur_col-1>=0&&cur_col-1<=n-1))      // 当前位置的左下方位置不越界(这个条件可以不要，因为左下方位置肯定在界内)
            {
                // 将左下方位置当作当前位置
                cur_row = cur_row + 1;
                cur_col = cur_col - 1;
            }
            // 只要当前位置不越界,就取出当前位置的值,并朝"左下"移动一步
            while(cur_row>=0&&cur_row<=m-1&&cur_col>=0&&cur_col<=n-1)
            {
                result.push_back(mat[cur_row][cur_col]);
                // 将左下方位置当作当前位置
                cur_row = cur_row + 1;
                cur_col = cur_col - 1;
            }
            cur_col++;      // 越界后右移一列
        }
    }
    return result;
}
```

### 3.1.3 知识点

- 没头绪的问题可以先找规律，形成一个大概的思路，再一步步细化；

## 3.2 

# 4. Tree



# 5. Database

主要记录一些数据库相关的知识点，必要时可以把题目加进来。

## 5.1 Combine Two Tables

参考：[Combine Two Tables](https://leetcode-cn.com/problems/combine-two-tables/);  [SQL-多表如何查询](https://leetcode-cn.com/problems/combine-two-tables/solution/tu-jie-sqlmian-shi-ti-duo-biao-ru-he-cha-xun-by-ho/); 

- 多表联结

  涉及到多表查询，需要用到**联结**。多表的联结分以下几种类型：

  - **left join**(左联结): 联结结果保留左表的全部数据；
  - **right join**(右联结): 联结结果保留右表的全部数据；
  - **inner join**(内联结): 取两表的公共数据；

  联结条件用**on**；

  示例：

  ```sql
  select A.firstName, A.lastName, B.city, B.state
  from Person as A left join Address as B
  on A.personId = B.personId
  ;
  ```

  

![pic](https://pic.leetcode-cn.com/ad3df1c4ecc7d2dbe85f92cdde8ec9a731fdd20dc4c5629ecb372b21de26c682-1.jpg)

## 5.2 Second Highest Salary

参考：[Second Highest Salary](https://leetcode-cn.com/problems/second-highest-salary/); [SQL-查找第N高的数据](https://leetcode-cn.com/problems/second-highest-salary/solution/tu-jie-sqlmian-shi-ti-ru-he-cha-zhao-di-ngao-de-sh/); [CSDN-SQL中limit与offset的区别](https://blog.csdn.net/cnwyt/article/details/81945663); 

思路1：利用`max(列名)` 返回该列的最大值`m`，利用`distinct`去重，那么再找小于`m`的数据中的最大值就是第二高的数据。

示例：

```sql
select max(distinct Salary) as SecondHighestSalary
from Employee
where salary < (select max(distinct Salary)
                from Employee)
                ;
```

思路2：利用`limit y`返回查询结果的前`y`条数据，利用`offset x`跳过`x`条数据，利用`ifnull(expression, value)`判空；

- `limit y`: 读取`y`条数据；
- `limit x,y`: 跳过`x`条数据，读取`y`条数据；
- `limit y offset x`: 跳过`x`条数据，读取`y`条数据；
- `ifnull(expression, value)`: 判断表达式`expression`是否为`null`，如果为`null`则返回`value`，如果不为`null`则返回`expression`的值；

示例：

```sql
select ifnull(
    (select distinct salary as SecondHighestSalary
    from Employee 
    order by salary desc
    limit 1 offset 1)
    ,null) as SecondHighestSalary
;
```

同理，如果求第`n`高的值，需要跳过`n-1`个数据，可能会用到：`SET N := N-1`; 

## 5.3 Rank Scores

要求把成绩按从大到小排列，并给出排名（存在并列排名，即并列第1时，第3个成绩的排名是2）。

参考：[Rank Scores](https://leetcode-cn.com/problems/rank-scores/); 

思路1：分成两部分，第1部分得到从大到小排的成绩，第2部分得到排名，再组合起来。

求排名时，可以把所有大于等于某一分数的所有结果进行去重后计数，就是该分数的排名。

由于每个成绩都要计算一次`count`，因此效率并不高。

示例：

```sql
select A.score as score,
    (select count(distinct B.score) from Scores B where B.score >= A.score) as 'rank'
from Scores A
order by A.score desc;
```

- 别名不能是SQL中的关键字，可以用引号括起来；（如上面的`rank`当做别名时要用加上引号，否则会报语法错误）

思路2：利用排序函数`dense_rank()`;

几个排名相关函数的区别：(都是搭配`over()`使用，`over()`里是待排名的列)

- `row_number() over( order by 列名)`: 不考虑数据的重复性，按照查询出来的顺序依次标号；（如：按成绩排序时，三个100分，则排名也是`1,2,3`；
- `rank() over( order by 列名)`: 对于数据重复的情况进行跳跃标号；（如：成绩分别是100，100，98；则排名是1，1，3；跳过了标号2）
- `dense_rank() over( order by 列名)`: 连续标号；（如：成绩分别是100，100，98；则排名是1，1，2；）

示例：

```sql
select score, dense_rank() over(order by score desc) as 'rank'
from Scores
```

