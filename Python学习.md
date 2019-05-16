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
#### 三角形面积计算
海伦公式：
s=\frac{1}{4}\sqrt((a+b+c)(a+b-c)(a+c-b)(b+c-a))
#### range()
range(101)  0~100之内的整数
range(1,100) 1~99之间的整数
range（1,100,2）1~99之间步长为2
知道循环迭代次数使用for in range
不知迭代次数 使用while
#### 下划线\_用法
##### 解释器中
\_符号是指交互解释器中最后一次执行语句的返回结果。这种用法最初出现在CPython解释器中，其他解释器后来也都跟进了。
##### 作为名称使用
这个跟上面有点类似。\_用作被丢弃的名称。按照惯例，这样做可以让阅读你代码的人知道，这是个不会被使用的特定名称。举个例子，你可能无所谓一个循环计数的值：
```
n = 42
for _ in range(n):
    do_something()
```
##### 函数名使用
\_还可以被用作函数名。这种情况，单下划线经常被用作国际化和本地化字符串翻译查询的函数名。这种惯例好像起源于C语言
#### 水仙花数
水仙花数（Narcissistic number）也被称为超完全数字不变数（pluperfect digital invariant, PPDI）、自恋数、自幂数、阿姆斯壮数或阿姆斯特朗数（Armstrong number），水仙花数是指一个 3 位数，它的每个位上的数字的 3次幂之和等于它本身（例如：1^3 + 5^3+ 3^3 = 153）。
```python
for i in range(100,1000):
    a=i//100
    b=i%100//10
    c=i%10
    if i==a**3+b**3+c**3:
        print('%d是水仙花数'%i)
```
#### 完美数
它所有的真因子（即除了自身以外的约数）的和（即因子函数），恰好等于它本身。如果一个数恰好等于它的因子之和，则称该数为“完全数”。第一个完全数是6，第二个完全数是28，第三个完全数是496，后面的完全数还有8128、33550336等等。
```python
for number in range(1,1000000):
    part_ls=[]
    sum=0
    half_number=number//2
    for j in range(1,half_number+1):
        if number%j==0:
            part_ls.append(j)
    for i in range(len(part_ls)):
        sum+=part_ls[i]
    if sum==number:
        print('%d是一个完美数'%number)
		```
#### 百鸡百钱
```python
for cock_number in range(0,20):
    for hen_number in range(1,33):
        chick_number=100-cock_number-hen_number
        if cock_number*5+hen_number*3+chick_number/3==100:
            print('公鸡%d只，母鸡%d只，鸡雏%d只'%(cock_number,hen_number,chick_number))
```
公鸡0只，母鸡25只，鸡雏75只
公鸡4只，母鸡18只，鸡雏78只
公鸡8只，母鸡11只，鸡雏81只
公鸡12只，母鸡4只，鸡雏84只
#### 斐波那契数列
指的是这样一个数列：1、1、2、3、5、8、13、21、34、……在数学上，斐波纳契数列以如下被以递推的方法定义：F(1)=1，F(2)=1, F(n)=F(n-1)+F(n-2)（n>=3，n∈N*）
```pyhton
fabonacci=[]
fabonacci.append(1)
fabonacci.append(1)
for i in range(2,20):
    fabonacci.append(fabonacci[i-1]+fabonacci[i-2])
print(fabonacci)
```
#### craps赌博游戏
玩家掷两个骰子，每个骰子点数为1-6，如果第一次点数和为7或11，则玩家胜；如果点数和为2、3或12，则玩家输庄家胜。若和为其他点数，则记录第一次的点数和，玩家继续掷骰子，直至点数和等于第一次掷出的点数和则玩家胜；若掷出的点数和为7则庄家胜。
```python
import random
count=0
guest_count=0
host_count=0
dice_1=random.randint(1,6)
dice_2=random.randint(1,6)
dice_sum=dice_1+dice_2
count+=1
if dice_sum in [7,11]:
    guest_count+=1
    print('%d次投掷中，玩家第一次取胜,点数为%d,%d,和为%d'%(count,dice_1,dice_2,dice_sum))
if dice_sum in [2,3,12]:
    guest_count+=1
    print('%d次投掷中，庄家第一次取胜,点数为%d,%d,和为%d'%(count,dice_1,dice_2,dice_sum))
if dice_sum not in [2,3,7,11,12]:
    while True:
        dice_sum_1=dice_sum
        dice_1=random.randint(1,6)
        dice_2=random.randint(1,6)
        dice_sum=dice_1+dice_2
        count+=1
        if dice_sum==dice_sum_1:
            guest_count+=1
            print('玩家获胜')
            print('玩家在第%d次获胜，获胜概率为%f'%(count,guest_count/count))
            break
        if dice_sum==7:
            host_count+=1
            print('庄家获胜')
            print('庄家在第%d次获胜，获胜概率为%f'%(count,host_count/count))
            break
```