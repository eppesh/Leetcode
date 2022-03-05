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

