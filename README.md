# LeetCode-implemented-by-Python
根据灵神整理的基础算法题单，代码加上了个人理解的思路，复杂度，用于速查

[📑灵茶山艾府题单](https://github.com/EndlessCheng/codeforces-go/blob/master/leetcode/README.md)


- [相向双指针1](#相向双指针1)
- [相向双指针2](#相向双指针2)

### 相向双指针1

1. [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

```python
"""
思路：
1.最简单的暴力法是写两层for循环，枚举组合数Cn2，这样时间复杂度是O(n^2)，
因为你没有利用到数组有序这个条件。
2.进阶做法是相向双指针，left=0，right=len(numbers)-1，计算sum1值，
这里有3种情况，等于target时说明找到答案直接返回（题目说明存在且唯一答案），
小于target时需要把左指针右移（因为假设左指针不动，右指针已经是可给的最大值了，
中间的值会更小，均不符合，因此要控制左指针的值偏大一些），同理大于target时，
要把右指针左移。

时间复杂度：O(n)。最多用双指针遍历整个数组。
空间复杂度：O(1)。只创建了常量空间。
"""
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        left = 0
        right = len(numbers) - 1
        while left < right:
            sum1 = numbers[left] + numbers[right]
            if sum1 == target:
                return [left+1, right+1]
            elif sum1 < target:
                left += 1
            elif sum1 > target:
                right -= 1
```

2. [15. 三数之和](https://leetcode.cn/problems/3sum/)

```python
"""
思路：三数之和是两数之和的进阶变式，我们需要将其转换为两数之和问题
1.两数之和是在有序条件下，所以我们直接nums.sort()进行排序。排序好后，
固定一个数x，遍历数组从0到length-3的位置（因为要留两个位置给剩余两个数），
原题为x+y+z=0，因此问题转换成y+z=-x，即两数之和的问题。需要注意的是，
两数之和求的是数组下标，而三数之和求的是数组中的元素，考虑到排序后有
相同元素并列在一起的情况，为了不使ans出现同样的元素列表，我们需要跳过
这些相同的元素（注意这里不能去重，否则无法处理[0,0,0]这样的情况），
只需要做如下处理即可
①if i > 0 and nums[i] == nums[i-1]，
②left += 1
    while left < right and nums[left] == nums[left-1]:
        left += 1
2.两个优化。I.当前遍历的列表的前三个数相加大于0时，说明已经找不到等于0的
三元组了，直接break。II.当前遍历的列表的第一个元素x和最后两个元素小于0时，
说明只有当第一个元素x变大时，才有机会出现等于0的三元组，因此直接continue。
注意这两个的区别，一个是全局无三元组，一个是双指针已经没必要进行下去。

时间复杂度：O(n^2)。快排的时间复杂度为O(nlogn),固定x遍历+双指针两层嵌套循环，
          时间复杂度为O(n^2)
空间复杂度：O(1),如果不算快排的空间复杂度的话。
          O(logn)，如果算快排的空间复杂度的话。
"""
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        ans = []
        length = len(nums)
        for i in range(0, length-2):
            x = nums[i]
            if i > 0 and nums[i] == nums[i-1]:
                continue
            # 优化处理1
            if x + nums[i+1] + nums[i+2] > 0:
                break
            # 优化处理2
            if x + nums[length-1] + nums[length-2] < 0:
                continue
            left = i + 1
            right = length - 1
            while left < right:
                s = nums[left] + nums[right]
                if s < (-1)*x:
                    left += 1
                elif s > (-1)*x:
                    right -= 1
                else:
                    ans.append([x, nums[left], nums[right]])
                    left += 1
                    while left < right and nums[left] == nums[left-1]:
                        left += 1
                    right -= 1
                    while left < right and nums[right] == nums[right+1]:
                        right -= 1
        return ans
```

3. [2824. 统计和小于目标的下标对数目](https://leetcode.cn/problems/count-pairs-whose-sum-is-less-than-target/)

```python
"""
思路：很明显的两数之和变式，只是将等于改为了小于，把有序变成了无序，
首先nums.sort()对数组排序，然后用双指针处理，值得注意的是，当满足
nums[left]+nums[right]<target时，说明这是一个结果，此时不能将
cnt自增再让left右移，因为left和right直接包围的数和left组合起来也
符合条件，如果left右移的话，就永远丢失这些解了。因此cnt要加上right-left，
再将left右移。

时间复杂度：O(nlogn)。
空间复杂度：O(1)，如果不算快排的空间复杂度的话。O(logn)，如果算的话。
"""
class Solution:
    def countPairs(self, nums: List[int], target: int) -> int:
        nums.sort()
        cnt = 0
        left = 0
        right = len(nums) - 1
        while left < right:
            s = nums[left] + nums[right]
            if s < target:
                cnt += right - left
                left += 1
            elif s >= target:
                right -= 1
        return cnt
```

4. [16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)

```python
"""
思路：三数之和的变式，在三数之和的基础上存储与target值的差值的绝对值，
在双指针内，每次尝试更新绝对值，并尽量往着target值靠即可。需要注意的是
优化，一方面双指针遍历时若相等，直接返回target，另一方面，依然可以根据
与target值进行大小比较进行两个优化，一个break一个continue，对于continue
的优化，还要记得更新记录的最小绝对值temp。

时间复杂度：O(n^2)，固定第一个数和双指针共两层嵌套循环。
空间复杂度：空间复杂度:O(1),不算快排的话。O(logn)，算快排的话。
"""
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        nums.sort()
        temp = abs(nums[0] + nums[1] + nums[2] - target)
        ans = nums[0] + nums[1] + nums[2]
        length = len(nums)
        for i in range(length-2):
            x = nums[i]
            if i > 0 and nums[i] == nums[i-1]:
                continue

            # 优化1
            s = x + nums[i+1] + nums[i+2]
            if s > target:
                if s - target < temp:
                    ans = s
                break
            # 优化2
            s = x + nums[length-2] + nums[length-1]
            if s < target:
                if target - s < temp:
                    ans = s
                    temp = target - s
                continue

            left = i + 1
            right = length - 1
            while left < right:
                s1 = nums[left] + nums[right] + x
                s2 = abs(nums[left] + nums[right] + x - target)
                if s2 < temp:
                    temp = s2
                    ans = nums[left] + nums[right] + x
                if s1 < target:
                    left += 1
                elif s1 > target:
                    right -= 1
                else:
                    return target
        return ans
```

5. [18. 四数之和](https://leetcode.cn/problems/4sum/)

```python
"""
思路：
1.实际上与三数之和相比没有质的进阶，只需要先固定第一个数，再固定第二个数，
再处理双指针即可，只需要注意各个循环的边界条件
2.对于优化，在三数之和时有两个优化，一个是计算前三个之和，若大于target则
直接break(因为双指针怎么选都会更大，从固定数开始后续的子列表已经没有意义了)，
然后是固定数加最后两个数，若小于target，双指针怎么变都是更小，我们需要考虑
的是让固定数变大，因此我们直接continue(相当于这个固定数已经没有意义了，我们
需要去找别的固定数，注意与break的区别是break直接舍弃全部，因为确实没解了，
而固定数变大还是可能有解的)

时间复杂度:O(n^3)，固定第一、二个数字，双指针遍历共嵌套3层循环。
空间复杂度:O(1),不算快排的话。O(logn)，算快排的话。
"""
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        nums.sort()
        length = len(nums)
        ans = []
        for i in range(0, length-3):
            x = nums[i]
            if i > 0 and nums[i] == nums[i-1]:
                continue
            # 固定第一个数优化1
            if x + nums[i+1] + nums[i+2] + nums[i+3] > target:
                break
            # 固定第一个数优化2
            if x + nums[length-3] + nums[length-2] + nums[length-1] < target:
                continue
            for j in range(i+1, length-2):
                if j > i+1 and nums[j] == nums[j-1]:
                    continue
                y = nums[j]
                # 固定第二个数优化1
                if x + y + nums[j+1] + nums[j+2] > target:
                    break
                # 固定第二个数优化2
                if x + y + nums[length-2] + nums[length-1] < target:
                    continue
                left = j + 1
                right = length - 1
                while left < right:
                    s = nums[left] + nums[right] + y + x
                    if s == target:
                        ans.append([x, y, nums[left], nums[right]])
                        left += 1
                        while left < right and nums[left] == nums[left-1]:
                            left += 1
                        right -= 1
                        while left < right and nums[right] == nums[right+1]:
                            right -=1
                    elif s < target:
                        left += 1
                    elif s > target:
                        right -= 1
        return ans
```

6. [611. 有效三角形的个数](https://leetcode.cn/problems/valid-triangle-number/)

```python

```

