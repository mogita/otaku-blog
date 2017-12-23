---
title: 解决 PDF 复制文字重复的问题
permalink: unicode-normalization-issue-with-pdf
date: 2015-11-16 17:16:19
updated:
tags: Python
cover:
share_cover:
---

今天整理一些 pdf 格式的文稿时，为了统计字数而把文字全选并拷贝到一个 txt 文件中。但是发现拷贝出来的文字会偶尔产生重复文字，例如：

```
吸⽓气时缓慢放松腹部
注意呼吸 ⾮非常好
帮助缩⼩小腰围
```

重复文字的位置非常多，会严重影响字数统计的结果，因此必须去重。根据 @**rex.** 大大的[知乎回答](http://www.zhihu.com/question/20805225/answer/16273727)，我尝试匹配了一下要处理的文本，结果发现这些有问题的重复字都无法被匹配。你若感兴趣可以亲自试一试，将上边例子里的重复字转换成 unicode 编码，你会发现结果是不同的。你可以使用[这个在线工具](https://r12a.github.io/apps/conversion/)来转换。

所幸得到 V2EX 的 @**Sylv** 指点，原来这是一个「Unicode 规范化」问题（[查看原帖](https://www.v2ex.com/t/236498#r_2632245)）。用 `unicodedata.normalize()` 函数就可以将那些字形相同但实际上编码不同的文字的编码也规范化（统一化）。这样，就可以成功进行匹配并去重了。

Python 代码如下：

```python
import unicodedata
import re
import sys

# input file name as a parameter in the command line
filename = sys.argv[1]
token = "tokenthatneverduplicates"

# open original file
file = open(filename, 'r')
raw = file.read().decode("utf-8")
file.close()

# tag original double characters and letters
treated = re.sub(r'(.)\1', r'\1' + re.escape(token) + r'\1', raw)

# do the normalization
normalized = unicodedata.normalize('NFKC', treated).encode('utf-8')

# remove duplicated characters
result = re.sub(r'([\x80-\xff]{3})\1', r'\1', normalized)

# remove tags made earlier
final = re.sub(token, '', result)

# save result to new file
newfile = open(filename + '-conv.txt', 'w')
newfile.write(final)
newfile.close()
```

代码中每一步均有注释，步骤非常简单。其中有个需要稍加解释的地方，由于去重的正则匹配会将所有重叠词变成单字，会导致原本正确的部分也被改掉了（如「慢慢地 -> 慢地」），于是用了一个小伎俩保留住它们：

执行规范化之前，在重叠词之间添加一个独特的文本标记，然后在规范化之后将标记去掉。标记即代码开头的 token 变量。如果非常巧合，原文中有和我预设的标记相同的文字，那么修改 token 的值即可。

代码已在 OS X 10.11 上成功执行。

附：代码不是最优，但 python 水平实在有限，目前就确保可用吧。