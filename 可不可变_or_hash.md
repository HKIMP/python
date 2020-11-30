可哈希的元素：int, float, str, tuple, 自定义的类的实例对象
不可哈希的元素：list, set, dict

元素可不可哈希，意味着是否使用hash进行索引
list不使用hash值进行索引，故其对存储元素没有可哈希的要求
set/dict使用hash进行索引，所以对存储的元素有可哈希的要求
dict仅仅对键有可哈希的要求，对value没有。

























参考：
1. https://www.cnblogs.com/zhouie/p/10702613.html
2. https://www.cnblogs.com/pungchur/p/12079853.html#_label6
3. https://stackoverflow.com/questions/14535730/what-does-hashable-mean-in-python
4. https://blog.csdn.net/lnotime/article/details/81192207
   ![图 5](../images/0ef48dda37ead53f39f8da2fb73d0eee6e1b6c685b9b6f4c6f59f7cfbe158ca8.png)  
