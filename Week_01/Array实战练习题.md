1. https://leetcode-cn.com/problems/container-with-most-water/ 

2. https://leetcode-cn.com/problems/move-zeroes/
3. https://leetcode-cn.com/problems/climbing-stairs/
4.   (高频老题)



## [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

解题思路：

* **算法流程：** 设置双指针 l,r 分别位于容器壁两端，根据规则移动指针（后续说明），并且更新面积最大值 `area`，直到 `l == r` 时返回 `area`。
* x 轴一开始就是最大的，若想让area增大，就要增大两边更小的高度，比较height[i]和height[j]，哪个值更小就内敛。

```java
class Solution {
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1;
        int area = 0;
        while(l < r){
            area = height[l] < height[r] ? Math.max(area, (r - l) * height[l++]) 
                : Math.max(area, (r - l) * height[r--]);  
        }
        return area;
    }
}
```

- [x] 4.21
- [x] 4.22
- [x] 4.23
- [x] 5.23
- [x] 5.25
- [x] 6.3

## [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/)

解题思路：

* **算法流程：**设置快慢指针i 和 j ，i 用于遍历数组，当nums[i]不为0时，以j为索引存放在数组，最后把i != j 的设为0。 

```java
class Solution {
    public void moveZeroes(int[] nums) {
        int j = 0;
        for(int i = 0; i < nums.length; ++i){
            if(nums[i] != 0){
                nums[j] = nums[i];
                if(i != j){
                    nums[i] = 0;
                }
                j++;
            }
        }
    }
}
```

- [x] 4.21
- [x] 4.22
- [x] 4.23
- [x] 5.23
- [x] 5.25
- [x] 6.3

#### [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

解题思路：

* 

```java
class Solution {
    public int climbStairs(int n) {
        int pre = 1, preOfPre = 1, cur = 1;
        for(int i = 2; i <= n; i++) {
            cur = pre + preOfPre;
            preOfPre = pre;
            pre = cur;
        }
        return cur;
    }
}
```

- [x] 4.21
- [x] 4.22
- [x] 4.23
- [x] 5.23
- [x] 5.25
- [x] 6.3

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)



```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> lists = new ArrayList<>();
        Arrays.sort(nums);
        int l, r, sum;
        for (int i = 0; i < nums.length; ++i) {
            if (nums[i] > 0) break;
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            l = i + 1; r = nums.length - 1;
            while (l < r) {
                sum = nums[i] + nums[l] + nums[r];
                if (sum == 0) {
                    lists.add(Arrays.asList(nums[i], nums[l++], nums[r--]));
                    while(l < r && nums[l] == nums[l - 1]) l++;
                    while(l < r && nums[r] == nums[r + 1]) r--;
                }
                if (sum < 0) l++;
                if (sum > 0) r--;
            }
        }
        return lists;
    }
}
```

- [x] 4.22
- [x] 4.23
- [x] 5.23
- [x] 5.25
- [x] 6.3