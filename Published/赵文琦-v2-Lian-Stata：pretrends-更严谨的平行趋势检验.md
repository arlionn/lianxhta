# B806：Stata：pretrends-更严谨的平行趋势检验
&emsp;

> **作者：** 赵文琦 (浙江大学)        
> **邮箱：** <wenqi.zhao@zju.edu.cn>   

> **Source:** 本推文主要参考了以下论文和推文，特此致谢！  
>- [stata-pretrends](https://github.com/mcaceresb/stata-pretrends)
>- Roth, Jonathan. "Pretest with Caution: Event-Study Estimates after Testing for Parallel Trends." American Economic Review: Insights 4, no. 3 (2022): 305-22. [-PDF-](https://jonathandroth.github.io/assets/files/roth_pretrends_testing.pdf)
>- He, Guojun, and Shaoda Wang. 2017. "Do College Graduates Serving as Village Officials Help Rural China?" American Economic Journal: Applied Economics 9 (4): 186–215. [-PDF-](https://pubs.aeaweb.org/doi/pdfplus/10.1257/app.20160079)
>- [郭楚玉，2022，DID-倍分法：事前趋势检验的局限性和诊断](https://www.lianxh.cn/details/1103.html)

&emsp; 

- **Title**: Stata：pretrends-更严谨的平行趋势检验
- **Keywords**: 事前趋势检验, 倍分法, 双重差分法, Pre-trend Test, DID, parallel trends, power analysis, 统计功效

&emsp; 


## 1. 引言：为什么需要事前趋势检验 (Pre-trend Test) 的功效分析？

在双重差分 (Difference-in-Differences, DID) 研究中，**平行趋势假设**是识别处理效应的核心前提。该假设要求，若不施加任何处理干预，处理组和对照组的结果变量应随时间呈现相同的变化趋势。研究者通常通过检验处理前各期系数是否显著异于零，来评估该假设的合理性。

然而，这种传统的事前趋势检验方式存在一个关键问题：**即使真实存在趋势差异，我们的检验是否具有足够的统计功效 (power) 来识别这种偏离？** 若检验功效不足，即便平行趋势假设被违背，也可能无法有效检测出来，从而导致错误地接受该假设。

Roth (2022) 强调，事前趋势检验本身也应接受功效评估，即当平行趋势被违反时，应具有较高概率发现该违背。Stata 命令 `pretrends` 正是基于这一思想开发，能够帮助研究者量化事前趋势检验的识别能力，并通过可视化方式揭示在不同偏离程度下的检验表现，从而为平行趋势假设的判断提供更为稳健的依据。


## 2. pretrends 命令的理论基础

在识别真实、有效的政策效应时，研究者面临的根本挑战之一是**反事实结果的不可观测性**。在任何一个时间点，每个研究对象只能处于"接受处理"或"未接受处理"两种状态之一，而我们无法同时观测其在另一状态下的潜在结果。因此，**我们必须依赖控制组来近似处理组的反事实状态**，即用控制组的平均变化作为"若未接受处理时处理组将会如何变化"的估计。

要使这种替代成立，控制组与处理组之间必须具有较高的可比性 (comparability)，即在接受处理前，两组在因变量的趋势变化上应尽可能相似。**Pre-trend 检验**正是用来验证这一点的方法，通常通过回归中处理前各期的虚拟变量系数，检验其是否显著异于零。

在实际操作中，若各期的处理前系数均不显著，研究者往往据此认为"处理组和控制组在处理前趋势相似"，进而将控制组视为合格的反事实。然而，Roth (2022) 指出，这种判断方法存在两大问题：


### (1) 低功效问题 (Low Power) 

即使处理组和控制组的事前趋势并不相同，**传统的 Pre-trend 检验也可能无法检测出差异**，从而导致错误地接受平行趋势假设。换言之，这类检验存在严重的"第二类错误率过高"的问题——虽然能很好地控制第一类错误，却容易漏判真实存在的趋势差异。

为了解决这一问题，Roth 提出应当对事前趋势检验进行功效分析 (power analysis)。基于这一理念，Stata 中的 `pretrends` 命令可用于评估事前趋势检验的识别能力，即：

> **若平行趋势假设确实被违背，该检验需要多大的偏离程度，才能以一定概率 (如 80%) 检测出来？**

这一指标类似于随机对照实验 (RCT) 中的"最小可检测效应" (Minimum Detectable Effect, MDE)，有助于理解当未发现显著事前趋势时，这一结论是否可靠。


### (2) 样本选择偏误 (Sample Selection Bias) 

Roth 进一步指出，**能通过 Pre-trend 检验的样本，往往只是整个数据生成过程 (DGP) 中的一个"选定子样本" (selected sample)**。这些样本在某些特征上可能系统性地不同于总体，进而导致最终估计的政策效应出现统计偏误 (statistical distortion)。

为缓解这一问题，`pretrends` 命令提供了以下工具：

- 估算在"未发现显著事前趋势"这一条件下，政策效应估计量的**期望偏差**；
- 使用**贝叶斯因子 (Bayes Factor)** 评估，在未发现显著事前趋势时，我们应以多大程度的置信接受平行趋势假设。

综上，Roth (2022) 与 `pretrends` 命令提供的框架，有助于我们更严谨地理解和使用事前趋势检验结果，不仅关注"是否显著"，更关注"是否有能力发现趋势差异"以及"对样本选择偏误的控制"。这对于提升 DID 研究中因果识别的可信度具有重要意义。

接下来，本文将通过实例说明上述两个问题。

>**Note：** 如果你担心违背平行趋势假设，也可考虑 [HonestDiD](https://github.com/mcaceresb/stata-honestdid/?tab=readme-ov-file#example-usage----medicaid-expansions) 提供的敏感性分析方法。`HonestDiD` 不依赖事前检验显著性，而是假设处理后平行趋势违背相对于处理前不会"太大"，构建考虑了这种不确定性的置信区间。

## 2.1 低功效问题 (Low Power)

首先，我们来看事前趋势检验本身的低功效问题 (low power) 。我们假设三种处理组和控制组之间的相对趋势：分别是平行事前趋势、线性事前趋势和二次函数事前趋势。

### 2.1.1 平行事前趋势 (No Pre-trend)

如下图所示，绿色圆点代表了真实的 pre-trend 情况，即处理组和对照组的 $Y$ 变量拥有相同的事前平行趋势。Pre-trend test 的结果显示 $P$-value = 0.81。我们无法拒绝原假设：黑色三角 (回归估计系数) 等于绿色圆点。这是理想状况，事后期 (第 0-3 期) 的系数反映了真实的政策处理效应。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig03.png)

### 2.1.2 线性事前趋势 (Linear Pre-trend)

如下图所示，红色圆点代表了真实的 pre-trend 情况，即处理组和控制组的 $Y$ 变量的事前相对趋势满足线性关系。Pre-trend test 的原假设为黑色三角 (回归估计系数) 等于红色圆点。作者有意让这个 pre-trend test 的 $P$-value 也等于 0.81，即在这种情况下，我们同样无法拒绝原假设。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig04.png)

根据事前趋势的三个红色圆点可以外推出一条直线。在这种情况下，事后期 (第 0-3 期) 的系数**高估**了政策处理效应。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig05.png)

### 2.1.3 二次函数事前趋势 (Quadratic Pre-trend)

如下图所示，此时蓝色圆点代表了真实的 pre-trend 情况，即处理组和控制组的 $Y$ 变量的事前相对趋势满足二次函数关系。Pre-trend test 的原假设为黑色三角 (回归估计系数) 等于蓝色圆点。$P$-value 同样等于 0.81，即在这种情况下，我们仍无法拒绝原假设。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig06.png)

根据事前趋势的三个蓝色圆点可以外推出一条抛物线。在这种情况下，事后期 (第 0-3 期) 的系数**低估**了政策处理效应。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig07.png)

