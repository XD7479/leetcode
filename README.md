## Leet Code Record
--------
@(LeetCode)

#### 953. [Verifying an Alien Dictionary](https://leetcode-cn.com/problems/verifying-an-alien-dictionary/)
sorted:
iterable: 可迭代对象
cmp: 比较函数，可以利用自带的cmp函数
key: 比较关键字
reverse: False升序，True降序
```python
sorted(iterable, cmp=None, key=None, reverse=False)
# eg:
sorted(list, cmp = lambda x, y: cmp(x+y, -y))
sorted(list, key = lambda x: x[0], reverse = True)
```

#### 001. [Two Sum](https://leetcode-cn.com/problems/two-sum/)
涉及到 下标记录 的问题，善用字典（哈希表）
```python
# hashmap = {key: value}
# hashmap[key] = value
```

#### 1249. [Minimum Remove to Make Valid Parentheses](https://leetcode-cn.com/problems/minimum-remove-to-make-valid-parentheses/)
1. enumerate, for 比 python 内部的查找算法更费时间
2. `str.join(sequence)`: 将 sequence 中的字符以 str 连接成字符产
```python
# 字符转list:
li = list(s)
# list转字符：str.join(sequence)
s = ''.join(li)
```

#### 15. [3Sum](https://leetcode-cn.com/problems/3sum/)
2sum + target遍历 
排序 $O(NlogN)$ + 双指针 $O(N^2)$

优化：
1. if nums[i] > 0可以直接跳出循环，因为后面不会有再有三个数加起来等于0
2. 重复元素跳过，避免重复解
3. 令左指针 $L = i+1$, 右指针 $R = n-1$, while $L < R$:
	if num[i] + num[L] + num[R] == 0, 判断 L, R 附近是否有重复元素，挪动 L，R 直至下一位置，寻找新解
	if sum > 0, R--
	if sum < 0 , L++

#### 238. [Product of Array Except Self](https://leetcode-cn.com/problems/product-of-array-except-self/)
计算数组中除了第i位外所有元素的成绩
方法：分别记录 nums[0: i-1] 和 nums[i+1 :] 的乘积 L, R
优化：同时记录 L, R 并计算 res[i]
```python
out = [1for i in range(len(nums))]
left, right = 1, 1
for i,n in enumerate(nums):
    out[i] *= left
    left *= n
    out[len(nums)-i-1] *= right
    right *= nums[-i-1]
```

#### 67. [Add Binary](https://leetcode-cn.com/problems/add-binary/)
用字符串模拟二进制加法，模拟进位就可以了。注意下最后的进位要添加一位。

#### 283. [Move Zeroes](https://leetcode-cn.com/problems/move-zeroes/)
```python
offset = 0
for i in range(len(nums)):
    if nums[i] == 0:
        offset += 1
    else:
        nums[i-offset] = nums[i]

if offset:
    for i in range(offset):
        nums[-1-i] = 0
```

#### 560.  [Subarray Sum Equals K](https://leetcode-cn.com/problems/subarray-sum-equals-k/)
Given an array of integers nums and an integer k, return the total number of continuous subarrays whose sum equals to k.

```python
# 方法一：前缀和 (timeout)
res = 0
s = {-1:0}
for i, n in enumerate(nums):
    s[i] = s[i-1] + nums[i]
    if s[i] == k:
        res += 1
    for j in range(i):
        if s[i] - s[j] == k:
            res += 1
    
# 方法二：（题解）前缀和+哈希优化
res, s = 0, 0
# count 记录的是 s[i] 出现过的次数
count = {}
for i, n in enumerate(nums):
    s += n
    if s == k:
        res += 1
    # 要找 s[i] - s[j] = k, 0<j<i，只要存在一个 s[j] = s[i] - k，就存在一个 s[j: i] = k
    # 因此 count 记录的是 s[i] 出现过的次数
    if s - k in count:
        res += count[s - k]
    if s not in count:
        count[s] = 0
    count[s] += 1
return res
```

#### 125. [Valid Palindrome](https://leetcode-cn.com/problems/valid-palindrome/)
Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

验证包含标点符号的回文字符串 (palindrome)，需要忽略标点符号，只判断数字和字母
最优解：双指针
python 获取ASCii码：
```python
# 字符转数字
num = ord('A')
# 数字转字符
ch = chr(66)
```

#### 680. [Valid Palindrome II](https://leetcode-cn.com/problems/valid-palindrome-ii/)
Given a non-empty string s, you may delete at most one character. Judge whether you can make it a palindrome.

一道 easy 写了半天... 
最多可删除一个字符，使得字符串 $s$ 为回文串。这里比较巧妙的是考虑到回文串的性质，若 $s$ 为回文串，任意 $s[i: len(s)-i]$ 也是回文串。

