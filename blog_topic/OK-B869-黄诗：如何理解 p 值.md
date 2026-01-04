
> **作者**：黄诗 (暨南大学)     
> **邮箱**：<huangshi100484@126.com>    

&emsp;

> **编者按**：本文主要整理自以下内容，特此致谢！
> Hannah Volk-Jesussek. p-value. 2025. Blog at [-Link-](https://numiqo.com/tutorial/p-value)

&emsp; 

- **Title**: 统计学小白必看：如何通俗易懂地搞定 p 值与假设检验？
- **Keywords**: p 值，零假设，显著性水平，假设检验，统计推断

&emsp; 


## 1. 引言

在统计分析和科学研究中，“ p 值”是一个核心概念，但往往也是最容易被误解的概念之一。该视频旨在解释什么是 p 值以及如何正确解读它。为了深入浅出地理解这一概念，该视频通过一个具体的案例进行分析：假设有两家生产手机电池的工厂——工厂 A 和工厂 B。我们的目标是确定这两家工厂生产的电池在电池寿命（即从充满电到耗尽的时间）上是否存在差异。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image.png)
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-1.png)

&emsp;

## 2. 理论背景：从零假设出发

### 2.1 零假设 (Null Hypothesis)

要理解 p 值，首先必须建立“零假设”的概念。零假设通常陈述为“没有真正的效应或差异”。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-2.png)

在本案例中，零假设即为：**工厂 A 和工厂 B 生产的电池具有相同的平均电池寿命**。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-3.png)

### 2.2 抽样与随机性

在现实操作中，我们无法测量两家工厂生产的所有电池。因此，我们必须从工厂 A 和工厂 B 各抽取一部分样本进行测量。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-4.png)

即便在零假设成立（即两家工厂确实没有真实差异）的前提下，由于随机性的存在，我们要想抽到两组平均寿命完全相同的样本也是极不可能的。纯粹出于偶然，其中一组样本的平均寿命很可能会比另一组长一点。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-5.png)

例如，样本均值之间可能相差 13 分钟。甚至在某些情况下，可能观察到更大的差异，比如 22 分钟。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-6.png)

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-7.png)

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-8.png)

&emsp;

## 3. p 值的核心逻辑

### 3.1 p 值的定义

当我们观察到样本之间存在差异（例如 13 分钟）时，我们可以问一个问题：如果两家工厂真的没有区别，那么抽样得到差异为 13 分钟或更大的可能性是多少？
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-16.png)

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-17.png)

这就是 p 值告诉我们的信息。**p 值是指：如果零假设为真（即确实没有差异），那么观察到与当前样本一样极端、甚至更极端结果的概率**。

### 3.2 实例解读

假设我们通过实验观察到了 **43 分钟**的差异，并计算出此时的 p 值为 **0.04**。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-9.png)

- **数值含义**：这表示如果零假设是正确的，我们要纯粹通过随机抽样观察到 43 分钟或更极端差异的概率仅为 4%。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-10.png)

- **推断逻辑**：这是一个相当小的概率，意味着在零假设成立的情况下，这样的结果非常罕见。因此，观察到如此不可能的结果让我们开始怀疑零假设的真实性。

&emsp;

## 4. 统计决策：显著性水平

### 4.1 拒绝零假设的标准

仅仅得到 p 值是不够的，我们需要决定是“拒绝”还是“不拒绝”零假设 。这个判断标准被称为**显著性水平 (Significance Level)**，通常设定为 **5%**。
- **p < 5% (显著)**：如果计算出的 p 值小于 5%，我们认为在零假设下看到该数据的概率太低（小于 5%），因此拒绝零假设 。这被称为“显著”。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-13.png)

- **p < 1% (高度显著)**：如果 p 值小于 1%，则被称为“高度显著”。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-12.png)

- **p > 5% (不显著)**：如果 p 值大于 5%，则称为“不显著”，我们不能拒绝零假设。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-14.png)

### 4.2 决策规则的设定
显著性水平（如 5% 或 1%）必须在研究开始**之前**确定，以确保结果的可比性，不能为了得到想要的结果而在事后更改标准。p 值的具体数值通常通过假设检验（如 $t$ 检验）计算得出。

&emsp;

## 5. 重要误区与风险

### 5.1 p 值小 $\neq$ 零假设必假
这是关于 p 值最大的误区。一个很小的 p 值（例如 0.04）并**不意味着零假设绝对是假的** 。它只是说明：如果零假设是真的，观察到这种结果是非常不可能的。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-20.png)

### 5.2 犯错的风险 (Type I Error)
当我们设定 5% 的显著性水平并拒绝零假设时，我们实际上接受了 **5% 的犯错几率**。

这意味着，如果零假设在现实中完全为真（工厂之间确实没区别），当我们重复进行许多次实验时，大约有 **5% 的研究会纯粹因为随机运气而错误地拒绝零假设**。这是统计推断中不可避免的风险。
![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/image-18.png)