上述三种 pre-trend 情境告诉我们，在进行 Pre-trend test 时，我们无法拒绝平行事前趋势，但同样也无法拒绝线性事前趋势或二次函数事前趋势——而后两者都会导致事后期的估计系数产生偏误。这就是事前趋势检验本身低功效性 (low power) 的体现。

>**Note：** 当事前期的期数较少时，更容易出现事前趋势检验低功效性问题。


## 2.2 样本的选择性偏误

Pre-trend test 存在的第二个问题是"看似"**通过**前期趋势检验所导致的额外统计偏差。Roth 认为，能通过平行趋势检验的样本是整个 DGP 中某一类特殊的样本 (selected sample)，这会进一步导致偏误。

现在我们举一个简单的三期 DID 模型的例子。假设有三期 $t\in \{-1,0,1\}$，处理组在 $t=0$ 和 $t=1$ 期受到处理。假设不存在处理效应。处理组相对控制组的变化趋势呈线性关系，斜率系数为 $\delta$。

下图展示了当 $\delta = 3$ 时，处理组和控制组 $Y$ 变量的相对变化趋势。注意，由于假设不存在处理效应，控制组 $Y$ 变量的整体均值为 0，而处理组 $Y$ 变量随时间变化，呈现为一条斜率为 3 的线段。通过模拟，我们可以从这个总体中抽取出多种样本结果，以灰色线表示。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig08.png)

