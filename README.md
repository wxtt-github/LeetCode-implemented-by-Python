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
"""
思路:
两种写法,自顶向下或自底向上,即递或归
法一:深度优先遍历,与104题类似,入参为结点和其上一级的深度,在求最大深度时,
最大的深度一定是在叶节点上出现,因此直接max(ans, current_depth)即可求得,
而在求最小深度时,需要增加一个叶节点的判断,我们才进行ans的更新,以防非叶节点
对结果的干扰。

法二:当为叶结点时,返回0,当结点的右结点不存在时,则返回其左子树的深度+1,
当结点的左结点不存在时,则返回其右子树的深度+1,当左右子树均存在时,则取其
左右子树最小深度+1。

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional, List
from collections import deque
import math

class Solution:
    # 自顶向下(递),与104题类似,增加一个叶节点判断
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root == None:
            return 0
        # ans = 1e6
        ans = math.inf
        def dfs(node, depth):
            nonlocal ans
            # 最优性剪枝
            if depth + 1 >= ans:
                return
            if node == None:
                return
            if node.left == None and node.right == None:
                ans = min(ans, depth+1)
            dfs(node.left, depth+1)
            dfs(node.right, depth+1)
        dfs(root, 0)
        return ans
    
    # 自底向上(归)
    # def minDepth(self, root: Optional[TreeNode]) -> int:
    #     if root == None:
    #         return 0
    #     if root.right == None:
    #         return self.minDepth(root.left) + 1
    #     if root.left == None:
    #         return self.minDepth(root.right) + 1
    #     if root.left != None and root.right != None:
    #         return min(self.minDepth(root.left), self.minDepth(root.right)) + 1
```

3.[404. 左叶子之和](https://leetcode.cn/problems/sum-of-left-leaves/description/)

```python

```

4.[112. 路径总和](https://leetcode.cn/problems/path-sum/)

```python
"""
思路:
分为两种方法,递增法和递减法。
法一:使用深度优先搜索dfs,入参为结点和上一级结点累加的数值,注意路径是根到叶节点,
因此最后需要判断传入的结点是否为叶节点
法二:递减法,每一级都减去当前结点的val,然后判断该结点是否是叶结点,若为叶节点,则
直接返回True,然后返回递归函数,传入其左子树和右子树

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional
class Solution:
    # 递增判断法
    # def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
    #     if root is None:
    #         return False
    #     ans = False
    #     def dfs(node, val):
    #         nonlocal ans
    #         if node is None:
    #             return val
    #         if dfs(node.left, val + node.val) == targetSum and node.left == None and node.right == None:
    #             ans = True
    #         if dfs(node.right, val + node.val) == targetSum and node.left == None and node.right == None:
    #             ans = True
    #     dfs(root, 0)
    #     return ans

    # 递减判断法
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root == None:
            return False
        targetSum -= root.val
        if targetSum == 0 and root.left == None and root.right == None:
            return True
        return self.hasPathSum(root.left, targetSum) or self.hasPathSum(root.right, targetSum)
```

5.[129. 求根节点到叶节点数字之和](https://leetcode.cn/problems/sum-root-to-leaf-numbers/)

```python
"""
思路:
本题的核心实际上是求根结点到叶结点的路径,采用深度优先搜索dfs递归遍历即可,
入参为结点和上一级结点的数值和,注意每下一级,可以将父节点数值乘10加上本级
结点计算出总和。收集所有路径的和数值,累加即可,可以将数组ans优化掉,
用常量ans=0进行累加,但不影响空间复杂度,仅是常量级优化。

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional
class Solution:
    def sumNumbers(self, root: Optional[TreeNode]) -> int:
        if root == None:
            return 0
        # ans = []
        ans = 0
        def dfs(node, x):
            nonlocal ans
            if node.left == None and node.right == None:
                # ans.append(x * 10 + node.val)
                ans += x * 10 + node.val
            if node.left:
                dfs(node.left, x * 10 + node.val)
            if node.right:
                dfs(node.right, x * 10 + node.val)
        dfs(root, 0)
        # return sum(ans)
        return ans
```

6.[1448. 统计二叉树中好节点的数目](https://leetcode.cn/problems/count-good-nodes-in-binary-tree/)

```python
"""
思路:
好结点即其值不小于其所有上级结点的结点,因此可以用深度优先搜索dfs,
入参为结点和其所有上级结点的最大值,当该结点满足大于其值时,统计,
不断更新其最大值,传入左右子树即可。

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional
import math
class Solution:
    def goodNodes(self, root: Optional[TreeNode]) -> int:
        if root == None:
            return 0
        ans = 0
        def dfs(node, max_num):
            nonlocal ans
            if node == None:
                return
            if node.val >= max_num:
                ans += 1
            dfs(node.left, max(node.val, max_num))
            dfs(node.right, max(node.val, max_num))
        dfs(root, -math.inf)
        return ans
```

7.[987. 二叉树的垂序遍历](https://leetcode.cn/problems/vertical-order-traversal-of-a-binary-tree/)

```python
"""
思路:本题核心主要分两步骤,一是遍历,二是获取每个结点的(col, row, val)信息,可以
用dfs遍历,然后将其存入defaultdict哈希表中,以键为列,值为(row, val)二元组的列表,
获得所有信息后,先对键进行排序遍历,然后对其元组列表进行排序,至此,列,行,值均完成了
排序,统计即可

复杂度的计算:对于时间复杂度,关键在于排序的数量,即同行同列的数量,从(0, 0)出发,
先向左再向右,得到(2, 0),先向右再向左,也得到(2, 0),这两个同样坐标的点也重复操作
得到4个在(4, 0)的结点,则当有2^k个结点在同一坐标时,增加2^(k+2)个结点,可以得到
2^(k+1)个在同一坐标的结点,当n=2^(k+2)-3时,最多有2^k个结点在同一坐标,则需要排序
的点大约为n/4个,则排序的复杂度为O(nlogn),对于空间复杂度,当排为链状时,即每个结点
列坐标不同时,空间复杂度为O(n)
时间复杂度:O(nlogn)
空间复杂度:O(n)
"""
from typing import Optional, List
from collections import defaultdict
class Solution:
    def verticalTraversal(self, root: Optional[TreeNode]) -> List[List[int]]:
        hash_table = defaultdict(list)
        if root == None:
            return []
        def dfs(node, row, col):
            if node == None:
                return
            nonlocal hash_table
            hash_table[col].append((row, node.val))
            dfs(node.left, row+1, col-1)
            dfs(node.right, row+1, col+1)
        dfs(root, 0, 0)
        ans = []
        for key in sorted(hash_table.keys()):
            hash_table[key].sort()
            temp = []
            for _, value in hash_table[key]:
                temp.append(value)
            ans.append(temp)
        return ans
