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
