---
title: Python学习
tags: python
grammar_cjkRuby: true
---

[toc!]
#### print（）
print('hello','world',sep=',','!')
print('hello','woeld',end='!\n')
print('hello,world!')
print('输出变量%.3f' %变量名)
#### 运算
a/b 浮点型
a//b 整型 只取商
a%b 整型 取余数
a**b a的b次幂 a^b^

#### type()
检查变量类型
print(type(变量名))
如果代码太长，可以使用\或者()折行
#### getpass模块
getpass.getpass()函数
使用getpass()函数时，输入密码在终端不显示
```
password=getpass.getpass('输入口令：')
```
可以扁平化结构而不适用嵌套
#### random 模块
randint()生成指定范围内的随机整数
####  类型转换
python int型list和str型list互转
一.方式一
1.str --> int
results = ['1','2','3']
results = list(map(int, results))

2.int --> str
results = [1,2,3]
results = list(map(str, results))
二.方式二

1.str --> int
results = ['1','2','3'] 
results = [ int(i) for i in results ]

2.int --> str results = [1,2,3] 
results = [ str(i) for i in results ]
#### 三角形面积
s=\frac{1}{4}\sqrt((a+b+c)(a+b-c)(a+c-b)(b+c-a))