```

### 二叉树与递归-灵活运用

1.[100. 相同的树](https://leetcode.cn/problems/same-tree/)

```python
"""
思路:
边边历,边检查,首先判断边界条件当有一结点为空时,则需要两个均为空,否则不满足,
然后判断其值,若不相同则不满足,然后递归遍历两个结点的左子树和右子树,给了两种
写法,一种dfs,一种直接递归

时间复杂度:O(min(m, n))
空间复杂度:O(min(m, n)),最坏情况退化成一条链
"""
from typing import Optional

class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        # 法一
        # def dfs(node1: Optional[TreeNode], node2:Optional[TreeNode]):
        #     if node1 == None or node2 == None:
        #         if node1 != node2:
        #             return False
        #         else:
        #             return True
        #     if node1.val != node2.val:
        #         return False
        #     return dfs(node1.left, node2.left) and dfs(node1.right, node2.right)
        # return dfs(p, q)
    
        # 法二
        if p == None or q == None:
            return p == q
        if p.val != q.val:
            return False
        return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
```

2.[101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

```python
"""
思路:和100题-相同的数类似,只需要将递归遍历改为递归结点1左子树和递归结点2右子树即可

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional

class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        def dfs(node1, node2):
            if node1 == None or node2 == None:
                if node1 != node2:
                    return False
                else:
                    return True
            if node1.val != node2.val:
                return False
            return dfs(node1.left, node2.right) and dfs(node1.right, node2.left)
        # 两种返回均可
        # if root == None or (root.left == None and root.right == None):
        #     return True
        # return dfs(root.left, root.right)
        return dfs(root, root)
```

3.[110. 平衡二叉树](https://leetcode.cn/problems/balanced-binary-tree/)

```python
"""
思路:
平衡二叉树即左子树与右子树的高度差不大于1,首先要写一个获取结点
高度的函数,边界条件为结点为None时,返回0,否则返回其左右子树高度
最大值加1,然后做特殊处理,用-1来标记一个结点的左右子树高度不平衡,
当左右子树其中一个出现高度-1时,或左右子树高度差大于1时,返回-1

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional

class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:
        def getHeight(node: Optional[TreeNode]):
            if node == None:
                return 0
            left_height = getHeight(node.left)
            right_height = getHeight(node.right)
            if left_height == -1 or right_height == -1 or abs(left_height - right_height) > 1:
                return -1
            return max(getHeight(node.left), getHeight(node.right)) + 1
        return not getHeight(root)==-1
```

4.[199. 二叉树的右视图](https://leetcode.cn/problems/binary-tree-right-side-view/)

```python
"""
思路:
dfs深度优先搜索,先遍历右子树,确保右结点先被遍历,当第一个深度出现时,
则添加进ans中

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional, List

class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        ans = []
        def dfs(node: Optional[TreeNode], depth: int):
            if node == None:
                return
            if depth == len(ans):
                ans.append(node.val)
            dfs(node.right, depth+1)
            dfs(node.left, depth+1)
        dfs(root, 0)
        return ans
```

5.[226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

```python
"""
思路:
直接原地交换其左右子树,并递归地交换其子节点的左右子树,
注意这里要原地操作,创建新树是无法AC的

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional

class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if root == None:
            return None
        root.left, root.right = root.right, root.left
        self.invertTree(root.left)
        self.invertTree(root.right)
        return root
```

6.[617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/)

```python
"""
思路:
递归合并,先处理3种边界条件,然后递归其左右子树即可

时间复杂度:O(min(m, n))
空间复杂度:O(min(m, n))
"""
from typing import Optional

class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 == None and root2 == None:
            return None
        if root1 == None and root2:
            return root2
        if root1 and root2 == None:
            return root1
        root1.left = self.mergeTrees(root1.left, root2.left)
        root1.right = self.mergeTrees(root1.right, root2.right)
        root1.val += root2.val
        return root1
```

7.[1026. 节点与其祖先之间的最大差值](https://leetcode.cn/problems/maximum-difference-between-node-and-ancestor/)

```python
"""
思路:
求祖先到其子节点的最大差值,仅需维护其祖先结点到子节点的路径的最大值与最小值即可,
然后用该结点的差值来更新ans即可,深度优先搜索dfs入参为结点,最小值,最大值

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional

class Solution:
    def maxAncestorDiff(self, root: Optional[TreeNode]) -> int:
        ans = 0
        def dfs(node: Optional[TreeNode], min_val, max_val):
            nonlocal ans
            if node == None:
                return
            temp = max(abs(min_val - node.val), abs(max_val - node.val))
            if temp > ans:
                ans = temp
            dfs(node.left, min(min_val, node.val), max(max_val, node.val))
            dfs(node.right, min(min_val, node.val), max(max_val, node.val))
        dfs(root, root.val, root.val)
        return ans
```

8.[1080. 根到叶路径上的不足节点](https://leetcode.cn/problems/insufficient-nodes-in-root-to-leaf-paths/)

```python
"""
思路:
首先要理解"不足结点"的定义,即所有经过该结点的"根->叶"路径的和,均满足小于limit,
则该结点为不足结点,考虑叶结点和非叶结点的情况,对于一个叶结点,遍历时拿到其上的
路径和,然后加上val与limit判断即可,而对于非叶结点,当且仅当其左右子树均为None时,
才进行删除,这是因为,考虑"不足结点"的定义,若其子叶结点均为不足结点,则其也为"不足结点",
根据递归的顺序,若其子叶结点均被删除时,则该结点也变成了新的叶结点,会被边界条件处理,
若其为非叶结点,则直接返回即可。
为了能够使用递归,对入参进行了优化,用limit减去val值,若大于0,且该结点为非叶结点,则返回
None,否则直接返回其结点,然后对其左右子树递归传入新的limit值,最后判断保留非叶结点

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional

class Solution:
    def sufficientSubset(self, root: Optional[TreeNode], limit: int) -> Optional[TreeNode]:
        if root == None:
            return None
        limit -= root.val
        if root.left == None and root.right == None:
            if limit > 0:
                return None
            else:
                return root
        root.left = self.sufficientSubset(root.left, limit)
        root.right = self.sufficientSubset(root.right, limit)
        if root.left or root.right:
            return root
        # else:
        #     return None
