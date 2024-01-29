# LeetCode刷题笔记
- [LeetCode刷题笔记](#leetcode刷题笔记)
    - [1、HOT 100](#1hot-100)
      - [1.1 HOT100-LC3-无重复最长子串](#11-hot100-lc3-无重复最长子串)
      - [1.2 HOT100-LC4 寻找两个正序数组中的中位数](#12-hot100-lc4-寻找两个正序数组中的中位数)
    - [2.ByteDance](#2bytedance)



### 1、HOT 100
#### 1.1 HOT100-LC3-无重复最长子串
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

#### 1.2 HOT100-LC4 寻找两个正序数组中的中位数
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

### 2.ByteDance