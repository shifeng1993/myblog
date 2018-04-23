---
title: 【python随笔】python2和python3语法区别
categories: python
date: 2018-2-28
tags:
  - python
---
## print 语句
输出 `hello world`
- python:
```python
print "hello world"
```
- python3:
```python
print("hello world")
```

## 整数除法
`输出 `1.5``
- python:
```python
3.0 / 2
```
- python3:
```python
3 / 2
```

## 输入语句
- python:
```python
raw_input('请输入年龄')
```
- python3:
```python
input('请输入年龄')
```

## 创建类
- python: 
```python
class ClassName(object):
```
- python3: 
```python
class ClassName():
```

## 类的继承
父类为 `Car`, 子类为 `ElectricCar`
- python:
```python
class ElectricCar(Car):
  def __init__(self, make, model, year):
    super(ElectricCar, self).__init__()
```
- python3:
```python
class ElectricCar(Car):
  def __init__(self, make, model, year):
    super().__init__()
```