```

### 二叉树与递归-前序-中序-后序

1.[98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)*

```python
"""
思路:
对于前序:遵循"根-左-右",先处理边界条件node==None,对于二叉搜索树根结点,
其需满足严格大于左子树,严格小于右子树,若不满足则返回False,然后递归遍历
其左右子树即可

对于中序:遵循"左-根-右",对一颗二叉搜索树,若按中序遍历,则实际上得到的序列
是一个递增的序列,则首先要定义一个pre初始为-math.inf,先处理边界条件,
然后按"左-根-右"顺序处理,对于根,要进行判断的同时,更新pre

对于后序:目前还不太理解,后续更新

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional
import math

class Solution:
    # 前序
    # def isValidBST(self, root: Optional[TreeNode]) -> bool:
    #     def dfs(node: Optional[TreeNode], min_val, max_val):
    #         if node == None:
    #             return True
    #         if node.val > min_val and node.val < max_val:
    #             pass
    #         else:
    #             return False
    #         return dfs(node.left, min_val, node.val) and dfs(node.right, node.val, max_val)
    #     return dfs(root, -math.inf, math.inf)
    
    # 中序
    pre = -math.inf
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if root == None:
            return True
        if not self.isValidBST(root.left):
            return False
        if root.val <= self.pre:
            return False
        self.pre = root.val
        if not self.isValidBST(root.right):
            return False
        return True
```

2.[938. 二叉搜索树的范围和](https://leetcode.cn/problems/range-sum-of-bst/)

```python
"""
思路:
采用先序遍历,写一个dfs,处理空的边界条件,按"根-左-右"的顺序遍历即可,但是这样就没有利用到
二叉搜索树的性质,虽然不影响复杂度,但从算法的角度不是最优做法,特此补充一个递归做法,若该结点
的值不在范围内,则递归其相应子树,若在范围内,则ans加上其值,并递归左右子树

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional

class Solution:
    # 法一
    def rangeSumBST(self, root: Optional[TreeNode], low: int, high: int) -> int:
        ans = 0
        def dfs(node: Optional[TreeNode]):
            nonlocal ans
            if node == None:
                return
            if node.val >= low and node.val <= high:
                ans += node.val
            dfs(node.left)
            dfs(node.right)
        dfs(root)
        return ans
    
    # 法二
    def rangeSumBST(self, root: Optional[TreeNode], low: int, high: int) -> int:
        if root == None:
            return 0
        x = root.val
        if x > high:
            return self.rangeSumBST(root.left, low, high)
        if x < low:
            return self.rangeSumBST(root.right, low, high)
        return x + self.rangeSumBST(root.left, low, high) + self.rangeSumBST(root.right, low, high)
```

3.[2476. 二叉搜索树最近节点查询](https://leetcode.cn/problems/closest-nodes-queries-in-a-binary-search-tree/)

```python
"""
思路:
对于二叉搜索树,可以通过一个中序遍历得到一个严格递增的数组nums,实现一个二分查找的算法
lower_bound(arr, target),返回第一个大于等于target的值的坐标index,若arr的元素全小于target,
则会返回len(arr),然后根据得到坐标index确定mn和mx即可,注意这里若找到index,则只有
两种情况,若index-1为-1,则直接返回mn=-1即可,若index-1>0,则nums[index-1]一定是满足
小于query的最大的数mn

时间复杂度:O(n+q*logn)
空间复杂度:O(n)
"""
from typing import Optional, List
import math

class Solution:
    def closestNodes(self, root: Optional[TreeNode], queries: List[int]) -> List[List[int]]:
        def lower_bound(arr, target):
            left = 0
            right = len(arr) - 1
            while left <= right:
                mid = (left + right) // 2
                if arr[mid] < target:
                    left = mid + 1
                else:
                    right = mid - 1
            return left
        
        nums = []
        def dfs(node: Optional[TreeNode]):
            if node == None:
                return
            dfs(node.left)
            nums.append(node.val)
            dfs(node.right)

        dfs(root)
        ans = []
        for i in queries:
            index = lower_bound(nums, i)
            if index == len(nums):
                mx = -1
            else:
                mx = nums[index]
            if index == len(nums) or nums[index] != i:
                index -= 1
            if index == -1:
                mn = -1
            else:
                mn = nums[index]

            ans.append([mn, mx])
        return ans
```

4.[1373. 二叉搜索子树的最大键值和](https://leetcode.cn/problems/maximum-sum-bst-in-binary-tree/)

```python
"""
思路:需要采用后序遍历"左-右-根"的方式,每次遍历结点返回一个三元组,分别为树的最小值,树的最大值和树的总和。
先处理边界条件结点为空的情况,二叉搜索树的必要条件,即左子树的最大值要小于根结点,右子树的最小值要大于根结点,
为使空结点能满足必要条件,则返回inf, -inf, 0,使得其满足,然后递归遍历其左右子树,判断根结点是否满足二叉搜索
树的必要条件,若不满足,则返回-inf, inf, 0,若满足,则计算总和s = l_sum + r_sum + x,然后更新ans,最后返回
该二叉搜索树的最小值,最大值和总和。之所以能用必要条件来解决这个问题,是因为采用后续遍历的方式,即"归",从最小
的子树开始归并,若每个子树都是二叉搜索树,则组合成的树也是二叉搜索树。

时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional
from math import inf

class Solution:
    def maxSumBST(self, root: Optional[TreeNode]) -> int:
        ans = 0
        def dfs(node: Optional[TreeNode]):
            if node == None:
                return inf, -inf, 0
            l_min, l_max, l_sum = dfs(node.left)
            r_min, r_max, r_sum = dfs(node.right)
            x = node.val
            if l_max >= x or r_min <= x:
                return -inf, inf, 0
            s = l_sum + r_sum + x
            nonlocal ans
            ans = max(ans, s)
            return min(l_min, x), max(r_max, x), s
        
        dfs(root)
        return ans
```

5.[105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)*

```python
"""
思路:分为直接递归写法和利用哈希表递归写法,关键在于确定边界

法一:
时间复杂度:O(n^2)
空间复杂度:O(n^2)
法二:
时间复杂度:O(n)
空间复杂度:O(n)
"""
from typing import Optional, List

class Solution:
    # 法一
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder:
            return None
        left_size = inorder.index(preorder[0])
        left = self.buildTree(preorder[1:1+left_size], inorder[:left_size])
        right = self.buildTree(preorder[1+left_size:], inorder[1+left_size:])
        return TreeNode(preorder[0], left, right)
    
    # 法二
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        index = {}
        for i, x in enumerate(inorder):
            index[x] = i
        
        def dfs(pre_l, pre_r, in_l, in_r):
            if pre_l == pre_r:
                return None
            
            left_size = index[preorder[pre_l]] - in_l
            left = dfs(pre_l + 1, pre_l + 1 + left_size, in_l, in_l + left_size)
            right = dfs(pre_l + 1 + left_size, pre_r, in_l + 1 + left_size, in_r)
            return TreeNode(preorder[pre_l], left, right)
        
        return dfs(0, len(preorder), 0, len(inorder))
```

6.[106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)*

```python
"""
思路:
对哈希表的边界处理还不太理解,以后补充,提供直接递归写法

