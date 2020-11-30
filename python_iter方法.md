一、上代码、学用法
我们都比较熟悉 iter(obj)，会返现一个迭代器，如果 obj 不是可迭代对象，则会报错。
但其实如果仔细看官方文档，会发现 iter() 方法其实是接受两个参数的，文档说明如下

> iter(object[, sentinel])
  sentinel 英文翻译为 哨兵。

sentinel 参数是可选的，当它存在时，object 不再传入一个可迭代对象，
而是一个可调用对象，通俗点说就是可以通过()调用的对象，
而 sentinel 的作用就和它的翻译一样，是一个“哨兵”，
当时可调用对象返回值为这个“哨兵”时，循环结束，且不会输出这个“哨兵”。

可能有点难懂，用一个简单需求来说明，需求说明如下：

心里想一个[1, 10]范围的数，然后代码开始随机，当随机到想的数时停止，看每次代码需要随机几次。

实现分析：看起来应该很简单，random，然后加一个if判断即可，但是用 iter() 来实现更简单。实现代码如下：

```python
from random import randint

def guess():
    return randint(0, 10)

num = 1
#这里先写死心里想的是5：
for i in iter(guess, 5):
    print('第%s次猜测，猜测数字为：%s'%(num, i))
    num += 1
#当guess返回的是5时，会抛出StopIteration，但for循环会处理异常。即结束循环。
```

文档中这两个参数说的很细

> The first argument is interpreted very differently depending on the presence of the second argument.

> Without a second argument, object must be a collection object which supports the iteration protocol (the _iter_() method), or it must support the sequence protocol (the _getitem_() method with integer arguments starting at 0). If it does not support either of those protocols, TypeError is raised.

简单说，没有第二个参数，就是我们熟悉的用法
```python
iter('123')
<str_iterator at ....>
```
来看第二个参数的情况

> If the second argument, sentinel, is given, then object must be a callable object. The iterator created in this case will call object with no arguments for each call to its _next_() method; if the value returned is equal to sentinel, StopIteration will be raised, otherwise the value will be returned.

> 如果给定了第二个参数 sentinel，object 则必须是一个可调用对象，这个可调用对象没有任何参数，当可调用对象的返回值等于 sentinel 的值时，抛出 StopIteration 的异常，否则返回当前值。（这里如果不好理解可调用对象，可以理解为函数，这样更容易想明白）

对于这个用法的适用场景，文档中也给出了说明：

> One useful application of the second form of iter() is to build a block-reader. For example, reading fixed-width blocks from a binary database file until the end of file is reached:

> 翻译：对于第二个参数，一个有用的场景是创建一个 blokc-reader，即根据条件中断读取。比如：从二进制数据库文件读取固定宽度的块，直到到达文件的末尾，代码示例如下：

```python
from functools import partial
with open('mydata.db', 'rb') as f:
    for block in iter(partial(f.read, 64), b''):
        process_block(block)
```

总结：
1. iter()方法不管有没有第二个参数，返回的都是迭代器
2. iter()方法第一个参数的参数类型，根据有无第二个参数决定。

参考：[深入 Python iter() 方法](http://www.imooc.com/article/287997)