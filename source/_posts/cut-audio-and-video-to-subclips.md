---
title: 用Python实现音视频剪成片段
date: 2021-05-20 20:09:35
tags:
- 小工具
- 详细步骤
description: 最近在准备面试，为了方便学习和复习，需要把一段长音视频剪成一些小片段。本文介绍如何用Python实现，把音视频中的某一些片段剪出来。
---

## 背景

最近在准备面试，为了方便学习和复习，需要把一段长音视频剪成一些小片段。  
本文介绍如何用Python实现，把音视频中的某一些片段剪出来。

## 实践步骤

1. 寻找合适的Python库（安装是否麻烦、使用是否简便、执行会不会太久）
   - `moviepy` 音视频库。分析需要用的API：[代码示例](http://doc.moviepy.com.cn/index.html#id3)
2. 定义输入输出
   1. 输入：一个音视频文件的地址，需要剪出来的时间段
   2. 输出：剪辑片段的文件
3. 设计执行流程并一步步实现（定义函数，与使用具体API相关）
   1. 读入并创建clip对象。
   2. 剪辑subclip，输入时间参数可以是时间格式的字符串。
   3. 导出write_videofile。
4. 结论：时间太久，片段多长就花了多久的时间；CPU全跑满了。
   - stackoverflow [Concat videos too slow using Python MoviePY](https://stackoverflow.com/questions/56413813/concat-videos-too-slow-using-python-moviepy?r=SearchResults) 里面有个答案说，调用包里封装的ffmpeg函数会快一些：
        > You have some functions that perform direct calls to ffmpeg:
        [https://github.com/Zulko/moviepy/blob/master/moviepy/video/io/ffmpeg_tools.py](https://github.com/Zulko/moviepy/blob/master/moviepy/video/io/ffmpeg_tools.py)
        And are therefore extremely efficient, for simple tasks such as yours.
5. 重新设计和实现，直接使用`moviepy.video.io.ffmpeg_tools`里的函数：`ffmpeg_extract_subclip(源音视频文件,起,止,输出名)`。
   - 这个函数中输入的起止时间参数只能是数字，不能是字符串，而库基本使用的接口函数传入的是字符串。看源码发现是有个把时间字符串转换成数字的装饰器的，一步步找就可以找到那个转换的函数了。
6. 结论：时间快了很多，几乎是几秒内就完成。
   - 但并不明白为什么快了这么多
7. 优化：一次处理多个时间段
   1. 输入由一个起止时间，变为一组起止时间
   2. 循环处理每一组起止时间
   3. 输出的文件名按顺序拼接
8. 优化：每段时间配上名字
    1. 输入除了每一组的起止时间，还有后缀名
    2. 文件名+后缀得到输出的文件名
9. 优化：输入输出的合法性校验
    1. 校验输入地址是合法文件
    2. 校验时间段（没什么必要）
       1. 不可以小于0
       2. 不可以大于视频时间
       3. 起小于止

## 完整代码

需要`pip install moviepy`

```python
import os
import moviepy.video.io.ffmpeg_tools as fftool
from moviepy.tools import cvsecs

def add_suffix(file_name, suffix): # 文件名拼接后缀
    index = file_name.rfind('.') # 最后一个点号
    res = file_name[:index] + '_' + suffix + file_name[index:]
    return res

# 输入
file_name = r"./XX公司第3面.mkv"
output_arr = [
    ('04:20','05:07', '自我介绍'),
    ('05:07','17:47', '项目经历'),
    ('17:37','24:40', 'HTTPS'),
    ('24:40','28:10', '实现读写锁'),
]

if not os.path.isfile(file_name): # 校验
    print("不合法的输入", file_name)

for startStr, endStr, suffix in output_arr:
    start = cvsecs(startStr)
    end = cvsecs(endStr)
    
    if start < 0 or start >= end: # 校验
        print("不合法的时间",startStr, endStr)
        continue

    full_output_name = add_suffix(file_name, suffix)
    print('处理文件：', file_name, '时间：', startStr, '-', endStr)
    fftool.ffmpeg_extract_subclip(file_name,start,end,full_output_name) # 剪辑并输出
    print('处理功成功，输出：',full_output_name)
```

## 参考

- moviepy的文档
  - [moviepy中文文档](http://doc.moviepy.com.cn/)
  - [英文文档](https://zulko.github.io/moviepy/index.html)
  - [GitHub地址](https://github.com/Zulko/moviepy)
- 博文：[用moviepy将视频剪掉一段](https://blog.csdn.net/cymbals/article/details/105235448)
- stack overflow [Concat videos too slow using Python MoviePY](https://stackoverflow.com/questions/56413813/concat-videos-too-slow-using-python-moviepy?r=SearchResults)
