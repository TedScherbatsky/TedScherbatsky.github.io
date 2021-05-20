---
title: 递归函数的Debug技巧
date: 2021-05-02 15:24:56
tags:
- 调试
- 算法
- 小工具
description:  在递归函数内部配合缩进，打印关键值，可以更直观地观察递归函数的执行情况。而利用Python装饰器，可无需修改调试函数实现，达到同样的效果。
---
在递归函数内部配合缩进，打印关键值，可以更直观地观察递归函数的执行情况。

> 参考：[labuladong - 分享一个小技巧，提高刷题幸福感](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247490945&idx=1&sn=03da23d366ad4577d2a22328f3ba04f9)

### Python装饰器方案

利用Python装饰器，可无需修改调试函数实现，达到同样的效果。

```py
def debugHelper(func): # 装饰器
    cnt = 0
    indent = '│ ' # 根据自己的喜好来选择缩进方式
    def wrapper(*args, **kwargs):
        nonlocal cnt
        # 按照自己的需求来打印 begin
        print(indent*cnt,end="")
        print(args[0]) 
        # 按照自己的需求来打印 end
        cnt += 1
        res = func(*args, **kwargs) # 被修饰函数
        cnt -= 1
        # 按照自己的需求来打印 begin
        print(indent*cnt,end="")
        print(args[0],':',res) 
        # 按照自己的需求来打印 end
        return res 
    return wrapper
```

### 使用示例 1

斐波那契数列

```py
@debugHelper
def fib(n) -> int:
    if n == 0 or n == 1:
        return n
    return fib(n-1)+fib(n-2)

res = fib(4)
print(res)

# 输出
# (4,)
# │ (3,)
# │ │ (2,)
# │ │ │ (1,)
# │ │ │ (1,) : 1
# │ │ │ (0,)
# │ │ │ (0,) : 0
# │ │ (2,) : 1
# │ │ (1,)
# │ │ (1,) : 1
# │ (3,) : 2
# │ (2,)
# │ │ (1,)
# │ │ (1,) : 1
# │ │ (0,)
# │ │ (0,) : 0
# │ (2,) : 1
# (4,) : 3
```

### 使用示例 2

LeetCode第10题

```py
def isMatch(s: str, p: str) -> bool:
    # .*表示任意字符串都可以匹配了
    def optimizePattern(p):
        j = 0
        while j < len(p)-1:
            if p[j+1] == '*':
                if j+3 <= len(p)-1 and p[j:j+2] == p[j+2:j+4]:
                    p = p[:j+2] + p[j+4:]
                    continue
                j += 1 # 配合下一句，实现 j+=2
            j += 1
        return p

    @debugHelper
    def dp(s,p):
        n,m = len(s), len(p)
        if m == 0:
            return n == 0
        if n == 0:
            if m == 1 or p[1] != '*':
                return False
            return dp(s,p[2:])
        if m == 1 or p[1] !='*':
            if s[0] == p[0] or p[0] == '.':
                return dp(s[1:],p[1:])
            return False
        # .* 或者 any char with *
        if p[0] == '.' or s[0] == p[0]:
            return dp(s,p[2:]) or dp(s[1:],p) # 匹配或者不匹配
        else:
            return dp(s,p[2:]) # 不相同，只能不匹配了

    p = optimizePattern(p)
    return dp(s,p)

s = "aab"
p = 'c*a*b'
res = isMatch(s,p)
print(res)

# 输出
# ('aab', 'c*a*b')
# │ ('aab', 'a*b')
# │ │ ('aab', 'b')
# │ │ ('aab', 'b') : False
# │ │ ('ab', 'a*b')
# │ │ │ ('ab', 'b')
# │ │ │ ('ab', 'b') : False
# │ │ │ ('b', 'a*b')
# │ │ │ │ ('b', 'b')
# │ │ │ │ │ ('', '')
# │ │ │ │ │ ('', '') : True
# │ │ │ │ ('b', 'b') : True
# │ │ │ ('b', 'a*b') : True
# │ │ ('ab', 'a*b') : True
# │ ('aab', 'a*b') : True
# ('aab', 'c*a*b') : True
# True
```
