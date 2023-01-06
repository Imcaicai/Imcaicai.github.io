# 数组链表-滑动窗口算法


本文主要记录最难掌握的一类双指针技巧——滑动窗口算法。算法思路很简单，就是维护一个窗口，不断滑动，时间复杂度为 O(N)。大致逻辑如下：

```c++
int left = 0, right = 0;
while (right < s.size()) {
	// 增⼤窗⼝
	window.add(s[right]);
	right++;
	while (window needs shrink) {
		// 缩⼩窗⼝
		window.remove(s[left]);
		left++;
	}
}
```

滑动窗口算法的代码框架如下：

```c++
/* 滑动窗⼝算法框架 */
void slidingWindow(string s) {
	unordered_map<char, int> window;
	int left = 0, right = 0;
	while (right < s.size()) {
		// c 是将移⼊窗⼝的字符
		char c = s[right];
		// 增⼤窗⼝
		right++;
		// 进⾏窗⼝内数据的⼀系列更新
		...
		/*** debug 输出的位置 ***/
		printf("window: [%d, %d)\n", left, right);
		/********************/
        
		// 判断左侧窗⼝是否要收缩
		while (window needs shrink) {
			// d 是将移出窗⼝的字符
			char d = s[left];
			// 缩⼩窗⼝
			left++;
			// 进⾏窗⼝内数据的⼀系列更新
			...
		}
	}
}
```

unordered_map 就是哈希表（字典），相当于 Java 的 HashMap，它的⼀个方法 count(key) 相当于 Java
的 containsKey(key) 可以判断键 key 是否存在。可以使用方括号访问键对应的值 map[key]。需要注意的是，如果该 key 不存在，C++ 会自动创建这个 key，并把 map[key] 赋值为 0。  

套模板前需要思考 3 个问题：

✅ 什么时候移动 right 扩大窗口?

✅ 什么时候移动 left 缩小窗口?

✅ 什么时候更新结果?

## 1 最小覆盖子串

### 题目

**力扣 [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)**

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

### 解析

🟡 我们在字符串 S 中使用双指针中的左右指针技巧，初始化 left = right = 0，把索引左闭右开区间
[left, right) 称为一个「窗口」。

> 理论上区间可以两端都开或者两端都闭，但左闭右开最放便处理。因为初始化时区间 [0, 0) 没有元素，但 right 向右移动一位，区间 [0, 1) 就包含一个元素 0 了。如果区间的两端都开，那么 right 向右移动一位后区间 (0, 1) 仍没有元素；如果区间两端都闭，那么 [0, 0] 未初始化就包含了一个元素。这两种情况都会给边界处理带来不必要的麻烦。    

🟢 不断地增加 right 指针扩大窗口 [left, right)，直到窗口中的字符串符合要求  

🔵 停止增加 right，转而不断增加 left 指针缩小窗口[left, right)，直到窗口中的字符串不再符合要求。同时，每次增加 left，都要更新一轮结果。   

🟣 重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。  

如果⼀个字符进入窗口，应该增加 window 计数器；如果⼀个字符将移出窗口的时候，应该减少 window 计数器；当 count 满足 need 时应该收缩窗口；应该在收缩窗口的时候更新最终结果。  

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char, int> need, window;
        for(char c:t)   need[c]++;

        int left=0,right=0;
       	// 记录最小覆盖子串的起始索引及长度
        int start=0,len=INT_MAX;  
        // 记录窗口内含有规定字符的个数（无重复）
        int count=0;    
        while(right<s.size()){
            char c=s[right];	// 移入窗口的字符
            right++;	// 扩大窗口
            if(need.count(c)){
                window[c]++;    
                if(window[c]==need[c])
                    count++;
            }
			
            // 判断窗口是否要缩小
            while(count==need.size()){
                // 更新最小覆盖子串
                if(right-left < len){
                    start=left;
                    len = right-left;
                }
                char a=s[left];	// 移出窗口的字符
                left++;	// 缩小窗口
                // 更新数据
                if(need.count(a)){
                    if(window[a]==need[a])
                        count--;
                    window[a]--;
                }
            }
        }

        return len==INT_MAX ? "" : s.substr(start,len);
    }
};
```

## 2 字符串的排列

### 题目

**力扣 [567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/)**

给你两个字符串 `s1` 和 `s2` ，写一个函数来判断 `s2` 是否包含 `s1` 的排列。如果是，返回 `true` ；否则，返回 `false` 。

换句话说，`s1` 的排列之一是 `s2` 的 **子串** 。

### 解析

本题移动 left 缩小窗口 的时机是窗口大小大于 t.size() 时，因为排列嘛，显然长度应该是⼀样的。

当发现 valid == need.size() 时，就说明窗口中就是⼀个合法的排列，所以立即返回 true。

至于如何处理窗口的扩大和缩小，和最小覆盖子串完全相同  

```c++
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        unordered_map<char, int> need, window;
        for (char c : s1) need[c]++;
        int left=0,right=0;
        int count=0;
        while(right<s2.size()){
            char a=s2[right];
            right++;
            // 更新窗口数据
            if(need.count(a)){
                window[a]++;
                if(window[a]==need[a])
                    count++;
            }
            
            // 判断左侧窗口是否收缩
            while(right-left >= s1.size()){
                // 在这⾥判断是否找到了合法的⼦串
                if(count==need.size())
                    return true;
                char b=s2[left];
                left++;
				// 更新窗口数据
                if(need.count(b)){
                    if(window[b]==need[b])
                        count--;
                    window[b]--;
                }
            }
        }

        return false;
    }
};
```

## 3 找所有字母异位词  

### 题目

**力扣 [438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)**

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。

### 解析

```c++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        unordered_map<char, int> need, window;
        for (char c : p) need[c]++;

        int left=0,right=0;
        int count=0;
        vector<int> res;    // 记录结果
        while(right<s.size()){
            char a=s[right];
            right++;
			// 更新窗口数据
            if(need.count(a)){
                window[a]++;
                if(window[a]==need[a])
                    count++;
            }
			// 判断左侧窗口是否要收缩
            while(right-left>=p.size()){
                if(count==need.size())
                    res.push_back(left);
                char b=s[left];
                left++;
				// 更新窗口数据
                if(need.count(b)){
                    if(window[b]==need[b])
                        count--;
                    window[b]--;
                }
            }
        }

        return res;
    }
};
```

## 4 最长无重复子串

### 题目

**力扣 [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)**

给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长子串** 的长度。

### 解析

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> window;

        int left=0,right=0;
        int res=0;	// 记录结果
        while(right<s.size()){
            char a=s[right];
            right++;
            // 更新数据
            window[a]++;
			// 判断左侧窗口是否要收缩
            while(window[a]>1){
                char b=s[left];
                left++;
                window[b]--;	// 更新数据
            }
			// 更新答案
            res = right-left>res ? right-left : res;
        }

        return res;
    }
};
```

参考资料：

https://labuladong.github.io/algo/

