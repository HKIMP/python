## 需要修改目录里的文件后缀

把 .txt结尾的文件都修改成 .csv结尾
```python
import os
def unify_ext_with_os_path(path):
    for filename in os.listdir(path):
        basename, ext = os.path.splittext(filename)
        if ext == '.txt':
            abs_filepath = os.path.join(path, filename)
            os.rename(abs_filepath, os.path.join(path, f'{basename}.csv))
```
* os.listdir.path: 列出path目录下的所有文件（包含文件夹）
* os.path.splitext(filename)：切分文件名里面的基础部分和后缀部分
* os.path.join(path, filename)：组合需要操作的文件名为绝对路径
  
使用pathlib
```python
from pathlib import Path

def unify_ext_with_pathlib(path):
    for fpath in Path(path).glob('*.txt'):
        fpath.rename(fpath.with_suffix('.csv'))
```
* 使用Path(path)将字符串路径转换为Path对象
* 调用.glob('*.txt')  对路径下所有内容进行模式匹配并以生成器方式返回，
  结果仍然是Path对象，所有我们可以接着做后面的操作
* 使用.with_suffix('.csv')直接获取使用新后缀名的文件全路径
* 调用.rename(target)完成重命名

## 读取大文件

标准做法：
使用with open(file_name) 上下文管理器的方式获得一个文件对象。
然后使用for循环迭代它，逐行获取文件里的内容。
```python
def count_nine(filename):
    count = 0
    with open(filename) as file:
        for line in file:
            count += line.count('9')
    return count
```
这么做有两个好处：
1. with上下文管理器会自动关闭打开的文件描述符
2. 在迭代文件对象时，内容是一行一行返回的，不会占用太多内存。

### 标准做法的缺点
如果被读取的文件里，并没有任何换行符，那么上面第二个好处就不成立了。
当代码执行到for line in file时，line将会变成一个非常巨大的字符串对象。

### 使用read方法分块读取
为了解决这个问题，我们需要暂时把 标准做法 放一边。考虑使用更底层的file.read()方法。
与直接循环迭代文件对象不同，每次调用 file.read(chunk_size) 会直接返回从当前
位置往后读取chunk_size大小的文件内容。不必等任何换行符出现。

```python
def count_nine_v2(fname):
    count = 0
    block_size = 1024*8
    with open(fname) as fp:
        while True:
            chunk = fp.read(block_size)
            #当文件没有更多内容时，read调用将会返回空字符串''
            if not chunk:
                break
            count += chunk.count('9')
    return count
```
在新的函数中，我们使用了一个while循环 来读取文件内容。每次最多读取8kb的大小。
这样可以避免需要拼接一个巨大字符串的过程，把内存占用减低非常多。

### 利用生成器来解耦代码
如果我们讨论的不是Python，而是其他编程语言。上边的代码已经很好了。
但是如果认真分析一下count_nine_v2函数，会发现在循环体内部，有两个独立的逻辑。
1. 数据生成(read调用 chunk判断)
2. 数据消费()
这两个逻辑被耦合在了一起。
为了提升复用能力，我们可以定义一个新的chunked_file_reader生成器函数，由它来负责所有与'数据生成'相关的逻辑，
这样count_nine_v3里面的主循环就只负责计数即可。
```python
def chunked_file_reader(fp, block_size=1024*8):
    while True:
        chunk = fp.read(block_size)
        #???break
        if not chunk:
            break
        yield chunk
def count_nine_v3(fname):
    count = 0
    with open(fname) as fp:
        for chunk in chunked_file_reader(fp):
            count += chunk.count('9')
        return count
```






参考：
1.[围观大神是如何用 Python 处理文件的？](https://mp.weixin.qq.com/s/wCkBawChzDZEn9yInCQSWg)
