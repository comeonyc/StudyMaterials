# LeetCode刷题笔记
- [LeetCode刷题笔记](#leetcode刷题笔记)
  - [0、基础算法](#0基础算法)
    - [0.1 快速排序](#01-快速排序)
    - [0.2 单调栈](#02-单调栈)
  - [1、HOT 100](#1hot-100)
    - [1.1 HOT100-LC3-无重复最长子串](#11-hot100-lc3-无重复最长子串)
    - [1.2 HOT100-LC4 寻找两个正序数组中的中位数](#12-hot100-lc4-寻找两个正序数组中的中位数)
    - [1.3 HOT100-LC10-正则表达式匹配](#13-hot100-lc10-正则表达式匹配)
    - [1.4 HOT100-LC11-盛最多水的容器](#14-hot100-lc11-盛最多水的容器)
    - [1.5 HOT100-LC15-三数之和](#15-hot100-lc15-三数之和)
    - [1.6 HOT100-LC26-合并K个升序链表](#16-hot100-lc26-合并k个升序链表)
    - [1.7 HOT100-LC31-下一个排列](#17-hot100-lc31-下一个排列)
    - [1.7 HOT100-LC33-搜索旋转排序数组](#17-hot100-lc33-搜索旋转排序数组)
    - [1.8 HOT100-LC42-接雨水](#18-hot100-lc42-接雨水)
    - [1.9 HOT100-LC55-跳跃游戏](#19-hot100-lc55-跳跃游戏)
    - [1.10 HOT100-LC72-编辑距离](#110-hot100-lc72-编辑距离)
  - [2.ByteDance](#2bytedance)

## 0、基础算法
### 0.1 快速排序
* 算法思路（以升序讲解）
  * 指定一个标识位，找到标识位的位置，把标识位移到哪里，然后重复移动左区间和右区间，即可
  * 如何找？将比他大的移动到右边，将比他小的移动到左边，直至左右重合就是它的位置
* 代码实现要点
  * 如果以最左边为基准，升序时要先找比它大的（先移动右指针）
  * 如果以最左边为基准，降序要先找比它小的（先移动左指针）-->代证明
  * **原因**
    * 反证。
    * 升序时，如果先找比它小的，则相当于l向r移动。最后移动到r的时候，r位置的数一定比最左边的数要大，那么最后移动时相当于把比这个大的数移动过去了，造成排序失败
* 代码
```java
private void quickSort(int[] nums, int left, int right) {
    if (left >= right) {
        return;
    }

    int index = nums[left];
    int l = left, r = right;
    while (l < r) {
        while (l < r && nums[r] > index) {
            r--;
        }
        while (l < r && nums[l] <= index) {
            l++;
        }
        swap(nums, l, r);
    }
    nums[left] = nums[l];
    nums[l] = index;
    quickSort(nums, left, l - 1);
    quickSort(nums, l + 1, right);
}
```

### 0.2 单调栈
* 定义(从入栈顺序视角)
  * 单调递增栈：从 **栈底到栈顶** 严格递增
  * 单调递减栈：从 **栈底到栈顶** 严格递减
* 使用场景
  * 递增栈使用场景
    * 从栈顶角度分析，可以寻找下一个比它小的元素
    * 从遍历的当前元素看，
      * 从左到右依次遍历，可以寻找左侧第一个比它小的元素
      * 从右到左依次遍历，可以寻找右侧第一个比它小的元素
  * 递减栈使用场景
    * 从栈顶角度分析，可以寻找下一个比它大的元素
    * 从遍历的当前元素看，
      * 从左到右依次遍历，可以寻找左侧第一个比它大的元素
      * 从右到左依次遍历，可以寻找右侧第一个比它大的元素
* 递增栈场景举例
  * [1,3,4,5,2,9,6]
    * step1:i=0, nums[i]=1, 栈空, 入栈[1]
    * step2:i=1, nums[i]=3, $stack[peek]<=nums[i]$, 入栈[1,3]
      * 栈顶看stack[peek]=1, 当前nums[i]=3,为此找不到比栈顶第一个小的元素，待找
      * 当前nums[i]=3,栈顶stack[peek]=1,比nums[i]小，所以左侧第一个比它小元素是1
    * step3:i=2, nums[i]=4, $stack[peek]<nums[i]$, 入栈[1,3,4]
      * 栈顶看stack[peek]=3, 当前nums[i]=4,为此找不到比栈顶第一个小的元素，待找
      * 当前nums[i]=4,栈顶stack[peek]=3,比nums[i]小，所以左侧第一个比它小元素是3
    * step4:i=3, nums[i]=5, $stack[peek]<nums[i]$, 入栈[1,3,4,5]
      * 栈顶看stack[peek]=4, 当前nums[i]=5, 为此找不到比栈顶第一个小的元素，待找
      * 当前nums[i]=5,栈顶stack[peek]=4,比nums[i]小，所以左侧第一个比它小元素是4
    * step5:i=5, nums[i]=2, $stack[peek]>nums[i]$, 入栈[1,2]
      * 栈顶看stack[peek]=5, 当前nums[i]=2, 比栈顶元素小, 为维护单调栈性质，需要把栈中比2大的都探出去
        * 从栈遍历, 此时栈顶是5, 比当前元素nums[i]大, 故第一个比它小的就是nums[i]=2, 故比5大的第一个元素就是2, 将5弹出, 栈[1,3,4]
        * 继续遍历, 此时栈顶是4, 比当前元素nums[i]大, 故第一个比它小的就是nums[i]=2，故比4大的第一个元素就是2,将4弹出,栈[1,3]
        * 继续遍历, 此时栈顶是3, 比当前元素nums[i]大, 故第一个比它小的就是nums[i]=2，故比3大的第一个元素就是2,将3弹出,栈[1]
        * 继续遍历, 此时栈顶是1, 比当前元素nums[i]小, 故找不到比栈定当前元素小的数, 但是从当前元素nums[i]看, 第一个比当前元素nums[i]小的元素就是1
    * 后面依次类推

* * *

## 1、HOT 100
### 1.1 HOT100-LC3-无重复最长子串
* 题目描述
  * 给定一个字符串s，找出其中不含邮重复字符的 **最长子串** 长度
* 解题方案
  * 滑动窗口+哈希表
    * 窗口记录无重复子串
    * 哈希表用于快速寻找到上一个相同字符的位置
  * 动态规划+哈希表
* 滑动窗口+哈希表代码
  * 注意:hashmap的容量不要错误指定，否则会提高时间复杂度-->原因是hashmap用迭代器删除时，next操作会遍历hashmap的底层数组，数组越大，删除操作的复杂度就越高
```java
public int lengthOfLongestSubstring(String s) {
    if (Objects.isNull(s) || s.length() == 0) {
        return 0;
    }
    int right = 0;
    Map<Character, Integer> char2Index = new HashMap<>();
    int max = 0;
    while (right < s.length()) {
        char c = s.charAt(right);
        if (char2Index.containsKey(c)) {
            max = Math.max(char2Index.size(), max);
            int left = char2Index.get(c);
            char2Index.entrySet().removeIf(x->x.getValue()<=left);
        }
        char2Index.put(c, right);
        right++;
    }
    return Math.max(char2Index.size(), max);
}
```
* 动态规划+哈希表
  * dp方程如下：
    * dp[i] 表示的是 以i为结尾的 最长子串长度；用j来表达跟上一个i相等的字符，即 **s[i] == s[j]**
    * 如果前面的字符串没有满足 s[i]==s[j],则dp[i] = dp[i-1] + 1;
    * 如果有满足 s[i]==s[j],要看j的位置，在(i-1)包含的最长无重复子串包不包含j
      * 如果包含j,则dp[i] = i-(j+1)+1
      * 如果不包含j,则dp[i] = dp[i-1]+1
    * 边界情况：当i=0时，dp[0] = 1恒成立。
  * 上述难点是找到j,所以可以用hashmap来记录j的位置；
  * 根据动态方程可以看出，dp[i]只跟dp[i-1]有关，所以可以用变量tmp来替代
  * 具体代码如下
```java
public int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> indexMap = new HashMap<>();
    int tmp = 0, max = 0;
    for (int i = 0; i < s.length(); i++) {
        char cur = s.charAt(i);

        //to write dynamic planning
        if (i == 0) {
            tmp = 1;
        } else if (indexMap.containsKey(cur)) {
            int lastIndex = indexMap.get(cur);
            if (tmp >= i - lastIndex) {
                //contain last same char
                tmp = i - (lastIndex + 1) + 1;
            } else {
                tmp = tmp + 1;
            }
        } else {
            tmp = tmp + 1;
        }

        max = Math.max(tmp, max);
        indexMap.put(cur, i);
    }
    return max;
}
```

### 1.2 HOT100-LC4 寻找两个正序数组中的中位数
* 题目描述
  * 给定两个正序数组，寻找这两个有序数组的中位数；并要求时间复杂度log(m+n)
  * [1,2],[3,4] --> 2.5000
  * [1,3],[2] --> 2.0
* 解题分析
  * 由于时间复杂度要求log级别，所以会想到利用 **二分法** 来解决
  * 当m+n是奇数时，中间的数即为所求
  * 当m+n时偶数时，中间两数之和除2即位所求
  * 所以可以看出，该题的本质是两个有序数组找第k大的数（和第k+1大的数即可），然后要利用二分法来控制时间复杂度
* 解题思路
  * 可以从nums1中找m1个元素，那么在nums2中找k-m1个元素即可
  * m1的寻找可以用二分法来排除，如下所示
    * 让left指向nums1的头部，让right指向num2的尾部
    * 取 **m1 = left+(rigt-left)/2** , 则 **m2=k-m1**
    * 如果nums1[m1]<nums[m2],那么可以说明nums1数组的前m1的元素都是在前k大的数中，**不是第k大的那个数**，此时要让left=m1+1
    * 相反如果nums[m1]>=nums[m2],说明要在细化的看一下(left~m1)之间的区间，没办法直接排除，此时令right=m1即可
    * 如果m2的长度超过了数组的长度，则说明nums1中的元素取的不够，要多取一些，令left=m1+1
  * 最后从m1取几个元素，从m2中取几个元素就确定了
    * 如果m1=0，则说明m1中的元素过大，不包含第k大，要从nums2中取
    * 如果m2=0，则说明m2中的元素过大，不包含第k大，要从nums1中取
    * 否则，判断一下nums1中第m1个元素和nums2中第m2个元素，谁大哪个就是第k大的数
  * 代码如下
```java
public  double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int len1 = nums1.length;
    int len2 = nums2.length;
    int k = (len1 + len2 + 1) / 2;
    if ((len1 + len2) % 2 == 0) {
        return (findK(nums1, nums2, k) + findK(nums1, nums2, k + 1)) * 0.5;
    } else {
        return findK(nums1, nums2, k);
    }
}

public int findK(int[] nums1, int[] nums2, int k) {
    int len1 = nums1.length;
    int len2 = nums2.length;

    if (len1 > len2) {
        //因为是以nums1为依据，用nums2来补剩下的数，所以保证nums2的长度更长一些，防止下面发生越界
        return findK(nums2, nums1, k);
    }

    int left = 0;
    int right = len1;
    while (left < right) {
        int m1 = left + (right - left) / 2;
        int m2 = k - m1;
        if (m2 > len2) {
            left = m1 + 1;
        } else if (nums1[m1] <= nums2[m2 - 1]) {
            left = m1 + 1;
        } else {
            right = m1;
        }
    }

    int m1 = left;
    int m2 = k - m1;

    if (m1 == 0) {
        //代表m1中没有能用的，取m2即可
        return nums2[m2 - 1];
    } else if (m2 == 0) {
        //代表m2很大，取m1即可
        return nums1[m1 - 1];
    } else {
        return Math.max(nums1[m1 - 1], nums2[m2 - 1]);
    }
}
```
### 1.3 HOT100-LC10-正则表达式匹配
* 题目描述
  * 给定字符串s和p，实现一个支持 '.' 和 '*' 的正则表达式匹配
* 解题思路：
  * 递归
  * 动态规划
* 递归
  * 遍历两个字符串，判断两个字符是否相等或p是否是'.'
    * 如果相等，要判断一下是单一匹配，还是*匹配
    * 如果p+1是*，则进行*匹配
  * 如果当前两个遍历位置不相等，判断p+1是不是'*'
    * 如果是，则可以匹配0次
    * 如果不是，则不匹配
  * 代码
  ```java
    public boolean isMatch(String s, String p) {
        return match(s.toCharArray(), p.toCharArray(), 0, 0);
    }

    private boolean match(char[] s, char[] p, int sIndex, int pIndex) {
        if (sIndex == s.length && pIndex == p.length) {
            return true;
        }

        if (sIndex < s.length && pIndex < p.length && canEqual(s, p, sIndex, pIndex)) {
            if (pIndex + 1 < p.length && p[pIndex + 1] == '*') {
                //match zero / multi
                return match(s, p, sIndex, pIndex + 2)  || match(s, p, sIndex + 1, pIndex);
            } else {
                //match one
                return match(s, p, sIndex+1, pIndex + 1);
            }
        } else if (pIndex + 1 < p.length && p[pIndex + 1] == '*') {
            return match(s, p, sIndex, pIndex + 2);
        } else {
            return false;
        }
    }

    private boolean canEqual(char[] s, char[] p, int sIndex, int pIndex) {
        return (sIndex < s.length && pIndex < p.length && (s[sIndex] == p[pIndex])) || (pIndex < p.length && p[pIndex] == '.');
    }
  ```
* 动态规划
  * 定义二维数组dp[i][j],表示0-i的s子串和0-j的子串p是否满足匹配
  * 那么dp方程如下：
    * 如果s[i]=p[j]-->dp[i][j] = dp[i-1][j-2]
    * 如果上述不成立，判断p[j]是不是*，如果是*有机会做多次或0次匹配
      * 如果s[i-1]=p[j-1] || p[j-1] == '.'，
        * 则有机会进行多次匹配，dp[i][j] = dp[i-1][j],也就是让p[j-1]* 这个字符在顶替一下
        * 也可以不匹配，dp[i][j] = dp[i][j-2],让p[j-1]*不匹配s
      * 如果s[i-1] !=p[j-1] && p[j-1] == '.',则只能让这个* 不匹配p[j-1],要看下不匹配是否满足dp[i][j] = dp[i][j-2]
  * 初始化
    * 让dp[s.length()+1][p.length()+1]的范围比s和p的长度大一，这样可以减少dp转移方程时的越界判断量
    * 且dp[0][0]=true --> 两个空串恒匹配
    * 如果想dp[0][j]=true，那么一定是用重复的.*来稀释，所以判断有几个.*出现，*的index，则是true
  * 代码
```java
public boolean isMatch(String s, String p) {
    char[] ss = s.toCharArray();
    char[] pp = p.toCharArray();
    boolean[][] dp = new boolean[ss.length + 1][pp.length + 1];

    //init otherwise will array index overreach
    dp[0][0] = true;
    for (int j = 2; j <= pp.length; j += 2)
        dp[0][j] = dp[0][j - 2] && p.charAt(j - 1) == '*';

    for (int i = 1; i <= ss.length; i++) {
        for (int j = 1; j <= pp.length; j++) {
            if (canEqual(ss, pp, i - 1, j - 1)) {
                dp[i][j] = dp[i - 1][j - 1];
            } else if (pp[j - 1] == '*') {
                if (canEqual(ss, pp, i - 1, j - 2)) {
                    dp[i][j] = dp[i][j - 2] || dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i][j - 2];
                }
            } else {
                dp[i][j] = false;
            }
        }
    }

    return dp[ss.length][pp.length];
}

private boolean canEqual(char[] s, char[] p, int sIndex, int pIndex) {
    return (sIndex < s.length && pIndex < p.length && (s[sIndex] == p[pIndex])) || (pIndex < p.length && p[pIndex] == '.');
}
```
### 1.4 HOT100-LC11-盛最多水的容器
* 题目描述
  * 给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。
  * 找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。
* 解题思路
  * 双指针
    * 题目所求为下面公式的最大值：Math.min(height[right],height[left])*(right-left)
    * 比较left和right的高度，因为left和right是相遇运动，会让(right-left)变小，那么如果要让整个值变大，只能是淘汰低的，直至left和right相遇
  * 正确性证明
    * [原证明](https://leetcode.cn/problems/container-with-most-water/solutions/94102/on-shuang-zhi-zhen-jie-fa-li-jie-zheng-que-xing-tu)
    * 核心点在于每次比较高度，可以排除 **低高度** 的最大值，这样做相遇运动时不会丢数据
    * ![证明图片](./pic/lc11-proof.png "proof")
  * 代码
```java
public int maxArea(int[] height) {
    int left = 0, right = height.length - 1;
    int max = 0;
    while (left < right) {
        max = Math.max(max, (right - left) * Math.min(height[left], height[right]));
        if (height[left] > height[right]) {
            right--;
        } else {
            left++;
        }
    }
    return max;
}
```

### 1.5 HOT100-LC15-三数之和
* 题目描述
  * 给定一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k。同时还满足 nums[i] + nums[j] + nums[k] == 0 。
* 解题思路
  * 快排 + 双指针求解两数和
* 代码
```java
public List<List<Integer>> threeSum(int[] nums) {
    quickSort(nums, 0, nums.length - 1);
    List<List<Integer>> result = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] > 0) {
            break;
        }
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }

        int remain = -nums[i];
        int l = i + 1, r = nums.length - 1;
        while (l < r) {
            int sum = nums[l] + nums[r];
            if (sum == remain) {
                List<Integer> objects = new ArrayList<>(3);
                objects.add(nums[i]);
                objects.add(nums[l]);
                objects.add(nums[r]);
                result.add(objects);
                while (l < r && nums[l + 1] == nums[l]) {
                    l++;
                }
                l++;

                while (l < r && nums[r - 1] == nums[r]) {
                    r--;
                }
                r--;
            } else if (sum > remain) {
                r--;
            } else {
                l++;
            }
        }
    }
    return result;
}

private void quickSort(int[] nums, int left, int right) {
    if (left >= right) {
        return;
    }

    int index = nums[left];
    int l = left, r = right;
    while (l < r) {
        while (l < r && nums[r] > index) {
            r--;
        }
        while (l < r && nums[l] <= index) {
            l++;
        }
        swap(nums, l, r);
    }
    nums[left] = nums[l];
    nums[l] = index;
    quickSort(nums, left, l - 1);
    quickSort(nums, l + 1, right);
}

private void swap(int[] nums, int l, int r) {
    int tmp = nums[l];
    nums[l] = nums[r];
    nums[r] = tmp;
} 
```
### 1.6 HOT100-LC26-合并K个升序链表
* 题目描述
  * 给定K个升序链表，合并成一个升序链表
  * 有时间复杂度要求
* 解题思路：假定共有K个链表，N个节点
  * 暴力解法时间复杂度分析：平均每个节点会比较N-1次，总共时间复杂度是$N*(N-1)$，也就是$O(N^2)$
  * 分治法时间复杂度分析: 两两合并，将合并后的链表在进行合并，这样其中每个链表会比较$logK$次，共有K个链表，也就是$K*logK$次。每个链表平均有$N/K$个节点，故整体时间复杂度为：$(N/K)*K*logK = O(N*logK)$
* 代码
```java
public ListNode mergeKLists(ListNode[] lists) {
    if (null == lists || lists.length == 0) {
        return null;
    }
    if (lists.length == 1) {
        return lists[0];
    }

    int start = 0;
    int i = 0;
    int size = lists.length;

    while (size != 1) {
        while (i < size) {
            if (i + 1 < size) {
                lists[start++] = mergeTwo(lists[i], lists[i + 1]);
            } else {
                lists[start++] = lists[i];
            }
            i += 2;
        }
        size = start;
        i = 0;
        start = 0;
    }

    return lists[0];
}

public ListNode mergeTwo(ListNode l1, ListNode l2) {
    if (null == l1) {
        return l2;
    }

    if (null == l2) {
        return l1;
    }

    ListNode result = new ListNode();
    ListNode tmp = result;

    while (null != l1 && null != l2) {

        if (l1.val <= l2.val) {
            tmp.next = new ListNode(l1.val);
            l1 = l1.next;
        } else {
            tmp.next = new ListNode(l2.val);
            l2 = l2.next;
        }
        tmp = tmp.next;
    }

    if (null != l1) {
        tmp.next = l1;
    }

    if (null != l2) {
        tmp.next = l2;
    }

    return result.next;
}
```
  
### 1.7 HOT100-LC31-下一个排列
* 题目描述
  * 寻找字典序下的下一个排列，例如1，2，3-->下一个排列就是1，3，2
* 解题思路
  * 直观上看，如果一个排列是 **降序排列** ，那么不存在比当前排列更大的字典序排列，例如3，2，1
  * 故关键思路1:一定是从后往前看，那个升序的点才是核心点
  * 例如[1,3,4,7,6,5,2],一定是在4,7的位置才可能出现下一个排列
  * 并且从4的位置看，[7,6,5,2]没有比它更大的字典序
  * 如何根据这个位置进而找到整个排列的下个排列？ 从后往前遍历，找到比4大的那个位置，也就是5，并且将4和5交换，然后将[7,6,4,2]做升序排序后，就可以得到最下一个字典序最大的
  * 由于[7,6,4,2]肯定是降序的，故可以采取收尾交换排序的方式，减少时间复杂度
* 代码
```java
public void nextPermutation(int[] nums) {
    // step 1 : traverse array from end to start
    // find the flag pos where nums[i] > nums[i-1]
    int i = nums.length - 1;
    while (i - 1 >= 0 && nums[i] <= nums[i - 1]) {
        i--;
    }
    int left = i;

    // handle start case
    if (i == 0) {
        headToTailSort(nums, 0, nums.length - 1);
        return;
    }

    // step 2 : traverse array from end to left
    // find the number which more than nums[flag]
    i = nums.length - 1;
    while (i > left && nums[i] <= nums[left - 1]) {
        i--;
    }
    int right = i;

    // step 3 : swap nums[left],nums[right]
    swap(nums, left - 1, right);

    // step 4 : sort array from left to end, ensure asc order(skip:head to tail exchange)
    headToTailSort(nums, left, nums.length - 1);
}

private void headToTailSort(int[] nums, int start, int end) {
    int left = start, right = end;
    while (left < right) {
        swap(nums, left, right);
        left++;
        right--;
    }
}

private void swap(int[] nums, int a, int b) {
    int tmp = nums[a];
    nums[a] = nums[b];
    nums[b] = tmp;
}
```

### 1.7 HOT100-LC33-搜索旋转排序数组
* 题目描述
  * 整数数组 nums 按升序排列，数组中的值 互不相同 。
  * 给定一个 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。
* 解题思路
  * 定理一：只有在顺序区间内才可以通过区间两端的数值判断target是否在其中。
  * 定理二：判断顺序区间还是乱序区间，只需要对比 left 和 right 是否是顺序对即可，nums[left] <= nums[right]，顺序区间，否则乱序区间。
  * 定理三：每次二分都会至少存在一个顺序区间
  * 通过不断的用Mid二分，根据定理二，将整个数组划分成顺序区间和乱序区间，然后利用定理一判断target是否在顺序区间，如果在顺序区间，下次循环就直接取顺序区间，如果不在，那么下次循环就取乱序区间。
* 代码
```java
public int search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int middle = (left + right) / 2;
        if (target == nums[middle]) {
            return middle;
        }

        if (nums[middle] < nums[right]) {
            if (nums[middle] < target && target <= nums[right]) {
                left = middle + 1;
            } else {
                right = middle - 1;
            }
        } else {
            if (nums[left] <= target && target < nums[middle]) {
                right = middle - 1;
            } else {
                left = middle + 1;
            }
        }
    }
    return -1;
}
```

### 1.8 HOT100-LC42-接雨水
* 题目描述
  *  * 给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
* 解法
  * 按行求
  * 单调栈
  * 按列求
* 按行求
  * 主旨思想：求第 i 层的水，遍历每个位置，如果当前的高度小于 i，并且两边有高度大于等于 i 的，说明这个地方一定有水，水就可以加1
```java
public int trap(int[] height) {
    int max = 0;
    for (int i : height) {
        max = Math.max(i, max);
    }

    int sum = 0;
    for (int i = 1; i <= max; i++) {
        boolean start = false;
        int tmp = 0;
        for (int j : height) {
            if (start && j < i) {
                tmp++;
            }

            if (j >= i) {
                sum += tmp;
                tmp = 0;
                start = true;
            }
        }
    }
    return sum;
}
```
* 单调栈
  * 大致思想：如果数组呈降序排列，那一定没有水能接住，只有遇到升序的情况才可能接到水，故可以用一个**递减栈**
  * 遍历到当前元素，如果该元素比栈顶大，那么此时可以接到水。此时栈顶出是凹字形的最低点，比较次栈的和当前元素的大小即可获取能接住的高度
```java
public int trap(int[] height) {
    Stack<Integer> stack = new Stack<>();
    int sum = 0;

    for (int i = 0; i < height.length; i++) {
        while (!stack.isEmpty() && height[i] >= height[stack.peek()]) {
            int high = height[stack.pop()];
            if (stack.isEmpty()) {
                break;
            }
            int minHigh = Math.min(height[stack.peek()], height[i]);
            sum += (minHigh - high) * (i - stack.peek() - 1);
        }
        stack.push(i);
    }
    return sum;
}
```
* 按列求
  * 从每一列看，该列能接住多少水由其左侧及右侧的最高处决定
```java
public int trap(int[] height) {
    int[] maxLeft = new int[height.length];
    int[] maxRight = new int[height.length];

    for (int i = 1; i < height.length; i++) {
        maxLeft[i] = Math.max(maxLeft[i - 1], height[i - 1]);
    }
    for (int i = height.length - 2; i >= 0; i--) {
        maxRight[i] = Math.max(maxRight[i + 1], height[i + 1]);
    }

    int sum = 0;
    for (int i = 1; i < height.length - 1; i++) {
        int min = Math.min(maxLeft[i], maxRight[i]);
        if (min > height[i]) {
            sum += min - height[i];
        }
    }
    return sum;
}

//优化代码
public int trap(int[] height) {
    int len = height.length;
    int left=0,right=len-1,leftMax = height[0],rightMax = height[len-1];


    int result = 0;
    while(left<right){
        leftMax = Math.max(leftMax, height[left]);
        rightMax = Math.max(rightMax, height[right]);
        if (leftMax <rightMax) {
            // 左指针的leftMax比右指针的rightMax矮
            // 说明：左指针的右边至少有一个板子 > 左指针左边所有板子
            // 根据水桶效应，保证了左指针当前列的水量决定权在左边
            // 那么可以计算左指针当前列的水量：左边最大高度-当前列高度
            result += leftMax - height[left];
            ++left;
        } else {
            result += rightMax - height[right];
            --right;
        }
    }
    return result;
}
```
### 1.9 HOT100-LC55-跳跃游戏
* 题目描述
  * 给定非负整数数组 nums ，最初位于数组的第一个下标 。数组中的每个元素代表你在该位置可以跳跃的最大长度。判断你是否能够到达最后一个下标。
* 解题思路
  * 动态规划
  * 贪心
* 动态规划
  * 定义dp[i],代表i下标一次能跳跃的最大位置
  * 动态方程
    * 下标i一次能到达的最大位置右两点决定
      * 该位置跳跃的最远距离没有上一个下标跳的远：dp[i] = dp[i-1]
      * 该位置跳跃的最远距离比上一个下表跳的远 dp[i] = i+nums[i] 
    * 取上述两者的最大值即可 $dp[i] = Math.max(dp[i-1],nums[i]+i)$
  * 如何判断能否跳到最后？
    * 如果当前能跳的最大位置 大于数组长度，即可以 $dp[i]>nums.length$
    * 如果当前跳的最大位置 等于 该数组下标，也就是跳不动了，则不可以跳到最后 $dp[i]==i$
  * 代码如下
```java
public boolean canJump(int[] nums) {
    if (nums.length == 1) {
        return true;
    }
    if (nums[0] == 0) {
        return false;
    }
    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    int max = nums.length - 1;
    for (int i = 1; i < nums.length; i++) {
        dp[i] = Math.max(dp[i - 1], i + nums[i]);
        if (dp[i] >= max) {
            return true;
        }
        if (dp[i] == i) {
            return false;
        }
    }
    return false;
}
```
* 贪心
  * 记录当前能跳到的最大下标maxRight
  * 遍历数组，如果当前下标 $i<=maxRight$,一定说明可以走到这个下标处，此时更新一下maxRight，即$maxRight = Math.max(maxRight,i+nums[i])$
  * 如果走不到，则说明不能跳到最后
  * 如果当前maxRight比数组长度大，那么就可以
```java
public boolean canJump(int[] nums) {
    if (nums.length == 1) {
        return true;
    }
    if (nums[0] == 0) {
        return false;
    }

    int maxRight = 0;
    for (int i = 0; i < nums.length; i++) {
        if (maxRight >= i) {
            maxRight = Math.max(i + nums[i], maxRight);
        } else {
            return false;
        }

        if (maxRight >= nums.length - 1) {
            return true;
        }
    }
    return false;
}
```

### 1.10 HOT100-LC72-编辑距离
* 题目描述
  * 给定两个单词 word1 和 word2， 请返回将 word1 转换成 word2 所使用的最少操作数。
  * 每次操作对word1添加一个字符、删除一个字符、或替换一个字符
* 解题思路
  * 动态规划
* 动态规划
  * 1、定义dp[i][j] --> 代表（0,i）的word1子串转换成word2子串的最少操作数
  * 2、dp方程
    * 如果word1[i] == word[j] --> $dp[i][j] = dp[i-1][j-1]$
    * 否则
      * 添加字符：相当于是从(0,i-1)的word1的子串添加一个字符，使得word1(0,i) == word2(0,j)-->$dp[i][j] = dp[i-1][j]+1$
      * 删除字符：相当于是在(0,i)的word1子串，删除i位置的字符，使得word1(0,i) == word2(0,j-1)-->$dp[i][j] = dp[i][j-1]+1$
      * 替换字符：相当于是把i处的word1字符替换成word2的j字符，故从word1(0,i-1) = word2(0,j-1)的基础上加一个操作即可-->$dp[i][j] = dp[i-1][j-1]+1$ 
      * 上述三种操作取最小值就是dp[i][j]
* 代码如下
```java
public int minDistance(String word1, String word2) {
    int l1 = word1.length();
    int l2 = word2.length();
    int[][] dp = new int[l1 + 1][l2 + 1];
    for (int i = 1; i <= l2; i++) {
        dp[0][i] = i;
    }
    for (int i = 1; i <= l1; i++) {
        dp[i][0] = i;
    }

    for (int i = 1; i <= l1; i++) {
        for (int j = 1; j <= l2; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                int add = dp[i - 1][j] + 1;
                int delete = dp[i][j - 1] + 1;
                int replace = dp[i - 1][j - 1] + 1;
                dp[i][j] = Math.min(add, Math.min(delete, replace));
            }
        }
    }

    return dp[l1][l2];
}
```


## 2.ByteDance