解法：双指针
思路：双指针 l, r分别指向开头结尾位置，若 $s[l] == s[r]$，移动指针至下一位；若两数不同，则考虑是否可以删掉 $s[l],  s[r]$ 其中任意一位，即：若 $s[l+1: r+1] (不包括 r+1)$ 为回文串，或 $s[l: r] (不包括 r)$ 为回文串，则整个字符串也为回文串。
```python
l, r = 0, len(s)-1
while l < r:
    if s[l] == s[r]:
        l += 1
        r -= 1
        
    else:
        a = s[l + 1: r + 1]
        b = s[l: r]

        if a == a[::-1] or b == b[::-1]:
            return True
            
        return False
```

#### 91. [Decode Ways](https://leetcode-cn.com/problems/decode-ways/)
经典二阶DP，需要注意的是 0 不能单独解码，也不能作为前缀，判断下 0 前面如果不是1 or 2，res[i] 就没有解法。
```python
res = [0 for i in range(len(s)+1)]

  if s[0] == '0':
      return 0
  res[0], res[1] = 1, 1

  for i in range(1, len(s)):
      if s[i] != '0':
          res[i+1] = res[i]
          if s[i-1: i+1] >= "10" and s[i-1: i+1] <= "26":
              res[i+1] += res[i-1]
      else:
          if s[i-1] == '1' or s[i-1] == '2':
              res[i+1] = res[i-1]
          else:
              res[i+1] = 0
  
  return res[-1]
```


#### 621. [Task Scheduler](https://leetcode-cn.com/problems/task-scheduler/)
模拟CPU调度策略。
大佬的解题思路描述的很清晰：
1. 将任务按类型分组，正好A-Z用一个int[26]保存任务类型个数
2. 对数组进行排序，优先排列个数（count）最大的任务，如题得到的时间至少为 retCount =（count-1）* (n+1) + 1 ==> A->X->X->A->X->X->A(X为其他任务或者待命)
3. 再排序下一个任务，如果下一个任务B个数和最大任务数一致，则retCount++ ==> A->B->X->A->B->X->A->B
4. 如果空位都插满之后还有任务，那就随便在这些间隔里面插入就可以，因为间隔长度肯定会大于n，在这种情况下就是任务的总数是最小所需时间

```python
count = {}
for task in tasks:
    if task not in count:
        count[task] = 1
    else:
        count[task] += 1

count = sorted(list(count.values()), reverse=True)

minC = count[0] * (n+1) - n

i = 1
while i < len(count) and count[i] == count[i-1]:
    minC += 1
    i += 1

return max(minC, sum(count))

# 关于 dict:
# dict.items(): return all items in dict as a list of tuple, tuple 不可被赋值!
# dict.keys(), dict.values() 
```


#### 278. [First Bad Version](https://leetcode-cn.com/problems/first-bad-version/)
二分查找。

#### 253. [Meeting Rooms II](https://leetcode-cn.com/problems/meeting-rooms-ii/)
Given an array of meeting time intervals intervals where $intervals[i] = [start_i, end_i],$ return the minimum number of conference rooms required.

Example 1:
```
Input: intervals = [[0,30],[5,10],[15,20]]
Output: 2
```

- 解法一：贪心+小根堆 $O(NlogN)$
先需要对 $intervals$ 按 $start[i]$ 从小到大排列。
维护一个小根堆，堆的节点表示房间，键值是 $end[i]$。
然后对每个会议，检查堆顶元素 $heap[0]$：
1. 若堆顶会议已结束（$heap[0] <= start[i]$），则该房间空闲，将新的会议 $end[i]$ 替换堆顶。
2. 若会议未结束（$heap[0] > start[i]$），则该房间不空闲，开新房间，将新的会议 $end[i]$ 放入堆中。
```python
heap = []
max_count = 0

intervals = sorted(intervals, key = lambda x: x[0])

for inter in intervals:
    if not heap:
        heapq.heappush(heap, inter[1])
        max_count = 1
        continue
    
    if inter[0] >= heap[0]:
        heapq.heapreplace(heap, inter[1])
    else:
        heapq.heappush(heap, inter[1])

    if len(heap) > max_count:
        max_count = len(heap)

return max_count
```
- 解法二：$O(NlogN)$ 
比较巧妙。由于只需要考虑最小需要的房间数，不用考虑是哪些会议在此刻占用了房间，所以 $star[i], end[i]$ 没有必要关联。对任意 $i$, 我们只需要知道在 $start[i]$ 时有一个会议开始，$count ++;$ 在 $end[i]$ 时刻有一个会议结束，$count --;$ 就可以了。
```python
count, max_count = 0, 0
# intervals = np.array(intervals)
# starts = np.sort(intervals[:,0])
# ends = np.sort(intervals[:,1])

starts = [x[0] for x in intervals]
ends = [x[1] for x in intervals]
starts = sorted(starts)
ends = sorted(ends)

i, j = 0, 0
# while i < starts.shape[0]:
while i < len(starts):
    if starts[i] < ends[j]:
        count += 1
        if max_count < count:
            max_count = count
        i += 1
    elif starts[i] > ends[j]:
        count -= 1
        j += 1
    else:
        i, j = i+1, j+1
```