在这些随机抽样中，蓝色高亮线段代表那些能通过 Pre-trend test 检验的样本。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/Pre-trend-test%E5%B1%80%E9%99%90%E6%80%A7%E5%92%8C%E8%AF%8A%E6%96%AD_%E9%83%AD%E6%A5%9A%E7%8E%89_Fig09.png)

可以发现，那些在事前期较为平缓 (即事前期系数更接近于 0) 的样本更容易通过检验。这正是通过事前趋势检验而产生的额外统计偏差，它会导致无法准确估计真实、有效的政策处理效应。

## 3. pretrends命令使用说明
## 3.1 命令安装

推荐从 GitHub 直接安装最新版本：

```stata
local github https://raw.githubusercontent.com
net install pretrends, from(`github'/mcaceresb/stata-pretrends/main) replace
```

也可以下载后本地安装：

```stata
*先下载：https://github.com/mcaceresb/stata-pretrends
cap noi net uninstall pretrends // 如已安装，先卸载
net install pretrends, from(`c(pwd)'/stata-pretrends-main) // 从当前工作目录安装
```

使用 `pretrends` 前，你需要：

- 已完成一个事件研究回归，并能提取系数向量和协方差矩阵
- 确定系数对应的相对时间周期
- 安装 `coefplot` 包 (用于可视化，可选)

>**Note：** `pretrends` 可处理任何渐近正态估计器的事件研究结果，包括Callaway and Sant'Anna (2020)和Sun and Abraham (2020)的方法，只要系数和协方差矩阵保存在 `e(b)` 和 `e(V)` 中。

## 3.2 语法格式

`pretrends` 命令有两个主要的子命令，下面详细介绍其语法和选项。

### 3.2.1 计算特定功效下的事前趋势斜率

```stata
pretrends power <power_level> [, pre(varlist) post(varlist)]
```

**核心选项：**

- `<power_level>`：所需功效水平 (0-1之间) ，如 `0.8` 表示80%功效
- `pre(varlist)`：指定事件研究中事前处理系数的位置或变量列表
- `post(varlist)`：指定事件研究中事后处理系数的位置或变量列表

**返回结果：**

- `r(slope)`：计算出的线性事前趋势斜率 (正值，表示幅度)
- `r(Power)`：指定的功效水平

### 3.2.2 假设趋势下的功效分析与可视化

```stata
pretrends [, numpre(integer) 
             b(matrix_name) v(matrix_name) 
             slope(num) delta(matrix_name) 
             time(numlist) ref(num) 
             nocoefplot coefplot]
```

**核心选项：**

- `numpre(integer)`：事前处理系数数量
- `b(matrix_name)`：包含事件研究系数的矩阵名称
- `v(matrix_name)`：包含系数协方差矩阵的名称

**假设趋势选项：**

- `slope(num)`：假设线性事前趋势的斜率，如 `slope(0.052)`
- `delta(matrix_name)`：包含任意非线性假设趋势的矩阵

**时间周期指定选项：**

- `time(numlist)`：系数对应的相对时间周期列表，如 `time(-4(1)3)`
- `ref(num)`：基准期，如 `ref(-1)`

**可视化选项：**

- `coefplot`： (默认) 绘制事件研究图并叠加假设趋势
- `nocoefplot`：跳过可视化

**返回结果：**

- `r(Power)`：在假设的事前趋势下，我们发现显著事前趋势的概率。数值越高表明在假设的趋势下，我们更有可能发现显著的事前系数。
- `r(BF)`：贝叶斯因子，代表在假设的事前趋势下相对于平行趋势下通过事前趋势检验的概率之比。贝叶斯因子越小，在观察到事前趋势不显著 (即通过事前趋势检验) 时，我们就越倾向于支持平行趋势 (相对于假设趋势) 成立。
- `r(LR)`：似然比，代表在假设趋势下观察到的系数的似然值与在平行趋势下观察到的系数的似然值之比。如果这个值很小，那么在数据中观察到的事件研究系数在平行趋势下出现的可能性要远大于在假设趋势下出现的可能性。
- `r(results)`：用于制作事件图的数据。请注意 `meanAfterPretesting` 这一列，它也被绘制出来了。这一列的基本思路在于，如果我们仅在未发现显著的事前趋势这一条件下分析事件研究，那么我们分析的只是数据的一个选定子集。 `meanAfterPretesting` 这一列告诉我们，如果实际上的事前趋势就是研究人员所假设的趋势，那么在未发现显著事前趋势这一条件下，我们预期的系数值是多少。

## 4. Stata 实操
## 4.1 一个例子

在中国农村治理体系中，由于信息不对称和监管成本高，上级政府往往难以精准识别贫困人口。虽然村干部更了解本地情况，但地方精英俘获 (elite capture) 问题又可能导致扶贫资源被非贫困户占用。面对这一两难困境，大学生村官 (CGVOs) 计划为解决基层扶贫难题提供了一个独特视角。He and Wang (2017) 的研究采用双重差分法 (DID)考察了大学生村官计划能否提高贫困户识别与帮扶效率，其平行趋势检验方程如下：

$$y_{it} = \sum_{\substack{k \geq -4,\\ k \ne -1}}^{k=3} D_{it}^k \cdot \delta_k + \mu_i + \rho_t + \varepsilon_{it}$$

其中， $y_{it}$ 为村庄 $v$ 在 $t$ 年的结果变量，当其含义为登记贫困户数量时，估计结果绘制如下：

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/HeAndWang.png)