法一:
时间复杂度:O(n^2)
空间复杂度:O(n^2)
"""
from typing import Optional, List

class Solution:
    # 法一
    def buildTree(self, inorder: List[int], postorder: List[int]) -> Optional[TreeNode]:
        if not postorder:
            return None
        
        x = postorder[-1]
        left_size = inorder.index(x)
        left = self.buildTree(inorder[:left_size], postorder[:left_size])
        right = self.buildTree(inorder[left_size + 1:], postorder[left_size:-1])
        return TreeNode(x, left, right)
```

7.[889. 根据前序和后序遍历构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)*

```python
"""
思路:
对哈希表的边界处理还不太理解,以后补充,目前提供直接递归写法,这里的关键点在于构造出的
树可能不唯一,不妨用根后的第一个值假定为新根

法一:
时间复杂度:O(n^2)
空间复杂度:O(n^2)
"""
from typing import Optional, List

class Solution:
    def constructFromPrePost(self, preorder: List[int], postorder: List[int]) -> Optional[TreeNode]:
        if not preorder:
            return None
        if len(preorder) == 1:
            return TreeNode(preorder[0])
        
        left_size = postorder.index(preorder[1]) + 1
        left = self.constructFromPrePost(preorder[1:1+left_size], postorder[:left_size])
        right = self.constructFromPrePost(preorder[1+left_size:], postorder[left_size:-1])
        return TreeNode(preorder[0], left, right)
```

8.[1110. 删点成林](https://leetcode.cn/problems/delete-nodes-and-return-forest/)

```python
"""
思路:
采用后序遍历"左-右-根"的方式,写一个dfs,为了达到O(1)查找,用集合转换列表,使得用哈希表存储

时间复杂度:O(n+m)
空间复杂度:O(n+m)
"""
from typing import Optional, List

class Solution:
    def delNodes(self, root: Optional[TreeNode], to_delete: List[int]) -> List[TreeNode]:
        ans = []
        s = set(to_delete)
        def dfs(node: Optional[TreeNode]):
            if node == None:
                return None
            node.left = dfs(node.left)
            node.right = dfs(node.right)
            if node.val not in s:
                return node
            if node.left:
                ans.append(node.left)
            if node.right:
                ans.append(node.right)
            return None
        
        if dfs(root):
            ans.append(root)
        
        return ans
```

### 二叉树与递归-最近公共祖先

1.[236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

```python
"""
思路：
首先采用递归的思想，按先序遍历，关键是定义边界条件。考虑递归传入左右子树，当找到p或q或None时返回，
若左右子树均找到，考虑只有两层的结构，即一个p一个q，则返回其root，若其一边为None，则只采用另一边结果即可。

时间复杂度：O(n)
空间复杂度：O(n)
"""
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root in [p, q, None]:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        return left or right
```

2.[235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```python
"""
思路：
本题与236题的区别是该树为二叉搜索树，直接采用236题解法也可以，复杂度无差异，
但为了剪枝优化，考虑p和q的位置，要么都在左子树，要么都在右子树，要么一个结点
是另一个的祖先，要么一左一右，而二叉搜索树可以很好地通过值来判断位置关系，
若都在左子树，则只需返回递归左子树的结果，若都在右子树，则只需返回递归右子树的结果，
若一个结点是另一个的祖先，则返回该祖先结点即根结点即可,若一左一右，则也返回根节点即可。

时间复杂度：O(n)
空间复杂度：O(n)
"""
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        # 法一
        # if root in [p, q, None]:
        #     return root
        # left = self.lowestCommonAncestor(root.left, p, q)
        # right = self.lowestCommonAncestor(root.right, p, q)
        # if left and right:
        #     return root
        # return left or right

        # 法二
        x = root.val
        if p.val < x and q.val < x:
            return self.lowestCommonAncestor(root.left, p, q)
        if p.val > x and q.val > x:
            return self.lowestCommonAncestor(root.right, p, q)
        return root
```

3.[1123. 最深叶节点的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-deepest-leaves/)

```python
"""
思路：
两种方法，一种方法是定义ans和max_depth，然后dfs遍历，不断更新最大深度，当左右子树的深度
相等且与最大深度相同时，则ans更新为node。另一种方法是dfs返回子树的最大高度以及祖父结点，
当node==None时返回0, None，然后递归遍历其左右子树，若左子树高，则返回左子树树高+1与其祖父
结点，若右子树高，则返回右子树树高+1与其祖父结点，若同样高则返回左子树树高+1与node。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional

