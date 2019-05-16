---
title: Anaconda 使用
tags: python,数据处理
grammar_cjkRuby: true
---

[toc!]

Anaconda Navigator ：用于管理工具包和环境的图形用户界面，后续涉及的众多管理命令也可以在 Navigator 中手工实现。

Jupyter notebook ：基于web的交互式计算环境，可以编辑易于人们阅读的文档，用于展示数据分析的过程。

qtconsole ：一个可执行 IPython 的仿终端图形界面程序，相比 Python Shell 界面，qtconsole 可以直接显示代码生成的图形，实现多行代码输入执行，以及内置许多有用的功能和函数。

spyder ：一个使用Python语言、跨平台的、科学运算集成开发环境

升级工具包
```
conda upgrade --all
```
安装一个package
```
conda install package_name
```
指定版本
```
conda install numpy=1.10
```
移除package
```
conda remove package_name
```
升级package版本
```
conda update package_name
```
查看所有packages
```
conda list
```
模糊查询
```
conda search search_term
```
默认环境为root
创建新环境
```
conda create -n env_name   list of packages
```
-n 代表name
env_name 为要创建的环境的名称
list of packages 为在新环境中需要安装包的名称
安装python3的Anaconda版本，root为python3环境
创建python2 环境
```
conda create -n py2 python=2.7 pandas
```
使用conda的方便之处是可以自动安装相应的依赖包，不需要手动安装
进入名为env_name 的环境
```
source activate env_name
```
退出当前环境
```
source deactivate
```
在windows系统中没有不带source
显示所有环境
```
conda env list
```
当分享代码时，将代码环境打包分享，可已将环境信息存入名为environment的YAML文件中
```
conda env  export 	> environment.yaml
```
执行别人的代码时，使用其他人的环境配置
```
conda env create -f environment.yaml
```