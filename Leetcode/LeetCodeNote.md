# LeetCode刷题笔记
- [LeetCode刷题笔记](#leetcode刷题笔记)
    - [1、HOT 100](#1hot-100)
      - [1.1 HOT100-LC3-无重复最长子串](#11-hot100-lc3-无重复最长子串)
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
  * 注意:hashmap的容量不要错误指定，否则会提高时间复杂度-->todo hashmp源码确认
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

### 2.ByteDance