---
title: Markdown使用教程
date: 2021-05-19 13:40:59
updated: 2021-05-19 13:40:59
tags: Markdown
categories: 效率工具
---
### 标题
```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

显示效果：

<img src="/images/markdown/md_title.gif" width="50%" height="50%" align="center">

### 段落换行
```
注释：两行之间插入空行
段落1

段落2
```
显示效果：
段落1

段落2
<!--more-->
### 字体
```
_斜体文本_
__粗体文本__
___粗斜体文本___
```
显示效果：
_斜体文本_
__粗体文本__
___粗斜体文本___

### 分割线
```
注释：减号空格减号空格减号空格
- - -
```
显示效果：
- - -

### 删除线
```
注释：文字两端加上两个波浪线
hello ~~我是删除线~~
```
显示效果：
hello ~~我是删除线~~

### 下划线
```
注释：下划线可通过<u>我是下划线文本</u>
```
显示效果：
<u>我是下划线文本</u>

### 脚注
```
注释：脚注是对文本的补充说明
脚注示例[^我是脚注]
[^我是脚注]: 对文本的补充说明！
```
显示效果：
<img src="/images/markdown/md_jiaozhu.gif" width="50%" height="50%" align="center">

### 列表
#### 无序列表
```
注释：-空格内容
- 第一项
- 第二项
- 第三项
```
显示效果：
- 第一项
- 第二项
- 第三项

#### 有序列表

```
注释：数字.空格内容
1. 第一项
2. 第二项
3. 第三项
```
显示效果：
1. 第一项
2. 第二项
3. 第三项

#### 列表嵌套
```
注释：子列表的选项前面添加四个空格
- 第一项
    - 我是子列表项1.1
    - 我是子列表项1.2
- 第二项
    - 我是子列表项2.1
    - 我是子列表项2.2
```
显示效果：
- 第一项
    - 我是子列表项1.1
    - 我是子列表项1.2
- 第二项
    - 我是子列表项2.1
    - 我是子列表项2.2

### 区块
```
注释：>空格内容
> 最外层
>> 第一层嵌套
>>> 第二层嵌套
```
显示效果：
> 最外层
> > 第一层嵌套
> >
> > > 第二层嵌套

___区块可以嵌套列表，列表可以嵌套区块___

### 代码

``` python
# 用 ``` 包裹一段代码，并指定一种语言（也可以不指定）
# 段落上的一个函数或片段的代码可以用反引号把它包起来（`）
def log(*args,**kwargs):
	print(*args.**kwargs)
```
此处，`log`函数与`print`函数功能是一样的
### 链接
```
[这是我的个人网站](https://www.codejerry.top)
```
显示效果：
[这是我的个人网站](https://www.codejerry.top)

### 图片
```
方式1
![替代文本](https://codejerry.top/images/avatar.jpg "网站头像")
方式2（可以指定图片的宽、高、位置）
<img src="https://codejerry.top/images/avatar.jpg" width="20%" height="20%" align="center">
```
显示效果：
![替代文本](https://codejerry.top/images/avatar.jpg "网站头像")

<img src="https://codejerry.top/images/avatar.jpg" width="20%" height="20%" align="center">

### 表格
```
注释：|分割不同的单元格，-分割表头和其他行
|表头|表头|
|-|-|
|单元格|单元格|
|单元格|单元格|
```
显示效果：
<img src="/images/markdown/md_table.jpg" width="50%" height="50%" align="center">

```
注释：设置表格对齐方式
  :-左对齐
  -:右对齐
  :-:居中对齐
```
显示效果：
<img src="/images/markdown/md_table_align.jpg" width="50%" height="50%" align="center">

### 高级技巧
#### 支持的 HTML 元素
```
注释：目前支持的 HTML 元素有：<kbd> <b> <i> <em> <sup> <sub> <br>等
使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑
```
显示效果：
使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Del</kbd> 重启电脑
#### 转义
```
注释：\加上待转义符号
\\
\*
\()
```
显示效果：
\\
\*
\()
#### 公式

```
注释：使用两个美元符 $$ 包裹 TeX 或 LaTeX 格式的数学公式
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
${$tep1}{\style{visibility:hidden}{(x+1)(x+1)}}
$$
```
显示效果：
<img src="/images/markdown/md_math.jpg" width="50%" height="50%" align="center">

### 参考
[runoob.com|Markdown 教程](https://www.runoob.com/markdown/md-tutorial.html)

