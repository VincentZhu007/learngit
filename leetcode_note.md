# LeetCode笔记

## 1 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的 两个 整数。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

**示例:**

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```



### 1.1 暴力穷举法

|    循环    |     循环内部操作      |
| :--------: | :-------------------: |
| 循环$i=2$  |  比较$target-2 = 7?$  |
|            | 比较$target-2 = 11?$  |
|            |  比较$target-2 =15?$  |
| 循环$i=7$  | 比较$target-7 = 11?$  |
|            | 比较$target-7 = 15?$  |
| 循环$i=11$ | 比较$target-11 = 15?$ |

Java代码实现

```java
class Solution {
    public int[] twoSum(int[] nums, int target) 
    {
        int a, b;
        int i=0, j=0;
        for ( i=0; i<nums.length; i++ )
        {
            a = nums[i];
            b = target - a;
            for ( j=i+1; j<nums.length; j++ )
            {
                if( b == nums[j] )
                    break;
            }
            if ( j != nums.length ) // 找到了对应的b
                break;           
        }
        return new int[]{i,j};
    }
}
```

提交运行结果

> 执行用时: 22 ms, 在Two Sum的Java提交中击败了65.70% 的用户

计算次数为 $\frac{n \times (n+1)}{2}$，**计算复杂度** 为$O(n^2)$



### 1.2 哈希表法

将数组元素作为键存入哈希表，对应的哈希值就是该元素在数组中的索引，通过查询哈希表即可消除内层循环。

|    循环    |   循环内部操作   |
| :--------: | :--------------: |
| 循环$i=2$  | 查找$j=target-2$ |
| 循环$i=7$  | 查找$j=target-2$ |
| 循环$i=11$ | 查找$j=target-2$ |

Java代码实现

```java
class Solution {
    public int[] twoSum(int[] nums, int target) 
    {
        HashMap<Integer, Integer> hashMap = new HashMap<>((int)(nums.length/0.75));
        for ( int i=0; i<nums.length; i++ )
        {
            hashMap.put(nums[i], i);     
        }
        int res = 0;
        int j=0, k=0;
        for ( j=0; j<nums.length; j++ )
        {
            res = target - nums[j];
            if( hashMap.containsKey(res) == true && hashMap.get(res) != j ) 
            {
                k = hashMap.get(res);
                break;
            } 
        }
        return new int[]{j, k};
    }
}
```

**注意**：对于nums=[3,3], target=6这种具有相同元素的情况，构建HashMap时nums[1]的键值对会覆盖掉nums[0]的键值对。

提交运行结果

> 执行用时: 9 ms, 在Two Sum的Java提交中击败了85.27% 的用户

假设HashMap操作复杂度为$O(1)$，则总的**计算复杂度** 为$O(n)$



### 1.3 一遍哈希表

1.2节的思路是将数组转换成哈希表存储，利用哈希表的常数时间查找优势来降低计算量，但这道题里面的两数加和$a+b$具有对称性(a为数组索引较小的元素，b为数组索引较大的元素)，可以固定$a$来查找$b$，这就是前两种方法的思路，也可以固定$b$来查找$a$，从这个角度出发可以边固定$b$进行查找边存储$a$。

循环步骤：

1. 选择$b=target-num[i]$，对$b$进行哈希查找，如果找到，表示对应的$a$已经存入哈希表，并且在nums中的索引小于$i$，算法结束；
2. 如果未找到，表明$a$还未存入哈希表，当前的$num[i]$可以作为新的可能$a$值继续存入哈希表中。

Java代码实现

```java
class Solution {
    public int[] twoSum(int[] nums, int target) 
    {	
        int res = 0;
        int index_a = 0, index_b = 0;
        HashMap<Integer, Integer> hashMap = new HashMap<>((int)(nums.length/0.75));
        hashMap.put(nums[0], 0);  
        for ( int i=1; i<nums.length; i++ )
        {
            res = target - nums[i];
            if ( hashMap.containsKey(res) == true )
            {
                index_a = hashMap.get(res);
                index_b = i;
                break;
            }
            else
            	hashMap.put(nums[i], i);     
        }
        return new int[] {index_a, index_b};
    }
}
```

**注意**：对于nums=[3,3], target=6这种具有相同元素的情况，因为在存入$nums[1]$之前就会先通过哈希查找得到结果[0,1]，因此不需要额外处理。

提交运行结果

> 执行用时: 7 ms, 在Two Sum的Java提交中击败了92.26% 的用户

假设HashMap操作复杂度为$O(1)$，则总的**计算复杂度** 为$O(n)$

