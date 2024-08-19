# LeetCode-implemented-by-Python
根据灵神整理的基础算法题单，自己做了一遍，加上了个人理解的思路，复杂度说明（一般为最低复杂度）。

❤感谢灵神辛苦整理题单，讲授课程。

[📑灵茶山艾府题单](https://github.com/EndlessCheng/codeforces-go/blob/master/leetcode/README.md)


- [相向双指针1](#相向双指针1)
- [相向双指针2](#相向双指针2)
- [滑动窗口](#滑动窗口)
- [二分查找](#二分查找)
- [二分查找 - 变形](#二分查找 - 变形)

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
"""
思路：三角形三条边的关系，很容易看出是三数之和的变式。
1.框架。由于有效三角形的定义是任意两边之和大于第三边，但是任意是比较难处理的，
如果你任选，需要组合数为3种情况，但是如果是有序状态a<=b<=c，那么只需要
满足a+b>c一种情况即可（因为无论是a或b加上了c，都会大于另外一边，因为c是
最大的嘛，c>b,c>a）。因此我们先nums.sort(reverse=True)进行排序，reverse
代表反转，不带参数是升序排列，反转后就是降序排列。为什么需要降序排列呢，
因为假如我们升序排列，a<=b<=c，要满足a+b>c，变式为a+b-c>0，而我们一般会
固定第一个数a，双指针控制的b，c一正一负，就不好调整双指针了，因为left
右移满足>0，right左移也继续满足>0，不是我们想要的效果，我们想要的效果应该
是，控制较小的两个数，来和最大的数进行比较，因此我们采用降序排列（其实你继续
升序排列也可以，只需要从i从2开始遍历到length-1，固定最大数nums[i]就行，但
我不太喜欢）。当你采用降序排列时，a>=b>=c，我们固定a，i从0到length-1遍历，
双指针控制b，c，当b+c>a时，也说明了b，c中间的数和b一起配对也满足，因此
ans+=right-left，防止漏解，然后left+=1，当b+c<=a时，我们希望让两数之和变大，
因此我们控制right-=1，至此，完成了主体逻辑。
2.两个优化。三数之和的两个优化在这里也适用，本质上都是拿这三个数构造一个式子，
与target进行比较。
优化I.比如这里我们降序排列后，固定a，后续的式子我们取出两个最小的数min[-1]，
min[-2]，若这两个数的和大于a，说明从a到min[-1]的这个序列，a..min[-1],min[-2]
任取3个数均满足有效三角形（因为你最小的两个边大于最大边了，我把最大边a变成中间
数也满足，我把min[-1][-2]改成其他两个数也大于a，也大于其他数）。因此我们计算这
个序列的组合数C(length-i)(3)，即从length-i个数中取出3个数的组合数，然后break。
优化II.固定a，后续的式子取出两个最大数，若这两个最大数的和小于等于a，说明对于a，
在后面的序列中已经找不到两个数b，c，使得b+c>a了，因此我们continue，使a变为下一个
数，即让a变小，让他还有可能匹配。

时间复杂度：O(n^2)。固定数＋双指针共两层嵌套循环。
空间复杂度：O(1)，不算快排的话。O(logn)，算快排的话。
"""
class Solution:
    def triangleNumber(self, nums: List[int]) -> int:
        nums.sort(reverse=True)
        ans = 0
        length = len(nums)
        if length < 3:
            return 0
        for i in range(length-2):
            x = nums[i]
            # 优化1
            if nums[length-2] + nums[length-1] > x:
                ans += (length-i) * (length-i-1) * (length-i-2) // 6
                break
            # 优化2
            if nums[i+1] + nums[i+2] <= x:
                continue
            left = i + 1
            right = length - 1
            while left < right:
                s = nums[left] + nums[right]
                if s > x:
                    ans += right - left
                    left += 1
                elif s <= x:
                    right -= 1
        return ans
```

### 相向双指针2

1. [11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

```python
"""
思路：双指针。根据题目的示例1，又根据木桶效应，我们首先固定左边最长的
红边，若右边较短的红边往内靠近（替换成中间的黑边），则木桶的长度一定是
变小的，若这时高度再变小，则说明容积肯定会变小，我们的目的是在长度变小
时，尽量找到高度更高的木板，与其对冲，说不定就能找到更大的容积。所以我们
控制双指针在0，length-1的位置，先把长度置为最大，然后每次选择较矮的木板，
往内靠近。
优化。当较短木板向里靠近时，我们只需要关注高度比该木板高的中间木板，没有
必要每次靠近一块木板就计算一次area，因此我们可以先控制指针向里一位，接着
用循环找到较高的木板（注意要先移动再用while循环，不要直接while循环，否则
一开始条件不满足的话，会造成left永远不右移，死循环）。如下是左指针的优化：
left += 1
while height[left] < height[left-1] and left < right:
    left += 1

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def maxArea(self, height: List[int]) -> int:
        ans = 0
        left = 0
        right = len(height) - 1
        while left < right:
            area = (right - left) * min(height[left], height[right])
            if area > ans:
                ans = area
            if height[left] < height[right]:
                left += 1
                # 优化1
                while height[left] < height[left-1] and left < right:
                    left += 1
            else:
                right -= 1
                # 优化2
                while height[right] < height[right+1] and left < right:
                    right -= 1
        return ans
```

2. [42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

> 法一（时间复杂度：O(n)，空间复杂度：O(n)）

```python
"""
思路：
1.根据木桶效应，容纳的水量取决于短板。对于第i格，可以用两块板围成一个
木桶，前面的板为前缀最大值，后面的板为后缀最大值，取这两个值的最小值，
代表这个木桶容纳的水量，但是，这个木桶还可能包括了黑色的柱子，因此我们
还要用这个容积减去黑柱的高度，才能得到还能接的雨水量。
2.前缀最大值的计算为，pre_max[0]=height[0]，第i个的pre_max为取第i-1
个的pre_max和第i个的height做比较，取最大值。后缀最大值的计算反向进行，
suf_max[-1]=height[-1]，第i-1个suf_max为取第i个的suf_max和第i-1个的
height做比较，取最大值。
3.计算能接的雨水数。桶能装的水取决于前缀最大值和后缀最大值的最小值，
桶能装的水减去柱子的高度即height，则为能接的雨水数，可以理解为桶的
底部被柱子所占，桶还剩余的空间即为接水量。

时间复杂度：O(n)。只是对长度为n的数组遍历了常数次而已。
空间复杂度：O(n)。创建了前缀最大值数组和后缀最大值数组。
"""
class Solution:
    def trap(self, height: List[int]) -> int:
        ans = 0
        length = len(height)
        pre_max = [0] * length
        suf_max = [0] * length
        # 计算前缀最大值
        pre_max[0] = height[0]
        for i in range(1, length):
            pre_max[i] = max(pre_max[i-1], height[i])
        # 计算后缀最大值
        suf_max[-1] = height[-1]
        for i in range(length-2, -1, -1):
            suf_max[i] = max(suf_max[i+1], height[i])
        # 计算i从0到length-1的每个位置能容纳的雨水数
        for pre, suf, h in zip(pre_max, suf_max, height):
            ans += min(pre, suf) - h
        return ans
```

> 法二（时间复杂度：O(n)，空间复杂度：O(1)）

```python

```

### 滑动窗口

1. [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

```python

```

2. [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```python

```

3. [713. 乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)

```python

```

4. [2958. 最多 K 个重复元素的最长子数组](https://leetcode.cn/problems/length-of-longest-subarray-with-at-most-k-frequency/)

```python

```

5. [2730. 找到最长的半重复子字符串](https://leetcode.cn/problems/find-the-longest-semi-repetitive-substring/)

```python

```

6. [2779. 数组的最大美丽值](https://leetcode.cn/problems/maximum-beauty-of-an-array-after-applying-operation/)

```python

```

7. [1004. 最大连续 1 的个数 III](https://leetcode.cn/problems/max-consecutive-ones-iii/)

```python

```

8. [2962. 统计最大元素出现至少 K 次的子数组](https://leetcode.cn/problems/count-subarrays-where-max-element-appears-at-least-k-times/)

```python

```

9. [2302. 统计得分小于 K 的子数组数目](https://leetcode.cn/problems/count-subarrays-with-score-less-than-k/)

```python

```

10. [1658. 将 x 减到 0 的最小操作数](https://leetcode.cn/problems/minimum-operations-to-reduce-x-to-zero/)

```python

```

11. [1234. 替换子串得到平衡字符串](https://leetcode.cn/problems/replace-the-substring-for-balanced-string/)

```python

```

12. [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

```python

```

### 二分查找

### 二分查找 - 变形

### 