下面通过该估计结果进行实例演示。

## 4.2 事件研究

首先载入原始数据：

```stata
use "https://media.githubusercontent.com/media/mcaceresb/stata-pretrends/main/data/workfile_AEJ.dta", clear
```

接着，运行 He and Wang (2017) 的回归模型：

```stata
local leads "Lead_D4_plus Lead_D3 Lead_D2"
local lags  "Lag_D1 Lag_D2 Lag_D3_plus"
reghdfe l_poor_reg_rate `leads' D0 `lags', ///
        absorb(v_id year) cluster(v_id) dof(none)
```

可以得到如下结果：

```stata
             |              Robust
l_poor_reg~e |     Coef.   Std. Err.    t    P>|t|    [95% CI]
-------------+-------------------------------------------------
Lead_D4_plus |  .0667032   .0943746   0.71   0.480  -.119  .253
     Lead_D3 | -.0077018   .0770514  -0.10   0.920  -.159  .144
     Lead_D2 | -.0307691   .0551237  -0.56   0.577  -.139  .078
          D0 |  .0840307   .0626478   1.34   0.181  -.039  .207
      Lag_D1 |  .2424418   .0898103   2.70   0.007   .066  .419
      Lag_D2 |   .219879   .0887783   2.48   0.014   .045  .395
 Lag_D3_plus |  .1910925   .0989365   1.93   0.055  -.004  .386
       _cons |  1.478639   .0811732  18.22   0.000  1.319  1.64
```

## 4.3 计算特定功效 (power) 下的线性趋势斜率

计算50%功效下的线性事前趋势斜率:

```stata
pretrends power 0.5, pre(1/3) post(4/7)
```

得到结果：

```stata
Slope for 50% power =  .0520259
```

这表明，如果存在斜率为0.052的线性事前趋势，我们仅有50%的概率能检测到显著的事前趋势！换言之，即使真实存在这种趋势，我们大约有一半的情况下会错误地接受平行趋势假设。

## 4.4 可视化假设趋势并分析功效

接上例，假设存在斜率约为 0.0520259 的线性事前趋势:

```stata
* 提取回归系数和协方差矩阵
matrix sigma = e(V)
matrix beta  = e(b)
* 筛选出事件研究相关系数 (前7个)
matrix beta  = beta[., 1..7]
matrix sigma = sigma[1..7, 1..7]
* 可视化分析
pretrends, numpre(3) b(beta) v(sigma) slope(`r(slope)')
```

得到结果：

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250622184842.png)

生成的图表显示了假设趋势 (红线) 与估计系数 (蓝点) 的对比，以及在“通过事前检验”条件下的期望系数 (蓝虚线)。

此外，也可以通过 `return list` 命令查看详细计算结果，得到：

```stata
scalars:
                 r(LR) =  .1062107867920915
              r(Bayes) =  .5699360246383278
              r(Power) =  .4991930498887394
              r(slope) =  .052

macros:
   r(PreTrendsResults) : "PreTrendsResults"

matrices:
            r(results) :  8 x 6
              r(delta) :  1 x 8
```

