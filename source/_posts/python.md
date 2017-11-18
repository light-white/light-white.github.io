---
title: Python高级编程
date: 2017-11-14 18:07:17
tags:
- Python
---

# 1.简介
在这里总结一下Python的高级编程  
灵活运用Python特性,写出pythonic的代码来减少我们的代码量

# 2.推导式
Python文档,列表推导式能非常简洁的构造一个新列表:只用一条简洁的表达式即可对得到的元素进行转换变形  
## 2.1 列表推导式
比方说我们要找出一个列表中所有大于5的数  
正常写出来应该是这样的
```python
nums = [-1,4,78,32,7,4,-9]
result = []
for i in nums:
    if i > 5:
      result.append(i)
# [78, 32, 7]
```
正常写大概要花这么四行 使用列表推导式的话只需要一行
```python
nums = [-1,4,78,32,7,4,-9]
result = [i for i in nums if i > 5]
# [78, 32, 7]
```
列表推导式的语法  
[key_exp：value_exp for item in collection if codition]  
列表推导式有点不好就是整个列表会一次性加载于内存中,当列表很大的时候会占用大量内存  
这种时候可以使用生成器,把中括号换成小括号就会返回一个生成器
```python
nums = [-1,4,78,32,7,4,-9]
result = (i for i in nums if i > 5)
print result

# <generator object <genexpr> at 0x00583E18>

for item in result:
    print item

# 78, 32, 7
```
## 2.2 字典推导式
我们来合并一下大小写key
```python
m = {'a': 10, 'b': 34, 'A': 7, 'Z': 3}
result = {}
for key, value in m.items():
    if key.lower() in result:
        result[key.lower()] += m[key]
    else:
        result[key.lower()] = m[key]
#{'a': 17, 'b': 68, 'z': 3}
```
在这里我用的是python3.6 在2.7中可以使用dict.has_key()判断key是否存在

使用字典推导式
```python
result = {key.lower():m.get(key.lower(),0) + m.get(key,0) for key in m.keys()}
#{'a': 17, 'b': 68, 'z': 3}
```
## 2.3 集合推导式
集合推导式与列表推导式相似,我们将得到一个无重复的集合,只要把列表推导式的中括号换成大括号
```python
nums = [1,2,3,3,3,4,4,4,5,5,5]
result = {i for i in nums}
#{1, 2, 3, 4, 5}
```
# 3.Python高阶函数
Python函数式编程

函数本身可以赋值给变量，赋值后变量为函数；允许将函数本身作为参数传入另一个函数；允许返回一个函数。
## 3.1 匿名函数lambda
lambda表达式  
没有函数名 单条语句组成 语句执行的结果就是返回值 可用作sort的key函数
