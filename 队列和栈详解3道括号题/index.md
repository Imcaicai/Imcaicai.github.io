# 【队列和栈】详解3道括号题


## 1.1 判断有效括号串  

### 题目

给定一个只包括 `'('`，`')'` 的字符串 `s` ，判断字符串是否有效。即每个右括号 `')'` 的左边必须有⼀个左括号 `'('` 和它匹配。  

### 解析

用一个变量 left 记录 `'('` 相对于 `')'` 的数量，遇到 `'('` 就 +1，遇到 `')'`  就 -1。如果最后 left==0，则括号串有效，否则无效。并且，如果中间出现 left 数量为负，则说明有 `')'`  出现在 `'('`  之前，也为无效。

代码实现：

```java
bool isValid(string str) {
	// 待匹配的左括号数量
	int left = 0;
	for (int i = 0; i < str.size(); i++) {
		if (s[i] == '(') {
			left++;
		} else {
			// 遇到右括号
			left--;
		}
		// 右括号太多
		if (left == -1)
			return false;
	}
	// 是否所有的左括号都被匹配了
	return left == 0;
}
```

## 1.2 判断有效括号串进阶

### 题目

**力扣 [20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)**

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

示例：

```
输入：s = "()[]{}"
输出：true
```

### 解析

使用⼀个名为 left 的栈代替之前思路中的 left 变量，遇到左括号就入栈，遇到右括号就去栈
中寻找最近的左括号，看是否匹配。

代码实现：

```c++
class Solution {
public:
    bool isValid(string s) {
        stack<char> left;
        for(char c:s){
            // c是左括号
            if(c=='(' || c=='{' || c=='[')
                left.push(c);
            else{	
                // c是右括号且栈中最近的左括号匹配
                if(!left.empty() && leftOf(c)==left.top())
                    left.pop();
                // 栈中最近的左括号不匹配
                else return false;
            }
        }
		// 是否所有的左括号都被匹配了
        return left.empty();
    }
    
    char leftOf(char c){
        if(c==')')  return '(';
        else if(c=='}') return '{';
        else return '[';
    }
};
```

## 2.1 平衡括号串

### 题目

**力扣 [921. 使括号有效的最少添加](https://leetcode.cn/problems/minimum-add-to-make-parentheses-valid/)**

只有满足下面几点之一，括号字符串才是有效的：

- 它是一个空字符串，或者
- 它可以被写成 `AB` （`A` 与 `B` 连接）, 其中 `A` 和 `B` 都是有效字符串，或者
- 它可以被写作 `(A)`，其中 `A` 是有效字符串。

给定一个括号字符串 `s` ，在每一次操作中，你都可以在字符串的任何位置插入一个括号

- 例如，如果 `s = "()))"` ，你可以插入一个开始括号为 `"(()))"` 或结束括号为 `"())))"` 。

返回 *为使结果字符串 `s` 有效而必须添加的最少括号数*。

### 解析

**核心思路是以左括号为基准，通过维护对右括号的需求数 need，来计算最小的插入次数。** 需要注意两个地方：  

🟠 **need == -1**

need == -1 意味着右括号太多，需要及时判断插入左括号。**注意后面多出的左括号不能抵消前面多出的右括号。**

🟡 **返回 res+need**

res 记录左括号的插入次数，need 记录右括号的需求。

代码实现：

```c++
class Solution {
public:
    int minAddToMakeValid(string s) {
        int res=0;	// res记录插入次数
        int need=0;	// need记录右括号的需求量

        for(char c:s){
            if(c=='('){
                need++;
            }
            else if(c==')'){
                need--;
                if(need==-1){	// 需要插入一个左括号
                    need++;
                    res++;
                }
            }
        }
        return res+need;
    }
};
```

## 2.2 平衡括号串进阶

### 题目

**力扣 [1541. 平衡括号字符串的最少插入次数](https://leetcode.cn/problems/minimum-insertions-to-balance-a-parentheses-string/)**

给你一个括号字符串 `s` ，它只包含字符 `'('` 和 `')'` 。一个括号字符串被称为平衡的当它满足：

- 任何左括号 `'('` 必须对应两个连续的右括号 `'))'` 。
- 左括号 `'('` 必须在对应的连续两个右括号 `'))'` 之前。

比方说 `"())"`， `"())(())))"` 和 `"(())())))"` 都是平衡的， `")()"`， `"()))"` 和 `"(()))"` 都是不平衡的。

你可以在任意位置插入字符 '(' 和 ')' 使字符串平衡。

请你返回让 `s` 平衡的最少插入次数。

###  解析

🟧 **首先，按照上面的思路维护 res 和 need 变量**

🟨 **need == -1**

当 need == -1 时，说明遇到一个多余的右括号，则需要插入一个左括号，对右括号的需求变为 1。即 `res++;	need = 1;`

🟩 **遇到左括号**

对右括号的需求量 +2。若此时 need 为奇数，则需要插入一个右括号，使 need 变为偶数。即 `res++;	need--;`

代码实现：

```c++
public:
    int minInsertions(string s) {
        int res=0;	// 记录插入的括号数量
        int need=0;	// 记录右括号的需求量
        for(char c:s){
            if(c=='('){
                need+=2;	// 一个左括号对应两个右括号
                if(need%2 == 1){
                    res++;	// 插入一个右括号
                    need--;	// 对右括号的需求减一
                }
            }
            else{	
                need--;	
                if(need==-1){	// 右括号多了
                    need=1;	// 还需要再来一个右括号
                    res++;	// 插入一个左括号
                }
            }
        }

        return res+need;
    }
```

参考资料：

https://labuladong.github.io/algo/