还可以用 `matlist r(results)` 命令和 `matlist r(delta)` 命令查看矩阵，得到：

```stata
.	matlist	r(results)		

    |  t    betahat         lb        ub  deltatrue  meanAft~g 
----+----------------------------------------------------------
 r1 | -4   .0667032  -.1182677   .251674      -.156  -.0922333 
 r2 | -3  -.0077018  -.1587197  .1433162      -.104   -.055506 
 r3 | -2  -.0307691  -.1388096  .0772715      -.052  -.0278831 
 r4 | -1          0          0         0          0          0 
 r5 |  0   .0840307  -.0387567   .206818       .052    .064827 
 r6 |  1   .2424418   .0664168  .4184668       .104   .1207074 
 r7 |  2    .219879   .0458768  .3938812       .156   .1692721 
 r8 |  3   .1910925  -.0028194  .3850045       .208   .2242819 


.	matlist	r(delta)

    |    c1     c2     c3  c4    c5    c6    c7    c8 
----+-------------------------------------------------
 r1 | -.156  -.104  -.052   0  .052  .104  .156  .208 
```

除了线性趋势外，我们还可以分析任意形式的非线性趋势。这里以二次函数事前趋势为例：

```stata
* 定义二次函数事前趋势
mata st_matrix("deltaquad", 0.024 * ((-4::3) :- (-1)):^2)

* 分析非线性趋势
pretrends, time(-4(1)3) ref(-1) deltatrue(deltaquad) coefplot
```
得到结果：

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250622181506.png)

生成的图表同样显示了假设趋势 (红线) 与估计系数 (蓝点) 的对比，以及在“通过事前检验”条件下的期望系数 (蓝虚线)

还可以查看详细结果：

```stata
. return list

scalars:
                 r(LR) =  .4332635420562245
              r(Bayes) =  .3841447062350145
              r(Power) =  .662449239187808
              r(slope) =  .

macros:
   r(PreTrendsResults) : "PreTrendsResults"

matrices:
            r(results) :  8 x 6
              r(delta) :  1 x 8

. matlist r(results)

    |  t    betahat         lb         ub  deltatrue  meanAft~g 
----+-----------------------------------------------------------
 r1 | -4   .0667032  -.1182677    .251674       .216   .1184861 
 r2 | -3  -.0077018  -.1587197   .1433162       .096    .040358 
 r3 | -2  -.0307691  -.1388096   .0772715       .024   .0040393 
 r4 | -1          0          0          0          0          0 
 r5 |  0   .0840307  -.0387567    .206818       .024   .0093079 
 r6 |  1   .2424418   .0664168   .4184668       .096    .072993 
 r7 |  2    .219879   .0458768   .3938812       .216   .2004382 
 r8 |  3   .1910925  -.0028194   .3850045       .384   .3617779 

. matlist r(delta)

    |   c1    c2    c3  c4    c5    c6    c7    c8 
----+----------------------------------------------
 r1 | .216  .096  .024   0  .024  .096  .216  .384 
```

## 5. 结语

`pretrends` 命令为评估 DID 研究中事前趋势检验的可靠性提供了一种实用工具。然而，需注意的是，**事前趋势不显著并不意味着事前趋势不存在**，因此在解读检验结果时应保持谨慎。研究者可进一步结合不依赖事前趋势显著性假设的敏感性分析方法，例如 [HonestDiD](https://github.com/mcaceresb/stata-honestdid/?tab=readme-ov-file#example-usage----medicaid-expansions) 所提供的工具，以提高识别策略的稳健性与解释力。

## 6. 参考文献

- Roth, Jonathan. 2022. "Pretest with Caution: Event-Study Estimates after Testing for Parallel Trends." *American Economic Review: Insights* 4(3): 305-322.

- He, Guojun, and Shaoda Wang. 2017. "Do College Graduates Serving as Village Officials Help Rural China?" *American Economic Journal: Applied Economics* 9(4): 186–215.

- Callaway, Brantly, and Pedro H.C. Sant'Anna. 2020. "Difference-in-Differences with Multiple Time Periods." *Journal of Econometrics*.

- Sun, Liyang, and Sarah Abraham. 2020. "Estimating Dynamic Treatment Effects in Event Studies with Heterogeneous Treatment Effects." *Journal of Econometrics*.
