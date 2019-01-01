---
title: LeetCode note(Python) Top Interview Questions Easy part
date: 2019-01-01 17:46:36
tags:
- LeetCode
- Python
---
## Remove Duplicates from Sorted Array

題目原文：

{% blockquote Remove Duplicates from Sorted Array https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/ LeethCode%}
Given a sorted array nums, remove the duplicates in-place such that each
element appear only once and return the new length.
Do not allocate extra space for another array, you must do this by modifying
the input array in-place with O(1) extra memory. 
{% endblockquote %} 

大意為給一個**排序過的陣列**找出重複的項目並移除，function必須**回傳陣列的長度**。

條件為**不再分配空間給其他陣列**，直接**修改傳入的陣列**。 

範例輸出：

```javascript
[0,0,0,1,2,2,3,3,4] => [0,1,2,3,4]
[1,1,1,1] => [1]
```

整理出以下流程：

1. 找出重複的項目並刪除
2. 回傳修改過的陣列長度

<br/>

移除項目有三種方式, 詳細請參考 [Python 官方文件](https://docs.python.org/3/)

* [5.1. More on Lists](https://docs.python.org/3.6/tutorial/datastructures.html#more-on-lists)

* [7.5. The del statement](http://docs.python.org/reference/simple_stmts.html#the-del-statement)


```python
del list[index]
```
```python
# 移除第一個值為x的項目
list.remove(x)
```
```python
# 移除該索引值項目並回傳
list.pop(index)
```
確認目標後，很直觀的用了以下程式碼：

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        for num in nums:
            while nums.count(num)>1:
                nums.remove(num)
        return len(nums)
        
# Runtime: Time Limit Exceeded
```
短陣列沒什麼問題，但是在長陣列的時候效率低下得到`Time Limit Exceeded`錯誤。
主要原因是[list.count(x)](https://github.com/python/cpython/blob/d8dcd57edb88ce57063e5c2b85fe0ee1abb1ce8b/Objects/listobject.c#L2511)會再把整個陣列走一遍。

因此必須調整思維，重新檢視題目後可以發現其中一個重點：**排序過的陣列**

也就是重複的值必然排列再一起，只需要比對後一個的值是不是相同，如果相同就將[i]的項目刪除，不相同就往後移動。

可以想像成左腳右腳各對應一個物品，相同的話左腳就踢掉那個物品，不相同就往下個位置移動。

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 空陣列直接回傳0
        if not nums:
            return 0
        i = 0 #index計數
        while i < len(nums)-1:
            if nums[i] == nums[i+1]:
                del nums[i]
                #nums.pop(i)
            else:
                i+=1
        return len(nums)
        
# Runtime: 80ms
```
從輸出的結果可以知道，希望得出的東西為：**一個陣列裡有什麼東西的目錄**

討論區看到一個方法效率更好，蠻有趣的思維。
他並沒有實際去將重複項目去除，而是將修改原始陣列當作目錄來記錄。

從 playground 的原始碼可以看出來，輸出結果是依賴回傳值的陣列長度將修改過後的陣列印出來。

```python
def stringToIntegerList(input):
    return json.loads(input)

def integerListToString(nums, len_of_list=None):
    if not len_of_list:
        len_of_list = len(nums)
    return json.dumps(nums[:len_of_list])

def main():
    import sys
    def readlines():
        for line in sys.stdin:
            yield line.strip('\n')

    lines = readlines()
    while True:
        try:
            line = next(lines)
            nums = stringToIntegerList(line);
            
            ret = Solution().removeDuplicates(nums)

            out = integerListToString(nums, len_of_list=ret)
            print(out)
        except StopIteration:
            break

if __name__ == '__main__':
    main()
```
[i]會把陣列走一遍，而 count 會從[0]開始，當[i]遇到新東西[count]就往後移動並記錄新東西。
回傳值為被修改過的項目的長度。( [0]到[count]被修改，長度為 count+1 )

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        count = 0 #計數器
        for i in range(len(nums)):
            if nums[i] != nums[count]:
                count += 1
                nums[count] = nums[i]
        return count + 1
        
# Runtime: 60ms
```
接下來這段為利用 [set(iterable)](https://docs.python.org/3.6/library/stdtypes.html?highlight=set#set)

set 為元素不重複的集合，將原陣列轉為 set 後用 [sorted(iterable)](https://docs.python.org/3.6/library/functions.html?highlight=built#sorted) 回傳排序後的陣列。
可以參考這篇 [options to clone or copy a list on python](https://stackoverflow.com/questions/2612802/how-to-clone-or-copy-a-list/26562235#26562235)。

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        nums[:]=sorted(set(nums))
        return len(nums)

# Runtime: 60ms
```
---
## Refrence
* [Python 3.6.5 documention](https://docs.python.org/3/)
* [options to clone or copy a list on python](https://stackoverflow.com/questions/2612802/how-to-clone-or-copy-a-list/26562235#26562235)
* [https://groups.google.com/forum/#!topic/comp.lang.python/4-nf21y-M8g](https://groups.google.com/forum/#!topic/comp.lang.python/4-nf21y-M8g)

Contact me at {% link GitHub https://github.com/EltonChou %}