class Solution:
    def lcaDeepestLeaves(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # 法一
        # ans = None
        # max_depth = -1
        # def dfs(node, depth):
        #     nonlocal ans, max_depth
        #     if node == None:
        #         max_depth = max(max_depth, depth)
        #         return depth
        #     left_depth = dfs(node.left, depth+1)
        #     right_depth = dfs(node.right, depth+1)
        #     if left_depth == right_depth == max_depth:
        #         ans = node
        #     return max(left_depth, right_depth)
        # dfs(root, 0)
        # return ans

        # 法二
        def dfs(node):
            if node == None:
                return 0, None
            left_height, left_lca = dfs(node.left)
            right_height, right_lca = dfs(node.right)
            if left_height > right_height:
                return left_height+1, left_lca
            elif left_height < right_height:
                return right_height+1, right_lca
            else:
                return left_height+1, node
        return dfs(root)[1]
```

### 二叉树-BFS

1.[102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```python
"""
思路：
层序遍历首先需要使用队列解决较为简单，from collections import deque，大致思路是
设置一个while循环，条件是队列不为空，pop一个结点后如果其有左右子树，则将其添加进队尾，
如此便解决了层序遍历的问题，在本题中，还有一个问题，就是列表嵌套列表的问题，这个需要在
while循环中设置一个for循环，创建一个临时空列表，然后根据当前队列长度来pop结点，添加其值
进空列表中，如此解决嵌套问题。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional, List
from collections import deque

class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root == None:
            return []
        q = deque()
        ans = []
        q.append(root)
        while len(q) != 0:
            temp_arr = []
            for i in range(len(q)):
                node = q.popleft()
                temp_arr.append(node.val)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            ans.append(temp_arr)
        return ans
```

2.[103. 二叉树的锯齿形层序遍历](https://leetcode.cn/problems/binary-tree-zigzag-level-order-traversal/)

```python
"""
思路：
在102题中，我们已经学会层序遍历的方法以及如何处理嵌套返回的问题，这题锯齿形层序遍历，
在我们层序遍历方法不变的情况下，无非是设置一个flag，然后将子列表在合适的时候进行逆置，
可以用temp_arr[:, :, -1]的方法进行逆置，也可以像我下面的方法一样，用双端队列q2来代替
temp_arr，在合适的时候，适时选择往队头插还是往队尾插，然后进行强转append即可。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional, List
from collections import deque

class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root == None:
            return []
        ans = []
        flag = True
        q1 = deque()
        q1.append(root)
        while len(q1) != 0:
            q2 = deque()
            for i in range(len(q1)):
                node = q1.popleft()
                if flag == True:
                    q2.append(node.val)
                else:
                    q2.appendleft(node.val)
                if node.left:
                    q1.append(node.left)
                if node.right:
                    q1.append(node.right)
            ans.append(list(q2))
            flag = not flag
        return ans
```

3.[513. 找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/)

```python
"""
思路：
有两种方法，可以用"根左右"dfs进行遍历，仅当出现更大的深度时，进行ans的更新即可，
也可以用层序遍历，经过102题我们已经学会层序遍历的一般写法以及通过for循环实现嵌套
列表的返回逻辑，可以设置一个flag，仅在每一层的第一个进行更新即可。对于层序遍历，
也可以转换思路，在队列添加时，先添加左子树，再添加右子树，这样最后一个结点即为答案，
但对复杂度无明显提升，属最优剪枝解法。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional
from collections import deque

class Solution:
    def findBottomLeftValue(self, root: Optional[TreeNode]) -> int:
        # 法一
        # ans = None
        # max_depth = -1
        # def dfs(node, depth):
        #     nonlocal ans, max_depth
        #     if depth > max_depth:
        #         ans = node.val
        #         max_depth = depth
        #     if node.left:
        #         dfs(node.left, depth+1)
        #     if node.right:
        #         dfs(node.right, depth+1)
        # dfs(root, 0)
        # return ans

        # 法二
        ans = None
        q = deque()
        q.append(root)
        while len(q) != 0:
            flag = True
            for i in range(len(q)):
                node = q.popleft()
                if flag:
                    ans = node.val
                    flag = False
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
        return ans
```

4.[107. 二叉树的层序遍历 II](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)

```python
"""
思路：
正常写层序遍历写法，最后逆置ans即可。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional, List
from collections import deque

class Solution:
    def levelOrderBottom(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root == None:
            return []
        ans = []
        q = deque([root])
        while q:
            temp_arr = []
            for i in range(len(q)):
                node = q.popleft()
                temp_arr.append(node.val)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            ans.append(temp_arr)
        return ans[::-1]
```

5.[116. 填充每个节点的下一个右侧节点指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)

```python
"""
思路：
法一：
通过队列进行层序遍历，在for循环中进行拼接即可
时间复杂度：O(n)
空间复杂度：O(n)。因为二叉树最后一层约有n/2个结点

法二：
将二叉树的每一层视为一个链表。设置current_node，dummy_node，next_node，
current_node用来遍历整个二叉树，dummy_node用于记录每一层的最左结点，
next_node用于拼接每一层的结点。难点在于循环的设计，首先将current_node结点
指向root，然后最外层是while current_node:循环，用于遍历整个二叉树的每一个结点，
设置一个dummy_node和next_node，初始时next_node=dummy_node，同样设置一个
while current_node的循环，然后将next_node指向current_node的左子树，
并更新next_node值，然后指向右子树，并更新。然后将current_node赋值为
current_node.next，这样能遍历完该层。最后在第二场循环外面，第一层循环里面
将current_node赋值为dummy_node.next即可。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional
from collections import deque

class Solution:
    def connect(self, root: 'Optional[Node]') -> 'Optional[Node]':
        # 法一
        # if root == None:
        #     return None
        # q = deque([root])
        # while q:
        #     temp = q[0]
        #     for i in range(len(q)):
        #         node = q.popleft()
        #         if i != 0:
        #             temp.next = node
        #             temp = node
        #         if node.left:
        #             q.append(node.left)
        #         if node.right:
        #             q.append(node.right)
        # return root
        
        # 法二
        current_node = root
        while current_node:
            dummy_node = Node()
            next_node = dummy_node
            while current_node:
                if current_node.left:
                    next_node.next = current_node.left
                    next_node = current_node.left
                if current_node.right:
                    next_node.next = current_node.right
                    next_node = current_node.right
                current_node = current_node.next
            current_node = dummy_node.next
        return root
```

6.[117. 填充每个节点的下一个右侧节点指针 II](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/)

```python
"""
思路：
同116题，关键在于将二叉树每一层当成一个链表进行处理，设置两层循环，第一层循环
用于控制遍历整个二叉树的每个结点，第二层循环用于控制遍历每一层结点以进行其子结点
的连接。

时间复杂度：O(n)
空间复杂度：O(1)
"""
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        current_node = root
        while current_node:
            dummy_node = Node()
            next_node = dummy_node
            while current_node:
                if current_node.left:
                    next_node.next = current_node.left
                    next_node = next_node.next
                if current_node.right:
                    next_node.next = current_node.right
                    next_node = next_node.next
                current_node = current_node.next
            current_node = dummy_node.next
        return root
```

7.[2415. 反转二叉树的奇数层](https://leetcode.cn/problems/reverse-odd-levels-of-binary-tree/)

```python
"""
思路：
层序遍历，搜集每一层的结点，当该层为奇数层时，使用一个列表vals来收集该层的值，
可以使用vals.reverse()进行原地逆置，然后将值依次赋值到该层结点即可。

时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional
from collections import deque

class Solution:
    def reverseOddLevels(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if root == None:
            return None
        q = deque([root])
        depth = 0
        while q:
            current_depth_nodes = []
            for i in range(len(q)):
                node = q.popleft()
                current_depth_nodes.append(node)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            if depth % 2 == 1:
                vals = [node.val for node in current_depth_nodes]
                vals.reverse()
                for i in range(len(vals)):
                    current_depth_nodes[i].val = vals[i]
            depth += 1
        return root
```

8.[2641. 二叉树的堂兄弟节点 II](https://leetcode.cn/problems/cousins-in-binary-tree-ii/)

```python
"""
思路：
法一：层序遍历，获取每一层的结点和其父节点，然后更新每一层的结点值即可，需要注意拷贝一份值，
否则会出现先更新干扰后更新的问题，但是这个方法时间复杂度为O(n^2)太高，无法通过所有用例，不推荐。
时间复杂度：O(n^2)
空间复杂度：O(n)

法二：层序遍历，遍历每层结点，观察到其左/右子结点更新的逻辑本质上是用下一层的结点的所有值之和，
减去其左，右子结点的值之和，然后用这个数来赋值，如此可以设置两个变量next_level_sum，current_nodes，
next_level_sum用来记录下一层结点的值之和，current_nodes用来保存当前层结点，然后后面再遍历一次
current_nodes，更新其每个元素的左右子结点
时间复杂度：O(n)
空间复杂度：O(n)
"""
from typing import Optional
from collections import deque

class Solution:
    def replaceValueInTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # 法一
        # if root == None:
        #     return None
        # q = deque([[root, None]])
        # while q:
        #     current_depth_nodes = []
        #     for i in range(len(q)):
        #         node = q.popleft()
        #         current_depth_nodes.append(node)
        #         if node[0].left:
        #             q.append([node[0].left, node[0]])
        #         if node[0].right:
        #             q.append([node[0].right, node[0]])
        #     vals = []
        #     for i, node1 in enumerate(current_depth_nodes):
        #         total = 0
        #         for j, node2 in enumerate(current_depth_nodes):
        #             if j != i and node2[1] != node1[1]:
        #                 total += node2[0].val
        #         vals.append(total)
        #     for i, node1 in enumerate(current_depth_nodes):
        #         node1[0].val = vals[i]
        # return root

        # 法二
        if root == None:
            return None
        q = deque([root])
        root.val = 0
        while q:
            next_level_sum = 0
            current_nodes = []
            for i in range(len(q)):
                node = q.popleft()
                current_nodes.append(node)
                if node.left:
                    q.append(node.left)
                    next_level_sum += node.left.val
                if node.right:
                    q.append(node.right)
                    next_level_sum += node.right.val
            for i in current_nodes:
                left_num = 0
                right_num = 0
                if i.left:
                    left_num = i.left.val
                if i.right:
                    right_num = i.right.val
                if i.left:
                    i.left.val = next_level_sum - left_num - right_num
                if i.right:
                    i.right.val = next_level_sum - left_num - right_num
        return root
```

### 回溯-子集型

1.[17. 电话号码的字母组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)

```python
"""
思路：
感官上其实像小学的组合题，从各个组中依次选择，然后进行组合，难点是如何定义变量，
来优美地实现这个过程。首先要做一个映射表，将数字2-9与字符串进行映射，然后设置
一个ans = []来保存答案，设置一个path = [''] * length来保存拼接的路径。写一个
dfs函数，传入index，从0开始，用于记录路径，当i == length时，则说明路径已达尽头，
收集拼接path列表成字符串，然后添加进ans列表中，记得return，当i != length时，
首先用i来索引拿到digit，digit用于映射拿到字符串，然后遍历这个字符串，拿到字符c，
将字符c赋值给path[i]，然后递归调用dfs(i+1)，至此完成整个算法。对于长度不定的拼接，
循环并不能解决问题，需要通过递归回溯来解决。

时间复杂度：O(n*4^n)。因为极限情况下，长度为n时，需要遍历4^n次，并且每次最后都要拼接
整个长度为n的path数组，因此复杂度为O(n*4^n)。
空间复杂度：O(n)
"""
from typing import List

class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        map_c = ["", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"]
        length = len(digits)
        if length == 0:
            return []
        ans = []
        path = [''] * length
        def dfs(i):
            if i == length:
                ans.append(''.join(path))
                return
            for c in map_c[int(digits[i])]:
                path[i] = c
                dfs(i+1)
        dfs(0)
        return ans
```

2.[78. 子集](https://leetcode.cn/problems/subsets/)

```python
"""
思路：
法一："选与不选"的思想
首先设置一个ans用于储存答案，然后设置一个path用于记录路径，设置一个dfs遍历，i从0开始，
对于"选与不选"的情况，先写不选的情况，再写选的情况，对于不选的情况，直接dfs(i+1)即可，
对于选的情况，将nums[i]加进path的路径中，然后再dfs(i+1)，
最后要恢复现场将path数组pop一下，最后是边界条件的处理，当i与length相等时，
将path的copy加进ans中return即可。

法二："从答案出发"的思想
首先设置一个ans用于储存答案，然后设置一个path用于记录路径，核心在于，每次都强制选一个
数，并且选完后要把他加进path中，由于排列的关系，[1, 2]和[2, 1]会重复计算，因此我们
在选数时需要强制下一个选的数的下标要大于当前选数的下标，因此在内部设置一个
for j in range(i, length)的循环，对于每个节点直接将path添加进ans中，当i与length
相等时，进行return。以nums = [1, 2, 3]举例，大致的路径就是加入[]，然后加入[1]，
然后加入[1, 2]，然后加入[1, 2, 3]，然后pop三次，然后加入[2]，加入[2, 3]，然后pop
两次，然后加入[3]，至此完成全过程。

时间复杂度：O(n*2^n)
空间复杂度：O(n)
"""
from typing import List

class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        # 法一:
        # ans = []
        # path = []
        # length = len(nums)

        # def dfs(i):
        #     if i == length:
        #         ans.append(path.copy())
        #         return
        #     dfs(i+1)
        #     path.append(nums[i])
        #     dfs(i+1)
        #     path.pop()

        # dfs(0)
        # return ans

        # 法二:
        ans = []
        path = []
        length = len(nums)

        def dfs(i):
            ans.append(path.copy())
            if i == length:
                return
            
            for j in range(i, length):
                path.append(nums[j])
                dfs(j+1)
                path.pop()
            
        dfs(0)
        return ans
```

3.[131. 分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)

```python
"""
思路:
法一："从答案出发"的思想
首先设置一个ans用于储存答案，然后设置一个path用于记录路径。其中，只有完全分割回文时，
才会将整个path添加进ans中，因此当i与length相等时，才进行append操作。然后设置一个
for j in range(i, length)的循环，t = s[i:j+1]，由于j在变，用于代表分割的部分，
然后判断是否是回文，若是回文则进行append，然后继续递归dfs(j+1)，用于让i变为j+1，
然后恢复现场pop即可。

法二："选与不选"的思想
首先设置一个ans用于储存答案，然后设置一个path用于记录路径。核心思想在于，对于整个
字符串，前段可以进行分割，也可以进行延长，后段代表剩余分割的部分。因此当i与length
相等时，代表分割完成，将path添加进ans中。当i<length-1时，代表还可以延长前段，
则进行递归遍历dfs(i+1, start)，当i==length-1时，则必须分割了，这里start代表
前段的最开始的位置。前面写了延长的情况，接下来是分割的情况，得到t=s[start:i+1]，
然后判断是否是回文，若是回文，则接着递归剩余部分，此时start也变为i+1，则dfs(i+1, i+1)，
然后恢复现场即可。

时间复杂度：O(n*2^n)
空间复杂度：O(n)
"""
from typing import List

class Solution:
    def partition(self, s: str) -> List[List[str]]:
        # ans = []
        # path = []
        # length = len(s)

        # def dfs(i):
        #     if i == length:
        #         ans.append(path.copy())

        #     for j in range(i, length):
        #         t = s[i:j+1]
        #         if t == t[::-1]:
        #             path.append(t)
        #             dfs(j+1)
        #             path.pop()

        # dfs(0)
        # return ans

        ans = []
        path = []
        length = len(s)
        def dfs(i, start):
            if i == length:
                ans.append(path.copy())
                return
            if i < length - 1:
                dfs(i+1, start)

            t = s[start:i+1]
            if t == t[::-1]:
                path.append(t)
                dfs(i+1, i+1)
                path.pop()

        dfs(0, 0)
        return ans
```

### 回溯-组合型与剪枝

1.[77. 组合](https://leetcode.cn/problems/combinations/)

```python
"""
思路：
法一："从答案出发"的思想
首先创建ans与path用于存储。对于"从答案出发"的思想，采用从大数开始选择会比较好，
先设置一个dfs(i)的递归函数，然后设置一个for j in range(i, 0, -1)的循环，
由于题目为从[1, n]进行选择，因此终止条件为0，是选不到的，然后将其添加进path中，
dfs(j-1)，最后恢复现场即可。终止条件就是当len(path)==k时，将path添加进
ans中即可。此外，还可以进行剪枝操作，记d=k-len(path)，代表还需要选d个数，
但若i<d时，则无法再选了，比如还需选1个数，此时i=1时就必须选了，若更小则无法满足。

法二："选与不选"的思想
首先创建ans与path用于存储。对于"选与不选"的思想，在组合问题中，从大数开始倒着
递归会比较好，对于"选与不选"的方法，要先处理不选的情况，然后再处理选的情况。
设置一个dfs(i)的递归函数，对于不选，则dfs(i-1)即可，但是要加一个if i > d的条件，
如果不加这个条件，则在最开始要加一个if i < 0则return的条件，对于第一个条件，这
代表是否还能进行不选的资格，比如还需选2个数，则刚需i至少为3，否则没有不选的资格。
对于选，则将i添加进path，然后递归dfs(i-1)，然后恢复现场即可。

时间复杂度：O(k*C(n, k))。对于组合问题，时间复杂度的公式为叶子数乘以路径长度
空间复杂度：O(k)
"""
from typing import List

class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        # 法一
        # ans = []
        # path = []

        # def dfs(i):
        #     d = k - len(path)
        #     if i < d:
        #         return
        #     if len(path) == k:
        #         ans.append(path.copy())
        #         return
        #     for j in range(i, 0, -1):
        #         path.append(j)
        #         dfs(j-1)
        #         path.pop()

        # dfs(n)
        # return ans

        # 法二
        ans = []
        path = []

        def dfs(i):
            d = k - len(path)
            if d == 0:
                ans.append(path.copy())
                return
            if i > d:
                dfs(i-1)

            path.append(i)
            dfs(i-1)
            path.pop()

        dfs(n)
        return ans
```

2.[216. 组合总和 III](https://leetcode.cn/problems/combination-sum-iii/)

```python
"""
思路：
继上一题的两种思路，只需添加path数组的和是否等于n的判断条件即可。可以增加一个target
参数到dfs函数中，target代表距离n还差多少的数，当target小于0时，说明path的和已超过
target，没有继续的必要，用于剪枝，不加这个参数进行剪枝算法也可以成立。

时间复杂度：O(k*C(9, k))
空间复杂度：O(k)
"""
from typing import List

class Solution:
    def combinationSum3(self, k: int, n: int) -> List[List[int]]:
        # 法一
        # ans = []
        # path = []

        # def dfs(i, target):
        #     if target < 0:
        #         return
        #     d = k - len(path)
        #     if i < d:
        #         return
        #     if len(path) == k:
        #         if sum(path) == n:
        #             ans.append(path.copy())
        #             return

        #     for j in range(i, 0, -1):
        #         path.append(j)
        #         dfs(j-1, target-j)
        #         path.pop()

        # dfs(9, n)
        # return ans

        # 法二
        ans = []
        path = []

        def dfs(i, target):
            if target < 0:
                return
            if len(path) == k:
                if sum(path) == n:
                    ans.append(path.copy())
                return

            d = k - len(path)
            if i > d:
                dfs(i-1, target)

            path.append(i)
            dfs(i-1, target-i)
            path.pop()

        dfs(9, n)
        return ans
```

3.[22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)

```python
"""
思路：
这题用"选与不选"的思想会好处理一点。选就代表选左括号，不选就代表选右括号，
问题的关键是，为了确保括号组合有效，我们需要左括号的个数始终大于等于右括号
的个数，比如当我们已经有了一个'()'，需要在下一个位置选择时，这时我们必须
选择左括号，否则该括号字符串就无效了。因此我们设计一个dfs递归函数，传入
i和open，open就代表左括号的个数，当i==2*n时，代表构造完成，添加进ans即可，
当选左括号时，记得加上open<n的条件，因为左括号个数再多，也只能为n，然后
递归遍历dfs，然后是选右括号的情况，仅当右括号的个数小于左括号个数时，
才能选右括号，然后递归遍历即可。

时间复杂度：O(n*C(2n,n))。相当于2n个位置选n个位置填入左括号，其中利用"技巧"控制括号字符串有效
空间复杂度：O(n)
"""
from typing import List

class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        m = 2 * n
        ans = []
        path = [''] * m

        def dfs(i, open):
            if i == m:
                ans.append(''.join(path))
                return
            if open < n:
                path[i] = '('
                dfs(i+1, open+1)

            if i - open < open:
                path[i] = ')'
                dfs(i+1, open)

        dfs(0, 0)
        return ans
```

4.[39. 组合总和](https://leetcode.cn/problems/combination-sum/)

```python
"""
思路：
法一："选与不选"的思想
首先设置一个ans和path用于存储。然后设置一个dfs递归函数，传入i和left，left代表剩余数，
当left为0时，将path添加进ans中，当i为n或left<0时，返回，对于"选与不选"的思想，先处理
不选的情况，再处理选的情况，记得恢复现场即可。
法二："从答案出发"的思想
首先设置一个ans和path用于存储。然后设置一个dfs递归函数，传入i和left，left代表剩余数，
当left为0时，将path添加进ans中，当i为n或left<0时，返回，对于"从答案出发"的思想，要
设置一个for j的遍历，从i到n，进行添加，递归，恢复现场的操作

时间复杂度：O(n*logn+((e^π*((2/3)*target))^(1/2))/target)。记target为前target项
空间复杂度：O(target)
"""
from typing import List

class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        # 法一
        # ans = []
        # path = []

        # def dfs(i, left):
        #     if left == 0:
        #         ans.append(path.copy())
        #         return
        #     if i == len(candidates) or left < 0:
        #         return

        #     dfs(i+1, left)

        #     path.append(candidates[i])
        #     dfs(i, left - candidates[i])
        #     path.pop()

        # dfs(0, target)
        # return ans

        # 法二
        ans = []
        path = []

        def dfs(i, left):
            if left == 0:
                ans.append(path.copy())
                return
            
            if i == len(candidates) or left < 0:
                return
            
            for j in range(i, len(candidates)):
                path.append(candidates[j])
                dfs(j, left-candidates[j])
                path.pop()

        dfs(0, target)
        return ans
```

### 回溯-排列型

1.[46. 全排列](https://leetcode.cn/problems/permutations/)

```python
"""
思路：
首先设置ans用于存储最终答案，设置path用于存储临时答案，对于path，我们只需要
关注其每个位置，是否要选nums里的其中一个数字，因此我们还需要一个on_path的布尔
数组来记录nums中的某个数是否已被选过。设置一个dfs函数，边界条件是当i==length时，
将path的copy添加进ans中，为了避免选择的不重复性，因此我们是对on_path数组进行遍历，
若取到的元素不为False时，即该数还未被选过时，我们可以把它赋值给path数组内，然后标记
该数已被选过，递归遍历，恢复现场即可。

时间复杂度:O(n*n!)。全排列节点个数为n!个，每个节点复制花费O(n)的时间
空间复杂度:O(n)
"""
from typing import List

class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        ans = []
        length = len(nums)
        path = [0] * length
        on_path = [False] * length

        def dfs(i):
            if i == length:
                ans.append(path.copy())
            
            for j, x in enumerate(on_path):
                if not on_path[j]:
                    path[i] = nums[j]
                    on_path[j] = True
                    dfs(i+1)
                    on_path[j] = False
            
        dfs(0)
        return ans
```

2.[51. N 皇后](https://leetcode.cn/problems/n-queens/)

```python
"""
思路：
对于N皇后问题，需要进行问题转换，由于N皇后不允许皇后出现在同行同列同斜线，
首先看待同行同列如何不满足，实际上其等价于一个全排列的问题，不考虑行号，
只考虑列号，若不满足同行同列，则实际上是关于列号的全排列，所以问题就转换成了，
我们先拿到0-(n-1)的全排列，然后再判断这个全排列的列表是否能不满足同斜线的问题。
值得注意的是，对于斜线，有正斜线和反斜线，正斜线的规律是行号+列号是相同的，
反斜线的规律是行号-列号是相同的，借助这两个规则我们就能判断是否不满足斜线的问题。
在代码中，由于我们是一个皇后一个皇后的放置，因此我们的代码的考虑模式是，我们
从0行开始放，那么肯定不会出现同行，然后若我们放的列已经被放过皇后，则我们不放，
进阶的，若我们放皇后的地方符合我们的两个规则，则我们不放，由此我们可以写出递归函数。
首先我们设置一个ans用于存储，然后设置一个quene用于存放每行实际放置皇后的列号，
设置一个col = [False] * n用于存放某列是否被选过，设置check1 = [False] * 2 * n
用于记录行号加列号的值是否出现过，因为我们最多需要记录到(n-1) + (n-1)的情况，
因此我们需要至少设置一个长为2n-1的数组，check2也是同理，python有负索引，因此
同样至少设置一个长为2n-1的数组即可。然后是写一个dfs遍历，i代表遍历的第i行，
边界条件是，当i==n时，我们可以添加临时答案了，设置一个temp列表，遍历queue
将每行代表的字符串添加进temp中，然后再将temp添加进ans即可。然后是遍历col列表，
i代表当前的处理行号，当第j列没被选时，当i+j在check1数组中不冲突时，当i-j在
check2数组中不冲突时，执行queue[i] = j的操作，然后将col，check1，check2的对应
标记设为True，然后递归遍历dfs(i+1)，然后恢复现场，这里quene[i]恢不恢复无所谓，
后面会被覆写。

时间复杂度：O(n^2*n!)。全排列节点数为n!，每次复制答案花费O(n^2)时间
空间复杂度：O(n)
"""
from typing import List

class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        ans = []
        quene = [0] * n
        col = [False] * n
        check1 = [False] * 2 * n
        check2 = [False] * 2 * n

        def dfs(i):
            if i == n:
                temp = []
                for j, x in enumerate(quene):
                    temp.append("." * x + "Q" + "." * (n - x - 1))
                ans.append(temp)
                return
            for j, x in enumerate(col):
                if not col[j] and not check1[i+j] and not check2[i-j]:
                    quene[i] = j
                    check1[i+j] = True
                    check2[i-j] = True
                    col[j] = True
                    dfs(i+1)
                    check1[i+j] = False
                    check2[i-j] = False
                    col[j] = False
        
        dfs(0)
        return ans
```

3.[52. N 皇后 II](https://leetcode.cn/problems/n-queens-ii/)

```python
"""
思路：
同51上一题N皇后一致，将边界条件改为ans+1即可

时间复杂度:O(n*n!)。全排列节点数为n!，添加一个答案要遍历col，复杂度为O(n)，
而对于前一题还需要构造答案，又多一个O(n)
空间复杂度:O(n)
"""
class Solution:
    def totalNQueens(self, n: int) -> int:
        ans = 0
        # queue = [0] * n
        col = [False] * n
        check1 = [False] * 2 * n
        check2 = [False] * 2 * n

        def dfs(i):
            nonlocal ans
            if i == n:
                ans += 1
                return
            
            for j, x in enumerate(col):
                if not col[j] and not check1[i+j] and not check2[i-j]:
                    # queue[i] = j
                    col[j] = True
                    check1[i+j] = True
                    check2[i-j] = True
                    dfs(i+1)
                    col[j] = False
                    check1[i+j] = False
                    check2[i-j] = False
        
        dfs(0)
        return ans
```

4.[2850. 将石头分散到网格图的最少移动次数](https://leetcode.cn/problems/minimum-moves-to-spread-stones-over-grid/)

```python
"""
思路：
根据题意，实际上是将多石头的地方移动到没有石头的地方，进而转化为，
我们记录多石头的位置坐标和无石头的位置坐标，对于多石头的地方，每
多一个石头，我们都重复记录一次，最终这两个列表实际上长度是相等的，
由此问题转化为两个列表元素的一一对应的选择问题，即将A列表进行全排列
的问题。对于将A列表全排列，可以用itertools中的permutations进行操作，
得到的每个元素都是一个新的全排列的序列列表，然后将其一一对应的坐标进行
距离计算，最后进行比较即可。

时间复杂度:O(mn*(mn)!)。m为行数，n为列数，全排列数为(mn)!，对于
计算的复杂度为O(mn)
空间复杂度:O(mn)
"""
from typing import List
import math
from itertools import permutations

class Solution:
    def minimumMoves(self, grid: List[List[int]]) -> int:
        ans = math.inf
        from_point = []
        to_point = []
        for i, row in enumerate(grid):
            for j, x in enumerate(row):
                if x > 1:
                    from_point.extend([(i, j)] * (x - 1))
                elif x == 0:
                    to_point.append((i, j))

        for i in permutations(from_point):
            total = 0
            for (x1, y1), (x2, y2) in zip(i, to_point):
                total += abs(x1 - x2)
                total += abs(y1 - y2)
            ans = min(ans, total)

        return ans
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
