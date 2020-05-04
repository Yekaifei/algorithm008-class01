1. https://leetcode-cn.com/problems/container-with-most-water/ 

2. https://leetcode-cn.com/problems/move-zeroes/
3. https://leetcode-cn.com/problems/climbing-stairs/
4. https://leetcode-cn.com/problems/3sum/ (高频老题)



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

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)



```java
class Solution {
    public static List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList();
        int len = nums.length;
        if(nums == null || len < 3) return ans;
        Arrays.sort(nums); // 排序
        for (int i = 0; i < len ; i++) {
            if(nums[i] > 0) break; // 如果当前数字大于0，则三数之和一定大于0，所以结束循环
            if(i > 0 && nums[i] == nums[i-1]) continue; // 去重
            int L = i+1;
            int R = len-1;
            while(L < R){
                int sum = nums[i] + nums[L] + nums[R];
                if(sum == 0){
                    ans.add(Arrays.asList(nums[i],nums[L],nums[R]));
                    while (L<R && nums[L] == nums[L+1]) L++; // 去重
                    while (L<R && nums[R] == nums[R-1]) R--; // 去重
                    L++;
                    R--;
                }
                else if (sum < 0) L++;
                else if (sum > 0) R--;
            }
        }        
        return ans;
    }
}
```

- [x] 4.22
- [x] 4.23