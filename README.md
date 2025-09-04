# LeetCode-implemented-by-Python
根据灵神整理的基础算法题单，自己做了一遍，加上了个人理解的思路，复杂度说明（一般为最低复杂度）。

❤感谢灵神辛苦整理题单，讲授课程。

[📑灵茶山艾府题单](https://github.com/EndlessCheng/codeforces-go/blob/master/leetcode/README.md)


- [相向双指针1](#相向双指针1)
- [相向双指针2](#相向双指针2)
- [滑动窗口](#滑动窗口)
- [二分查找](#二分查找)
- [二分查找-变形](#二分查找-变形)
- [链表-反转系列](#链表-反转系列)
- [链表-快慢指针](#链表-快慢指针)
- [链表-删除系列](#链表-删除系列)
- [二叉树与递归-深入理解](#二叉树与递归-深入理解)
- [二叉树与递归-灵活运用](#二叉树与递归-灵活运用)
- [二叉树与递归-前序-中序-后序](#二叉树与递归-前序-中序-后序)
- [二叉树与递归-最近公共祖先](#二叉树与递归-最近公共祖先)
- [二叉树-BFS](#二叉树-BFS)
- [回溯-子集型](#回溯-子集型)
- [回溯-组合型与剪枝](#回溯-组合型与剪枝)
- [回溯-排列型](#回溯-排列型)
- [动态规划-从记忆化搜索到递推](#动态规划-从记忆化搜索到递推)
- [0-1-背包-完全背包-至多-恰好-至少](#0-1-背包-完全背包-至多-恰好-至少)
- [最长公共子序列-LCS](#最长公共子序列-LCS)
- [最长递增子序列-LIS](#最长递增子序列-LIS)
- [状态机-DP-买卖股票系列](#状态机-DP-买卖股票系列)
- [区间-DP](#区间-DP)
- [树形-DP-直径系列](#树形-DP-直径系列)
- [树形-DP-最大独立集](#树形-DP-最大独立集)
- [树形-DP-最小支配集](#树形-DP-最小支配集)
- [单调栈](#单调栈)
- [单调队列](#单调队列)

### 相向双指针1

1. [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

```python
"""
思路：
1.最简单的暴力法是写两层for循环，枚举组合数Cn2，这样时间复杂度是O(n^2)，
但这样你就没有利用到数组有序这个条件。
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
"""
思路：为了将空间复杂度从O(n)优化成O(1)，必须利用一些其他的性质。计算接水量的
核心步骤依然是，桶的容积减去柱子高度，而桶容积取决于前缀最大值和后缀最大值中的
最小值。之前我们是用两个数组来保存前缀最大值和后缀最大值，最后再遍历一次计算。
因此我们要优化这一部分，用两个变量来储存，达到一边储存，一边计算的效果，而不是
先储存完，最后计算。
因为前缀最大值由前往后是非递减的，后缀最大值由后往前也是非递减的，之前的计算是
我们遍历i，计算第i个的min(pre_max[i], suf_max[i])-height[i]，但是事实上
我们不需要等到pre_max[i], suf_max[i]都知道后才能计算出min，比如当我们计算
第5个的min时（假设长度很长，比如20），如果我们已经知道pre_max[5]=2，
而suf_max[-2]=7，根据非递减性质，那么suf_max[5]的值肯定是大于pre_max[5]的，
那么我们可以直接知道min值为pre_max[5]。利用这个性质，我们可以初始化left，
right在左右两边，每次先计算当前的前后缀最大值，直接拿这两个前后缀最大值进行比较，
再移动left或right，我们每次必定可以在左或右其中一个方向计算出一个min，从而计算
出该位置能接的雨水数，最后把这些雨水数累加即可。

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def trap(self, height: List[int]) -> int:
        ans = 0
        left = 0
        right = len(height) - 1
        pre_max = 0
        suf_max = 0
        while left <= right:
            pre_max = max(pre_max, height[left])
            suf_max = max(suf_max, height[right])
            if pre_max < suf_max:
                ans += pre_max - height[left]
                left += 1
            else:
                ans += suf_max - height[right]
                right -= 1
        return ans
```

### 滑动窗口

> 与相向双指针对应，其实滑动窗口可以理解为同向双指针

1. [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

```python
"""
思路：滑动窗口思想，核心思想是当序列和满足>=target时，要将left右移，直到不满足>=target，
当不满足时，要将right右移直到满足>=target

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        ans = len(nums) + 1
        left = 0
        s = 0
        # 写法一
        for right, x in enumerate(nums):
            s += x
            while s >= target:
                ans = min(ans, right - left + 1)
                s -= nums[left]
                left += 1

        # # 写法二
        # for right, x in enumerate(nums):
        #     s += x
        #     while s - nums[left] >= target:
        #         s -= nums[left]
        #         left += 1
        #     if s >= target:
        #         ans = min(ans, right - left + 1)

        # # 写法三
        # right = 0
        # while left <= right and right <= len(nums):
        #     if s < target and right < len(nums):
        #         s += nums[right]
        #         right += 1
        #     elif s < target and right == len(nums):
        #         break
        #     else:
        #         ans = min(ans, right - left)
        #         s -= nums[left]
        #         left += 1

        if ans == len(nums) + 1:
            return 0
        else:
            return ans
```

2. [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```python
"""
思路：
考虑一般化的情况，当已有一个无重复子串时，在其后面添加一个字符ch，他可能会导致不满足无重复了，
而且一定是由ch这个字符的重复导致的问题，那么当遇到这种情况时，我们需要一直右移left，直到
ch这个字符的出现次数仅为1，而当我们正常添加了一个无重复的字符时，则计算其长度并与ans比较，
right右移，这里用遍历，就相当于right一直在右移了。

时间复杂度：O(n)。左右指针最多各移动n次。
空间复杂度：O(1)。尽管使用了哈希表，但是这题的字符个数是有限的。
"""
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        ans = 0
        left = 0
        cnt = dict()
        for right, ch in enumerate(s):
            if ch not in cnt:
                cnt[ch] = 1
            else:
                cnt[ch] += 1
            while cnt[ch] > 1:
                cnt[s[left]] -= 1
                left += 1
            ans = max(ans, right - left + 1)
        return ans
```

3. [713. 乘积小于 K 的子数组](https://leetcode.cn/problems/subarray-product-less-than-k/)

```python
"""
思路：
滑动窗口思想，需要注意计算ans时不是加1，而是加上right-left+1。为什么是r-l+1呢？
假如此时序列为l...temp...r，此时满足这些数的乘积小于k，注意此时右端点r是固定的，
题目要求连续子数组，则[l,r],[l+1,r],[l+2,r],[r,r](即[r]本身)都是满足的，
共有r-l+1个。

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        if k <= 1:
            return 0
        ans = 0
        left = 0
        # 乘积的英文为product，商的英文为quotient
        product = 1
        for right, x in enumerate(nums):
            product *= x
            while product >= k:
                product /= nums[left]
                left += 1
            if product < k:
                # 最容易错的一步，不是加1，因为乘数越少肯定越“小”
                ans += right - left + 1
        return ans
```

4. [2958. 最多 K 个重复元素的最长子数组](https://leetcode.cn/problems/length-of-longest-subarray-with-at-most-k-frequency/)

```python
"""
思路：
考虑一般化的情况，假设给一个序列后面新增一个数字x，若引起大于k的条件，则一定是这个
数字x的数量大于k了，因此我们首先新增这个数x，方法是在哈希表中将这个key的value加1，
然后做一个循环，不断右移left直到保证这个序列是符合条件的，然后取最大值即可。

时间复杂度：O(n)。左右指针最多各移动n次。
空间复杂度：O(n)。与之前滑动窗口第2题不同，这里的哈希表可能的数量为n，而不是有限。
"""
class Solution:
    def maxSubarrayLength(self, nums: List[int], k: int) -> int:
        ans = 0
        left = 0
        cnt = dict()
        for right, x in enumerate(nums):
            if x not in cnt:
                cnt[x] = 1
            else:
                cnt[x] += 1
            while cnt[x] > k:
                cnt[nums[left]] -= 1
                left += 1
            ans = max(ans, right - left + 1)
        return ans
```

5. [2730. 找到最长的半重复子字符串](https://leetcode.cn/problems/find-the-longest-semi-repetitive-substring/)

```python
"""
思路：
考虑破坏掉半重复性质的条件，一定是新加入的字符ch，与他前一个的字符s[right-1]相等。导致有两对相邻
字符是相等的，因此当遇到这种情况时，我们不断右移left，直到s[left]==s[left-1]，即减少了一对相等的
相邻字符，让相等的相邻字符数量为1，然后计算max即可。

时间复杂度：O(n)。左右指针最多各移动n次。
空间复杂度：O(1)。只用了常量的辅助空间。
"""
class Solution:
    def longestSemiRepetitiveSubstring(self, s: str) -> int:
        ans = 0
        cnt = 0
        left = 0
        for right, ch in enumerate(s):
            if right == 0:
                ans = max(ans, right - left + 1)
            else:
                if ch == s[right-1]:
                    cnt += 1
            while cnt > 1:
                left += 1
                if s[left] == s[left-1]:
                    cnt -= 1
                    break
            ans = max(ans, right - left + 1)

        return ans
```

6. [2779. 数组的最大美丽值](https://leetcode.cn/problems/maximum-beauty-of-an-array-after-applying-operation/)

```python
"""
思路：
问题的关键在于首先要理解，对于数组的每个数x，可以变为[x-k,x+k]的任意一个数。
并且问题的长度的判定，仅仅是相同数的个数，没有连续的要求。因此我们首先对数组排序，
对于每个数x，实际上相当于[x-k,x+k]，那么这个美丽值，其实就是这些区间的最大相交数。
对于两个区间（用数x，y来代替）是否相交，只需要x+k>=y-k即可，而在他们之间的这些区间，
肯定也是与这两个区间相交的。上述式子可以改写成y-x<=2k，也就是满足这个条件时，美丽值就是
right-left+1。因此我们写个循环，控制每次都满足该式子，然后计算最大ans即可。
注意left越向右，肯定是越满足这个式子的，而right越向右，肯定是越不满足这个式子的。

时间复杂度：O(nlogn)。用了快排。
空间复杂度：O(logn)。算快排的空间复杂度的话。O(1)，不算快排的空间复杂度的话。
"""
class Solution:
    def maximumBeauty(self, nums: List[int], k: int) -> int:
        ans = 0
        left = 0
        nums.sort()

        for right, x in enumerate(nums):
            while x - nums[left] > 2 * k:
                left += 1
            ans = max(ans, right - left + 1)
        return ans
```

7. [1004. 最大连续 1 的个数 III](https://leetcode.cn/problems/max-consecutive-ones-iii/)

```python
"""
思路：乍一看可能和滑动窗口无关，这是因为顺着题目的思路导致的。换一个角度想，既然
我们有能将k个0变成1的能力，统计的是最长连续1的序列的长度，那么我们可以想成在一个
滑动窗口中，最多出现k个0，这个滑动窗口的最大长度就是我们要求的答案。因为这个滑动
窗口中的0我们都能变成1，那么整个滑动窗口就都是1了。

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def longestOnes(self, nums: List[int], k: int) -> int:
        ans = 0
        cnt0 = 0
        left = 0
        for right, x in enumerate(nums):
            if x == 0:
                cnt0 += 1
            if cnt0 > k:
                if nums[left] == 0:
                    cnt0 -= 1
                left += 1
            ans = max(ans, right - left + 1)
        return ans
```

8. [2962. 统计最大元素出现至少 K 次的子数组](https://leetcode.cn/problems/count-subarrays-where-max-element-appears-at-least-k-times/)

```python
"""
思路：首先明确，题目中的子数组指的是连续的元素序列。比较能容易想到这题要用滑动窗口
的思想，但是对于计算子数组的数量，比较难以确定，这里思路是，新增right后，判断是否
等于k个，若等于，则ans+=len-right，然后右移left直到小于k，即等于k-1个。这样
处理的原因是，若子序列[a...b...c...d]中[a...b...c]满足条件，则d-c+1个子数组
均满足条件，包括[a...b...c,c+1]，[a...b...c,c+1,c+2]，...[a...b...c...d]。

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def countSubarrays(self, nums: List[int], k: int) -> int:
        ans = 0
        left = 0
        max_element = max(nums)
        cnt = 0
        for right, x in enumerate(nums):
            if x == max_element:
                cnt += 1
            while cnt == k:
                ans += len(nums) - right
                if nums[left] == max_element:
                    cnt -= 1
                left += 1
        return ans
```

9. [2302. 统计得分小于 K 的子数组数目](https://leetcode.cn/problems/count-subarrays-with-score-less-than-k/)

```python
"""
思路：只能说观察出是滑动窗口方法，然后猜测数学原理

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def countSubarrays(self, nums: List[int], k: int) -> int:
        ans = 0
        left = 0
        s = 0
        for right, x in enumerate(nums):
            s += x
            while s * (right - left + 1) >= k:
                s -= nums[left]
                left += 1
            ans += right - left + 1
        return ans
```

10. [1658. 将 x 减到 0 的最小操作数](https://leetcode.cn/problems/minimum-operations-to-reduce-x-to-zero/)

```python
"""
思路：题目想问的就是取一个前缀和一段后缀，他们两个的和加起来等于x。
因此我们可以转换思路，因为中间的序列是连续的，我们可以把中间的序列
看成一个滑动窗口，当其和等于sum(nums)-x时，满足题意。需要注意的是
当数组和小于x时，要处理这个特殊情况，直接返回-1，否则后面会死循环。

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def minOperations(self, nums: List[int], x: int) -> int:
        ans = len(nums) + 1
        left = 0
        if sum(nums) < x:
            return -1
        s1 = 0
        s2 = sum(nums) - x
        for right, x in enumerate(nums):
            s1 += x
            while s1 > s2:
                s1 -= nums[left]
                left += 1
            if s1 == s2:
                ans = min(ans, len(nums) - (right - left + 1))
        if ans == len(nums) + 1:
            return -1
        else:
            return ans
```

11. [1234. 替换子串得到平衡字符串](https://leetcode.cn/problems/replace-the-substring-for-balanced-string/)

```python
"""
思路：首先明确这个字符串只含有四种字符，其次他的长度一定是4的倍数。对于这个字符串，
我们分成两部分，一部分取其中的连续子串s1，剩下一部分为s2（注意这部分不一定连续）。
若s1以外的部分，他其中的一种字符数量已经超过了n/4，那么无论我们怎么替换子串s1，
这个字符的数量都是大于n/4的，因此不满足题意，若以外的部分中的每一种字符的数量都
小于等于n/4，并且联系已知这个字符串长度一定是4的倍数，那么我们可以通过替换子串s1，
来达到满足题意的效果。使用哈希表记录s1以外部分，每种字符的出现次数，先判断初始特殊情况，
然后遍历右指针right，其中再不断右移left，进行求解。


时间复杂度：O(n)。
空间复杂度：O(1)，因为只有4种字符，使用的哈希表空间为常量。
"""
import numpy as np
class Solution:
    def balancedString(self, s: str) -> int:
        cnt = dict()
        for ch in s:
            if ch not in cnt:
                cnt[ch] = 1
            else:
                cnt[ch] += 1
        m = len(s) // 4
        if len(cnt) == 4 and min(cnt.values()) == m:
            return 0
        ans = np.inf
        left = 0
        for right, ch in enumerate(s):
            cnt[ch] -= 1
            while max(cnt.values()) <= m:
                ans = min(ans, right - left + 1)
                cnt[s[left]] += 1
                left += 1
        return ans
```

12. [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

```python
"""
思路:
求最小覆盖子串,字串连续,由此想到滑动窗口思路。首先要能够统计t字符串中各字符的出现次数,
可以用collections库中的Counter数据结构来做这个事情,会返回一个字典,键为元素,值为数量。
初始化ans_left和ans_right在s之外,最终用于标记子串的开始和结尾,然后开始滑动窗口,初始
化left=0,用for循环遍历right,不断增加cnt_s,如果cnt_s>=cnt_t,即"涵盖",则更新ans,并不
断将left右移,直到破坏条件。最终根据ans返回子串。
时间复杂度:O(|Σ|m+n),其中|Σ|是字符集的大小,这里为52
空间复杂度:O(|Σ|)

优化:本题的进阶是要实现时间复杂度为O(m+n)的算法,对于之前的操作,引入了|Σ|的原因是判断
涵盖时需要用到哈希表的比较,因此要考虑一个更好的判断条件来优化。考虑用一个变量less代表
有cnt_s中有less种字符小于cnt_t中的数目,进一步的,只需要cnt_t一个哈希表判断即可,当其中
的元素的值均<=0时,则满足。需要注意,cnt_t即使引入了t中不存在的种类的字符,也不影响结果,
因为less+=1的条件是cnt_t[s[left]]原先为0,而如果引入不存在的种类的字符,less+=1时涉及
不存在的种类字符时,其不可能为0,只会为负数,因此对结果没有影响。
时间复杂度:O(m+n),其中|Σ|是字符集的大小,这里为52
空间复杂度:O(|Σ|)
"""
from collections import Counter
# @lc code=start
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        # 优化前算法
        # cnt_s = Counter()
        # cnt_t = Counter(t)
        # ans_left = -1
        # ans_right = len(s)
        # left = 0
        # for right, c in enumerate(s):
        #     cnt_s[c] += 1
        #     while cnt_s >= cnt_t:
        #         if right - left < ans_right - ans_left:
        #             ans_left = left
        #             ans_right = right
        #         cnt_s[s[left]] -= 1
        #         left += 1
        # if ans_left < 0:
        #     return ""
        # else:
        #     return s[ans_left:ans_right+1]

        # 优化后算法
        cnt_t = Counter(t)
        less = len(cnt_t)
        ans_left = -1
        ans_right = len(s)
        left = 0
        for right, c in enumerate(s):
            cnt_t[c] -= 1
            if cnt_t[c] == 0:
                less -= 1
            while less == 0:
                if right - left < ans_right - ans_left:
                    ans_left = left
                    ans_right = right
                x = s[left]
                if cnt_t[x] == 0:
                    less += 1
                cnt_t[x] += 1
                left += 1
        if ans_left < 0:
            return ""
        else:
            return s[ans_left:ans_right+1]
```

### 二分查找

1.[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

```python
"""
思路：
首先要实现一个函数lower_bound，他能够返回使得nums[i]>=k的最小的i，
如果都不满足，则返回数组长度。
无论是求开始位置还是结束位置，都可以用lower_bound解决，当求开始位置时，只需要
设置k为target本身，当求结束位置时，设置k为target+1，求得的值再减1即为结束位置。

时间复杂度：O(logn)
空间复杂度：O(1)
"""
def lower_bound(nums: List[int], target: int) -> int:
    left = 0
    right = len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return left

class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        start = lower_bound(nums, target)
        if start == len(nums) or nums[start] != target:
            return [-1, -1]
        end = lower_bound(nums, target + 1) - 1
        return [start, end]
```

2.[2529. 正整数和负整数的最大计数](https://leetcode.cn/problems/maximum-count-of-positive-integer-and-negative-integer/)

```python
"""
思路：求负整数的个数，就将lower_bound中的target设为0，即为答案
求正整数的个数，就将target设为1，再用len(nums)减去这个数即可。

时间复杂度：O(logn)
空间复杂度：O(1)
"""
def lower_bound(nums: List[int], target) -> int:
    left = 0
    right = len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return left

class Solution:
    def maximumCount(self, nums: List[int]) -> int:
        positive = lower_bound(nums, 0)
        negative = len(nums) - lower_bound(nums, 1)
        return max(positive, negative)
```

3.[2300. 咒语和药水的成功对数](https://leetcode.cn/problems/successful-pairs-of-spells-and-potions/)

```python
"""
思路：最普通的方法是遍历这两个数组，这样时间复杂度是O(mn)。对于spells[i]，
若能与potions[j]进行配对，则他们两个相乘要大于等于success，也就是相当于，
对于potions里的每个元素，只要满足大于等于success//spells[i]的向上取整的值，
就可以与spells[i]进行配对，因此我们可以先将potions进行排序，然后再用二分查找。

时间复杂度：O(nlogn+mlogn)即O((n+m)logn)。其中m为spells长度，n为potions长度。快排为O(nlogn)，
之后对spells遍历，外层为m，内层对potions进行二分，为logn。
空间复杂度：O(logn)。快排空间复杂度为O(logn)。
"""
def lower_bound(nums: List[int], target: int) -> int:
    left = 0
    right = len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return left

class Solution:
    def successfulPairs(self, spells: List[int], potions: List[int], success: int) -> List[int]:
        ans = []
        potions.sort()
        for i in spells:
            x = (success - 1) // i + 1
            start = lower_bound(potions, x)
            if start == len(potions):
                ans.append(0)
            else:
                ans.append(len(potions) - start)
        return ans
```

4.[2563. 统计公平数对的数目](https://leetcode.cn/problems/count-the-number-of-fair-pairs/)

```python
"""
思路：这题无非就是先对数组排序，然后遍历这个数组，固定一个数，对他之后的数组进行
二分查找，但是对于二分查找的实现方式，与前面几题不同，需要做一下重构。因为如果新建
一个数组nums2=nums[index+1:len(nums)]，需要注意这是一个O(n)的复制，外层遍历
叠加起来，时间复杂度就成了O(n^2)。因此不妨在二分查找算法上，传入一个start和end，
就省去了复制数组的时间，使得时间复杂度为O(nlogn)

时间复杂度：O(nlogn)
空间复杂度：O(logn)
"""
def lower_bound(nums: List[int], target: int, start, end):
    left = start
    right = end
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return left

class Solution:
    def countFairPairs(self, nums: List[int], lower: int, upper: int) -> int:
        nums.sort()
        ans = 0
        for index, value in enumerate(nums):
            if index == len(nums) - 1:
                break
            start1 = lower_bound(nums, lower - value, index+1, len(nums) - 1)
            if start1 == len(nums):
                continue
            start2 = lower_bound(nums, upper - value + 1, index+1, len(nums) - 1)
            ans += start2 - start1
        return ans
```

5.[2080. 区间内查询数字的频率](https://leetcode.cn/problems/range-frequency-queries/)

```python
"""
思路：最简单的做法是暴力，求频率就需要O(n)的时间复杂度，为了优化这个复杂度，
我们需要使用二分查找。用一个哈希表，将每个值的下标列表存起来，此时这个列表
满足非递减的性质，此时再对这个列表进行二分查找，即可得到答案。

时间复杂度：初始化需要O(n)，求频率需要O(logn)
空间复杂度：哈希表需要O(n)
"""
from typing import List
from collections import defaultdict
def lower_bound(nums: List[int], target) -> int:
    left = 0
    right = len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return left

class RangeFreqQuery:
    def __init__(self, arr: List[int]):
        # 和dict的区别是，defaultdict需要从collections导入，当访问不存在
        # 的键时，会自动创建一个默认的类型，如果用dict，需要用not in做几次判断处理
        index_map = defaultdict(list)
        for index, value in enumerate(arr):
            index_map[value].append(index)
        self.index_map = index_map

    def query(self, left: int, right: int, value: int) -> int:
        start = lower_bound(self.index_map[value], left)
        if start == len(self.index_map[value]):
            return 0
        end = lower_bound(self.index_map[value], right+1)
        return end - start
```

6.[275. H 指数 II](https://leetcode.cn/problems/h-index-ii/)

```python
"""
思路：这题为了要实现O(logn)的时间复杂度，首先不能陷入题目的思路，如果按照
题目的思路，先判断h指数可否为1，可否为2...可否为n，那时间复杂度一定是O(n)。
要转换思路，二分查找，直接判断中间(left+right)/2可否成立，因为若a<b，当h
指数可为b时，一定也可为a。然后就是如何判断h指数可否成立的问题，比如对于一个列表
[1,2,3,4,5,6]，我们想判断h指数可否为3，也就是有没有3个数大于等于3，也就是说
我们可以直接取倒数第3个数，若这个数大于等于3，又根据单调性，肯定能有至少3个数
大于等于3，那我们进行抽象，h指数可否为mid，就直接判断citations[-mid]是否>=
mid即可，还要注意这里不是返回left，而是返回right，因为当left==right时进行最后
一次二分判断时，若不满足，是将right=mid-1，也就是这个mid不满足，而这个right会
满足，这时候也同时跳出了循环。
[注]:对于取倒数第mid个数citations[-mid]，如果改写成citations[len(citations)-mid]，
会有错误，因为对于特殊列表[0]，mid=0时，会出现错误，其实循环也可以直接从1到len(citations)
开始，因为h指数必可为0，这样无论用哪种写法也不会产生这个错误，因为问题关键主要是，取倒数第0个
数这种说法，本身就不太合理。

时间复杂度：O(logn)
空间复杂度：O(1)
"""
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        left = 0
        right = len(citations)
        while left <= right:
            mid = (left + right) // 2
            # if citations[len(citations) - mid] >= mid:
            #     left = mid + 1
            if citations[-mid] >= mid:
                left = mid + 1
            else:
                right = mid - 1
        return right
```

7.[875. 爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)

```python

```

8.[2187. 完成旅途的最少时间](https://leetcode.cn/problems/minimum-time-to-complete-trips/)

```python

```

9.[2861. 最大合金数](https://leetcode.cn/problems/maximum-number-of-alloys/)

```python

```

10.[2439. 最小化数组中的最大值](https://leetcode.cn/problems/minimize-maximum-of-array/)

```python

```

11.[2517. 礼盒的最大甜蜜度](https://leetcode.cn/problems/maximum-tastiness-of-candy-basket/)

```python

```

### 二分查找-变形

1.[162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/)

```python

```

2.[153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

```python

```

3.[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

```python

```

4.[1901. 寻找峰值 II](https://leetcode.cn/problems/find-a-peak-element-ii/)

```python

```

5.[154. 寻找旋转排序数组中的最小值 II](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)

```python

```

### 链表-反转系列

1.[206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/)

```python

```

2.[92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/)

```python

```

3.[25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

```python

```

4.[24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

```python

```

5.[445. 两数相加 II](https://leetcode.cn/problems/add-two-numbers-ii/)

```python

```

6.[2816. 翻倍以链表形式表示的数字](https://leetcode.cn/problems/double-a-number-represented-as-a-linked-list/)

```python

```

### 链表-快慢指针

1.[876. 链表的中间结点](https://leetcode.cn/problems/middle-of-the-linked-list/)

```python

```

2.[141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

```python

```

3.[142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

```python

```

4.[143. 重排链表](https://leetcode.cn/problems/reorder-list/)

```python

```

### 链表-删除系列

1.[237. 删除链表中的节点](https://leetcode.cn/problems/delete-node-in-a-linked-list/)

```python

```

2.[19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

```python

```

3.[83. 删除排序链表中的重复元素](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/)

```python

```

4.[82. 删除排序链表中的重复元素 II](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)

```python

```

5.[203. 移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/)

```python

```

6.[3217. 从链表中移除在数组中存在的节点](https://leetcode.cn/problems/delete-nodes-from-linked-list-present-in-array/)

```python

```

7.[2487. 从链表中移除节点](https://leetcode.cn/problems/remove-nodes-from-linked-list/)

```python

```

### 二叉树与递归-深入理解

1.[104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

```python
"""
思路:
法一:
求二叉树的最大深度,使用递归是最简单的,按先序遍历(根-左-右),先写终止条件,
即为空叶结点时,返回0,然后求其左子树深度,再求其右子树深度,最后取其最大值+1即可。
另外,需要熟记二叉树的类定义以及根据列表创建二叉数的函数,已附在Solution中。

法二:
采用深度优先搜索遍历模式,创建dfs函数,入参为结点及深度,两种方法复杂度均相同

时间复杂度:O(n)
空间复杂度:O(n),其空间复杂度为二叉树的深度(递归调用时栈的大小),最差情况下二叉树为一条链
"""
from typing import Optional, List
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
        
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # 法一
        # if root == None:
        #     return 0
        # leftDepth = self.maxDepth(root.left)
        # rightDepth = self.maxDepth(root.right)
        # return max(leftDepth, rightDepth) + 1

        # 法二
        ans = 0
        def dfs(node: Optional[TreeNode], depth):
            if node is None:
                return
            depth += 1
            nonlocal ans
            ans = max(ans, depth)
            dfs(node.left, depth)
            dfs(node.right, depth)
        dfs(root, 0)
        return ans

    def create_binary_tree(self, nums: List[Optional[int]]) -> Optional[TreeNode]:
        if nums is None or len(nums) == 0:
            return None
        
        root = TreeNode(nums[0])
        queue = deque()
        queue.append(root)
        i = 1
        while queue and i < len(nums):
            temp = queue.popleft()
            if i < len(nums) and nums[i] != None:
                temp.left = TreeNode(nums[i])
                queue.append(temp.left)
            i += 1
            if i < len(nums) and nums[i] != None:
                temp.right = TreeNode(nums[i])
                queue.append(temp.right)
            i += 1
        return root

def main():
    solution = Solution()
    nums = [3, 9, 20, None, None, 15, 7]
    root = solution.create_binary_tree(nums)

    print(f'Binary Tree Max Depth:{solution.maxDepth(root)}')

if __name__ == '__main__':
    main()
```

2.[111. 二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

```python

```

3.[112. 路径总和](https://leetcode.cn/problems/path-sum/)

```python

```

4.[129. 求根节点到叶节点数字之和](https://leetcode.cn/problems/sum-root-to-leaf-numbers/)

```python

```

5.[1448. 统计二叉树中好节点的数目](https://leetcode.cn/problems/count-good-nodes-in-binary-tree/)

```python

```

6.[987. 二叉树的垂序遍历](https://leetcode.cn/problems/vertical-order-traversal-of-a-binary-tree/)

```python

```

### 二叉树与递归-灵活运用

1.[100. 相同的树](https://leetcode.cn/problems/same-tree/)

```python

```

2.[101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

```python

```

3.[110. 平衡二叉树](https://leetcode.cn/problems/balanced-binary-tree/)

```python

```

4.[199. 二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/)

```python

```

5.[226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

```python

```

6.[617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/)

```python

```

7.[1026. 节点与其祖先之间的最大差值](https://leetcode.cn/problems/maximum-difference-between-node-and-ancestor/)

```python

```

8.[1080. 根到叶路径上的不足节点](https://leetcode.cn/problems/insufficient-nodes-in-root-to-leaf-paths/)

```python

```

### 二叉树与递归-前序-中序-后序

1.[98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

```python

```

2.[938. 二叉搜索树的范围和](https://leetcode.cn/problems/range-sum-of-bst/)

```python

```

3.[2476. 二叉搜索树最近节点查询](https://leetcode.cn/problems/closest-nodes-queries-in-a-binary-search-tree/)

```python

```

4.[1373. 二叉搜索子树的最大键值和](https://leetcode.cn/problems/maximum-sum-bst-in-binary-tree/)

```python

```

5.[105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

```python

```

6.[106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

```python

```

7.[889. 根据前序和后序遍历构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)

```python

```

8.[1110. 删点成林](https://leetcode.cn/problems/delete-nodes-and-return-forest/)

```python

```

### 二叉树与递归-最近公共祖先

1.[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

```python

```

2.[235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```python

```

3.[1123. 最深叶节点的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-deepest-leaves/)

```python

```

### 二叉树-BFS

1.[102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```python

```

2.[103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

```python

```

3.[513. 找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/)

```python

```

4.[107. 二叉树的层序遍历 II](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)

```python

```

5.[116. 填充每个节点的下一个右侧节点指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)

```python

```

6.[117. 填充每个节点的下一个右侧节点指针 II](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/)

```python

```

7.[2415. 反转二叉树的奇数层](https://leetcode.cn/problems/reverse-odd-levels-of-binary-tree/)

```python

```

8.[2641. 二叉树的堂兄弟节点 II](https://leetcode.cn/problems/cousins-in-binary-tree-ii/)

```python

```

### 回溯-子集型

1.[17. 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```python

```

2.[78. 子集](https://leetcode.cn/problems/subsets/)

```python

```

3.[131. 分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)

```python

```

### 回溯-组合型与剪枝

1.[77. 组合](https://leetcode.cn/problems/combinations/)

```python

```

2.[216. 组合总和 III](https://leetcode.cn/problems/combination-sum-iii/)

```python

```

3.[22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)

```python

```

4.[39. 组合总和](https://leetcode.cn/problems/combination-sum/)

```python

```

### 回溯-排列型

1.[46. 全排列](https://leetcode.cn/problems/permutations/)

```python

```

2.[51. N 皇后](https://leetcode.cn/problems/n-queens/)

```python

```

3.[52. N 皇后 II](https://leetcode.cn/problems/n-queens-ii/)

```python

```

4.[2850. 将石头分散到网格图的最少移动次数](https://leetcode.cn/problems/minimum-moves-to-spread-stones-over-grid/)

```python

```

### 动态规划-从记忆化搜索到递推

1.[198. 打家劫舍](https://leetcode.cn/problems/house-robber/)

```python

```

2.[70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

```python

```

3.[746. 使用最小花费爬楼梯](https://leetcode.cn/problems/min-cost-climbing-stairs/)

```python

```

4.[377. 组合总和 Ⅳ](https://leetcode.cn/problems/combination-sum-iv/)

```python

```

5.[2466. 统计构造好字符串的方案数](https://leetcode.cn/problems/count-ways-to-build-good-strings/)

```python

```

6.[2266. 统计打字方案数](https://leetcode.cn/problems/count-number-of-texts/)

```python

```

7.[213. 打家劫舍 II](https://leetcode.cn/problems/house-robber-ii/)

```python

```

8.[LCR 166. 珠宝的最高价值](https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/)

```python

```

### 0-1-背包-完全背包-至多-恰好-至少

1.[494. 目标和](https://leetcode.cn/problems/target-sum/)

```python

```

2.[322. 零钱兑换](https://leetcode.cn/problems/coin-change/)

```python

```

3.[2915. 和为目标值的最长子序列的长度](https://leetcode.cn/problems/length-of-the-longest-subsequence-that-sums-to-target/)

```python

```

4.[416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

```python

```

5.[518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-ii/)

```python

```

6.[279. 完全平方数](https://leetcode.cn/problems/perfect-squares/)

```python

```

### 最长公共子序列-LCS

1.[1143. 最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)

```python

```

2.[72. 编辑距离](https://leetcode.cn/problems/edit-distance/)

```python

```

### 最长递增子序列-LIS

1.[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

```python

```

2.[1671. 得到山形数组的最少删除次数](https://leetcode.cn/problems/minimum-number-of-removals-to-make-mountain-array/)

```python

```

3.[1626. 无矛盾的最佳球队](https://leetcode.cn/problems/best-team-with-no-conflicts/)

```python

```

### 状态机-DP-买卖股票系列

1.[122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

```python

```

2.[309. 买卖股票的最佳时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

```python

```

3.[188. 买卖股票的最佳时机 IV](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)

```python

```

4.[714. 买卖股票的最佳时机含手续费](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

```python

```

5.[2826. 将三个组排序](https://leetcode.cn/problems/sorting-three-groups/)

```python

```

6.[2786. 访问数组中的位置使分数最大](https://leetcode.cn/problems/visit-array-positions-to-maximize-score/)

```python

```

### 区间-DP

1.[516. 最长回文子序列](https://leetcode.cn/problems/longest-palindromic-subsequence/)

```python

```

2.[1039. 多边形三角剖分的最低得分](https://leetcode.cn/problems/minimum-score-triangulation-of-polygon/)

```python

```

3.[3040. 相同分数的最大操作数目 II](https://leetcode.cn/problems/maximum-number-of-operations-with-the-same-score-ii/)

```python

```

4.[1771. 由子序列构造的最长回文串的长度](https://leetcode.cn/problems/maximize-palindrome-length-from-subsequences/)

```python

```

5.[1000. 合并石头的最低成本](https://leetcode.cn/problems/minimum-cost-to-merge-stones/)

```python

```

### 树形-DP-直径系列

1.[543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)

```python

```

2.[124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

```python

```

3.[2246. 相邻字符不同的最长路径](https://leetcode.cn/problems/longest-path-with-different-adjacent-characters/)

```python

```

4.[687. 最长同值路径](https://leetcode.cn/problems/longest-univalue-path/)

```python

```

5.[3203. 合并两棵树后的最小直径](https://leetcode.cn/problems/find-minimum-diameter-after-merging-two-trees/)

```python

```

6.[1617. 统计子树中城市之间最大距离](https://leetcode.cn/problems/count-subtrees-with-max-distance-between-cities/)

```python

```

7.[2538. 最大价值和与最小价值和的差值](https://leetcode.cn/problems/difference-between-maximum-and-minimum-price-sum/)

```python

```

### 树形-DP-最大独立集

1.[337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)

```python

```

2.[1377. T 秒后青蛙的位置](https://leetcode.cn/problems/frog-position-after-t-seconds/)

```python

```

3.[2646. 最小化旅行的价格总和](https://leetcode.cn/problems/minimize-the-total-price-of-the-trips/)

```python

```

### 树形-DP-最小支配集

1.[968. 监控二叉树](https://leetcode.cn/problems/binary-tree-cameras/)

```python

```

### 单调栈

1.[739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

```python

```

2.[42. 接雨水](https://leetcode.cn/problems/trapping-rain-water/)

```python

```

3.[496. 下一个更大元素 I](https://leetcode.cn/problems/next-greater-element-i/)

```python

```

4.[503. 下一个更大元素 II](https://leetcode.cn/problems/next-greater-element-ii/)

```python

```

5.[901. 股票价格跨度](https://leetcode.cn/problems/online-stock-span/)

```python

```

6.[1019. 链表中的下一个更大节点](https://leetcode.cn/problems/next-greater-node-in-linked-list/)

```python

```

7.[1944. 队列中可以看到的人数](https://leetcode.cn/problems/number-of-visible-people-in-a-queue/)

```python

```

8.[84. 柱状图中最大的矩形](https://leetcode.cn/problems/largest-rectangle-in-histogram/)

```python

```

9.[1793. 好子数组的最大分数](https://leetcode.cn/problems/maximum-score-of-a-good-subarray/)

```python

```

### 单调队列

1.[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

```python

```

2.[2398. 预算内的最多机器人数目](https://leetcode.cn/problems/maximum-number-of-robots-within-budget/)

```python

```

3.[862. 和至少为 K 的最短子数组](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/)

```python

```

4.[1499. 满足不等式的最大值](https://leetcode.cn/problems/max-value-of-equation/)

```python

```

5.[1696. 跳跃游戏 VI](https://leetcode.cn/problems/jump-game-vi/)

```python

```

6.[2944. 购买水果需要的最少金币数](https://leetcode.cn/problems/minimum-number-of-coins-for-fruits/)

```python

```
