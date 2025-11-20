

> - 周洋 (清华大学, <yangzhou0327@outlook.com>)；    
> - 李森林 (中南财经政法大学, <2289286835@qq.com>)；   
> - 连玉君 (中山大学, <arlionn@163.com>)    

&emsp;

- **Title**: 平方项 = 倒U型 ？
- **Keywords**: 交乘项, 非线性关系, 边际效应, U形关系, 倒U形关系

&emsp; 


## 1. 非线性关系简介

在实证分析中我们常常假设解释变量和被解释变量存在线性关系。然而，在很多情况下，解释变量和被解释变量可能存在非线性关系。在模型中加入平方项，甚至是高阶项 (断点回归分析 RDD 中常用的处理方法)，是文献中常用的模型设定方法。典型的模型设定方式为：

$$y=a+bx+cx^2$$

其中， $a$ 是常数项， $b$ 是线性关系的系数， $c$  是平方项的系数 (说明：为了便于表述，这里省去了控制变量和干扰项)。

本文的目的在于分析加入平方项后我们该如何解释系数的含义？有哪些需要特别注意的地方？

### 平方项显著就一定意味着存在 U 形关系吗？

浏览现有的期刊论文，我们经常发现不少作者只要发现平方项的系数 $c$ 在统计上显著，便声称 $y$ 和 $x$ 之间存在「**U 形关系**」($c>0$) 或「**倒 U 形关系**」($c<0$) 。这并不严谨，有时甚至是错误的。

在社会科学研究中，我们必须考虑变量的经济含义和取值范围，这就使得我们不能像做初中代数习题那样来分析包含平方项的模型。比如，研究「收入 (Income) 与年龄 (Age) 」的非线性关系时，Age 就不可能取负值，甚至不可能小于 18。同时，Age 的最大值也有限制，取决于研究对象及样本特征。

一旦考虑到 $x$ 的取值范围，再配合二次曲线转折点的位置，我们便会发现，在样本区间内 $x  \in [x_{min}, x_{max}]$，$y$ 和 $x$ 之间的关系可能仅仅是 U 形曲线的左半支或右半支，也即，$y$ 和 $x$ 之间的关系仍然是单调关系，只是 $x$ 对 $y$ 的边际影响在变化 (递增或递减)。

**图 1** 呈现了一些典型的状况，我们可以可以通过在线性回归模型中添加二次项来拟合每一种情形。在 Stata 中，使用 ` regress` 命令即可为所有以上曲线提供了线性和二次系数的最佳估计。

下面，举一些例子来具体说明。

#### 例 1：美食的边际效应递减 (倒 U 形的左半支)

**图 1-A** 呈现了经济学中反复强调的边际效用递减规律。这种例子很多。比如，随着收入的增加，食物消费支出也会增加，但增幅会不断降低。又如，喝咖啡可以提神，但第五杯的效果远远低于第一杯。「倒 U 形关系」的典型特征是模型 (1) 中的平方项系数 $\hat{c}<0$，例如，图 1-A 便是基于如下函数关系产生的：

$$\hat{y}=0.910+0.122x-0.006 x^2$$  

其中，$\hat{b} = 0.122$ 是线性关系的系数，为正；而平方项系数是 $\hat{c}=-0.006$，为负。线性关系的系数为正，意味着随着 $x$ 的增加，$y$ 也不断增加。而平方项系数小于零则表示，随着 $x$ 的增加 (注意，在本例中 $x$ 的取值始终大于零)，$y$ 随之减少。由于 $|\ \hat{b}\ | > |\ \hat{c}\ |$，当 $x$ 较小时， 线性关系占主导作用。随着 $x$ 的增大，平方项系数会逐渐占主导作用。

#### 例 2：不吵架的夫妻幸福吗？(倒 U)

「**U 形关系**」或「**倒 U 形关系**」也很普遍，此时，典型的特征是，$x$ 取某些值时，$x$ 和 $y$ 的关系为正；而当 $x$ 取其它值时，$x$ 和 $y$ 的关系为负。

例如，**图 1-B** 呈现了夫妻冲突程度与夫妻快乐程度之间可能存在的「**倒 U 形关系**」关系。夫妻经常有冲突吗？ 如果他们一直有冲突，他们会不开心。 但是一种理论认为，从来没有或很少有冲突的夫妻也是不快乐的，那些有适度冲突的人是最快乐的。注意正的线性关系系数正在将 $y$ 幸福感的估计值向上推，并且负二次方系数将 $y$ 的估计值向下推。 在数据范围内，$x^2$ 的值变得如此之大，以至于二次项效应会反向抵消线性项的影响。所以，偶尔吵吵架其实有助于增强「夫妻感情的肌肉力量」。


#### 例 3：国民收入与婴儿死亡率 (U 形左半支)

**图 1-C** 中呈现了一种「衰减递减型曲线」关系。假设被解释变量是各国的婴儿死亡率 ($y$)，解释变量是国民生产总值 ($x$)。 国民生产总值较低的国家婴儿死亡率较高，但随着国民生产总值的提高，婴儿死亡率趋于下降，但下降速度会越来越慢。以至于当国民生产总值超过一定水平后，进一步增加国民生产总值不会导致婴儿死亡率明显变化 (会维持在一个相对稳定的水平)。或许可以用如下公式描述上述关系：

$$\hat{y}=0.910-0.122x+0.006 x^2$$  

此时，平方项的系数 $\hat{c}>0$ 意味着 $x$ 和 $y$ 整体上呈现 U 形关系，考虑到 $x$ 的取值介于 0 和 10 之间，只有曲线的左半支才有经济意义。 

类似的例子还有很多：止疼药可以减缓疼痛，但随着用药剂量的增加，药效逐渐下降 (产生了耐药性)；经济援助可以降低一国的失业率，但政策效果可能会不断降低，等等。

**图 1-D** 和 **图 1-E** 分别呈现了另外两种情形，有兴趣的读者可以自行举例分析。  


![图 1： 加入平方项可能刻画的非线性关系](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314004025.png)

> 图 1： 加入平方项可能刻画的非线性关系

&emsp;

> **注意：** 在加入二次项系数后，我们需要关注转折点：$TP = -0.5 \hat{b}/\hat{c}$，同时，还要作如下几个分析才能确保转折点是有意义的：
- 其一，转折点是否在解释变量 $x$ 的取值范围内，即 $TP \in [x_{min}, x_{max}]$；
- 其二，转折点的两侧是否有足够多的样本？如果没有的话，则表示在全样本范围内，$y$ 和 $x$ 整体上还是单调关系。


&emsp;


## 2. 系数含义和边际效应

在最简单的情形中，$y$ 只受到一个单独可观测的因素 $x$ 影响，但又取决于其二次形式，即模型设定为：

$$
y=\beta_{0}+\beta_{1} x+\beta_{2} x^{2}+u \qquad  (1)
$$

使用 OLS 估计出参数后，可以将 $y$ 的拟合值表示如下：

$$
\hat{y}=\hat{\beta}_{0}+\hat{\beta}_{1} x+\hat{\beta}_{2} x^{2} \qquad  (2)
$$

我们关注的是 $x$ 对 $y$ 的边际影响：

$$\frac{\Delta \hat{y}}{\Delta x} \approx \hat{\beta}_{1}+2 \hat{\beta}_{2} x \qquad  (3) $$

这说明，$x$ 对 $y$ 的边际影响并非常数，而是随着 $x$ 的取值的变化而变化。有两点需要注意：
- 其一，一次项系数的含义问题。我们可以将 $\hat{\beta}_{1}$ 解释为从 $x=0$ 到 $x=1$ 的近似斜率。或者，我们可以换个写法：$\partial{y}/\partial{x} | _{x=0} = \hat{\beta}_1$，也就是说，$\hat{\beta}_{1}$ 表示在 $x=0$ 处，$x$ 对 $y$ 的边际影响。然而，在很多情况下，$x$ 是无法取值为零的 (例如，上文的 **例 3** 中，国民收入无法取值为零)，此时单独讨论 $\hat{\beta}_{1}$ 是没有意义的。
- 其二，我们的关注点与研究目的有关。如果我们的目的是预测，即，我们只关心给定 $x$ 的起始值及其变化时 $y$ 的预测值的变动，可以直接用（2）式进行模型估计；如要分析 $x$ 对 $y$ 的影响，那么就必须使用（3）式进行解释，通常的做法是将样本中 $x$ 的均值或者某些有意义的数带入 (3) 式，如中位数或上下四分位3点，进而计算 $x$ 对 $y$ 的边际影响。

&emsp;


## 3. 使用平方项时的注意事项

### 3.1 转折点分析

当 $x$ 的系数为正，而 $x^{2}$ 的系数为负时，加入二次项的方程便具有了形如抛物线形态的倒 $U$ 型关系。表明模型总是存在一个正的 $x$ 值 $x^*$，此时 $x$ 对 $y$ 的影响为零；在此点之前 ($x<x^*$)， $x$ 对 $y$ 的影响为正；而在此点之后， $x$ 对 $y$ 的影响为负。在实证分析中，研究人员常常关注这个转折点的具体数值。

在（2）式中，如若估计出的$\hat{\beta}_{1}>0 ， \hat{\beta}_{2}<0$ ，那么 $x$ 对 $y$ 的影响递减，转折点（也是函数的最大值点）为 $x$ 的系数与 $x^{2}$ 系数的两倍之比，即：

$$
x^{*}=-\frac{\hat{\beta}_1}{2 \hat{\beta}_{2}} \qquad (5) 
$$ 

加入二次项的方程也可以具有 $U$ 型形态，当（2）式中的$\hat{\beta}_{1}<0 ， \hat{\beta}_{2}>0$ 时，就出现了 $U$ 形关系，这就刻画了 $x$ 对 $y$ 的递增影响。

如果水平项和二次项的系数具有相同的符号（同为正或者同为负），而解释变量必须非负，那么，便不存在 $x>0$ 的转折点。

例如，若$\beta_{1}$ 和 $\beta_{2}$ 都大于 0，则 $y$ 的最小期望值在 $x=0$ 处取得，且 $x$ 的增加总是对 $y$ 有正向的递增影响。（若$\hat{\beta}_{1}=0$，$\hat{\beta}_{2}>0$，则意味着在 $x=0$ 处的偏效应为零，并随着 $x$ 的增加而递增，情况也是一样。）类似地，若 $\beta_{1}$ 和 $\beta_{2}$ 都小于 0，则 $y$ 的最大期望值在 $x=0$ 处取得，$x$ 的增加对 $y$ 有负的影响，而且影响的幅度随着 $x$ 变大而递增。

对于任意二次方程的转折点的一般公式为 $x^{*}=-0.5 \hat{\beta}_{1} / \hat{\beta}_{2}$，这表明当 $\hat{\beta}_{1}$ 与 $\hat{\beta}_{2}$ 符号相反时，转折点为正值， $\hat{\beta}_{1}$ 与 $\hat{\beta}_{2}$ 符号相同时，转折点为负值。

因此，在计算转折点时，考虑 $x$ 的取值范围可以判断该转折点是否具有经济意义。

### 3.2 高度共线性问题

加入二次项之后，一般很难避免多重共线性。由于多重共线性具有方差膨胀（variance inflation）的作用，故加入二次项后一般会使得估计量的方差增大，导致回归系数的显著性下降。这当然不是我们想看到的效果。

那么，究竟是否应该在回归模型加入二次项呢？这就涉及到如何在遗漏变量偏差与多重共线性之间进行权衡。当然，如果线性模型已经是对于现实世界的足够好近似，那么就可以忽略遗漏变量偏差，而不必加入二次项或高次项了。为此，可以进行「回归方程设定误差检验」(Ramsey's RESET 检验，即 Regression Equation Specification Error Test)。比如，如果完整的方程为：

$$
y=\beta_{0}+\beta_{1} x_{1}+\beta_{2} x_{2}+\beta_{3} x_{1}^{2}+\beta_{4} x_{2}^{2}+\beta_{5} x_{1} x_{2}+\varepsilon
$$

则可对原假设

$$
H_{0}: \beta_{3}=\beta_{4}=\beta_{5}=0
$$

进行 F 检验。如果拒绝 $H_{0}$ ,则说明模型中应该有二次项；反之，则说明应使用线性模型。

如果在模型中加入二次项，则一般应在论文中同时汇报仅包含一次项的简洁模型，以及包含二次项的完整模型之估计结果，这是所谓「稳健性检验」(robustness checks) 的一种形式。如果两种模型的定性结果类似 (qualitatively similar)，或者不影响你感兴趣变量的显著性与符号，则也很容易处理。

困难之处在于，有时简洁模型与完整模型的结果并不一致，甚至影响了统计显著性或回归系数的符号。而产生这种现象的原因依然是遗漏变量偏差或多重共线性。如果存在遗漏变量偏差，则简单的线性模型并不一致，而包含二次项的完整模型才是一致估计，故二者的估计结果大相径庭，也在情理之中。此时，应该选择后者。 

另一方面，即使遗漏变量偏差不存在或较微弱，加入二次项所导致的多重共线性，也可能通过「方差膨胀因子」(variance inflation factor)，增大估计量的标准误，使得原来显著的项变得不再显著。(译者注：对于多数微观面板数据而言，如上市公司数据、劳动力调查数据等，共线性通常都不会构成太严重的威胁)。

&emsp;

## 4. Stata 范例及边际效应分析

下面我们使用 Stata 自带的 **nlsw88.dta** 数据为例，介绍回归分析中二次项在 Stata 中的具体用法。

$$
wage=\beta_{0}+\beta_{1} ttl +\beta_{2}  ttl ^{2}+u
$$

其中， `wage` 表示个体的工资水平， `ttl` 表示个体的工作经验，而 $ttl^{2}$ 是 `ttl` 的二次项。




###  4.1 转折点的确定及经济含义

在正式分析之前，我们可以使用如下命令绘制 **ttl** 与 **wage** 之间的散点图，以便初步确认二者之间是否存在非线性关系。 

```stata
. sysuse "nlsw88.dta", clear
. twoway (scatter wage ttl) (qfit wage ttl, lcolor(red))
```

其中，`twoway () ()` 是 Stata 绘制二维图的标注架构，两对括号中分别绘制两个图形，分处于两个图层中 (每个图层可以视为一张透明的胶片)。`qfit` 用于绘制两个变量之间的非线性拟合曲线。 输出的图形如下：


![图 2： 散点图及非线性拟合图](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314003948.png)

> 图 2： 散点图及非线性拟合图

可以看出，工资 **wage** 与工作经验 **ttl_exp** 可能存在倒 **U** 型关系。然而，我们可能更为关注转折点的具体数值，下面进行计算：

```stata
 . sysuse "nlsw88.dta", clear
 . gen ttl_exp2 = ttl_exp*ttl_exp
 . reg wage ttl_exp ttl_exp2  // 也可以添加一些控制变量

      Source |       SS           df       MS      Number of obs   =     2,246
-------------+----------------------------------   F(2, 2243)      =     87.46
       Model |  5379.78611         2  2689.89305   Prob > F        =    0.0000
    Residual |  68988.1813     2,243  30.7571027   R-squared       =    0.0723
-------------+----------------------------------   Adj R-squared   =    0.0715
       Total |  74367.9674     2,245  33.1260434   Root MSE        =    5.5459

------------------------------------------------------------------------------
        wage |      Coef.   Std. Err.      t    P>|t|     [95% Conf. Interval]
-------------+----------------------------------------------------------------
     ttl_exp |      0.557      0.109     5.10   0.000        0.343       0.771
    ttl_exp2 |     -0.009      0.004    -2.12   0.034       -0.018      -0.001
       _cons |      2.462      0.639     3.85   0.000        1.209       3.716
------------------------------------------------------------------------------


 . global tp: dis %3.1f -_b[ttl_exp]/(2*_b[ttl_exp2]) //转折点
 . dis "Turn point = " $tp //能够得到 Turn point = 29.6
  
*---------------------------图示-----------------------------
 . local b0 = _b[_cons]
 . local b1 = _b[ttl_exp]
 . local b2 = _b[ttl_exp2]
 . sum ttl_exp
 . local min:  dis %3.1f r(min)
 . local max:  dis %3.1f r(max)
 . #delimit
 . twoway ( function y = `b2'*x^2 + `b1'*x + `b0',
           range(`min' `max') )
         ( function y = `b2'*x^2 + `b1'*x + `b0',
           range(`max' 40) lp(dash) )
       ,
       ytitle("wage") xtitle("ttl_exp")
       xline(`min',lc(red) lw(*1.5))
       xline($tp,  lp(dash) lc(green))
       xline(`max',lc(red) lw(*1.5))
       xlabel(`min' "Min (`min')" 5(5)25
              `max' "Max (`max')", angle(50))
       legend(off)
       ;
  . #delimit cr
  . graph export "fig_Ushape.png", replace  //保存图形

```

下图绿色虚线所在位置即为转折点（turn point，TP），等于 29.6：


![ 3： 图示转折点](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314003915.png)

> 图 3： 图示转折点

为了便于查看，我们还在图中用红色实线标注了解释变量 **ttl_exp** 的最小值和最大值。显然，本例中计算所得到的转折点在 **ttl_exp** 的最大值的右侧。也就是说，虽然表面上存在倒 U 型关系 (**ttl_exp2**) 的系数显著为负，但在本例的样本区间并不能覆盖转折点及其右侧的观察值。这意味，虽然 **ttl_exp** 与 **wage** 之间存在非线性关系，但这种关系主要表现为 **ttl_exp** 对 **wage** 的边际影响逐渐递减，但二者的关系始终是单调增的。简言之，此时只能讨论「倒 U 型关系」的左半支，而转折点本身并不具有实际的经济意义。

我们也可以使用 `count` 命令统计一下转折点两侧的观察值的分布情况：

```stata
. count if ttl_exp>$tp & e(sample)==1
  0
```

### 4.2 使用 margins 命令计算边际效应

在上述模型表示，工作经验 (**ttl_exp**) 及其二次项 (**ttl_exp2**) 会影响工资水平，我们要计算的是工作经验对工资的边际效应。

我们先用 `regress` 命令 (需要使用因子变量形式来表示二次项，`help fvvarlist`，详情参阅[「Stata：因子变量的全攻略」]([https://www.jianshu.com/p/16b08797e591](https://www.jianshu.com/p/16b08797e591)
) 估计模型，进而使用 `margins` 命令计算边际效应，并以此为基础使用 `marginsplot` 图示之。

```stata
sysuse "nlsw88.dta", clear
reg wage c.ttl_exp##c.ttl_exp $xx
sum ttl_exp
global min: dis %4.1f r(min)
global max: dis %4.1f r(max)
margins, dydx(ttl_exp) at(ttl_exp=($min 1(3)28 $max))

------------------------------------------------------------------------------
             |            Delta-method
             |      dy/dx   Std. Err.      t    P>|t|     [95% Conf. Interval]
-------------+----------------------------------------------------------------
ttl_exp      |
         _at |
          1  |   .5551562   .1084481     5.12   0.000     .3424871    .7678252
          2  |   .5382228   .1007071     5.34   0.000     .3407339    .7357117
          3  |   .4817783   .0752654     6.40   0.000     .3341811    .6293755
          4  |   .4253338   .0510196     8.34   0.000     .3252833    .5253843
          5  |   .3688893   .0309232    11.93   0.000     .3082482    .4295304
          6  |   .3124448   .0269191    11.61   0.000     .2596558    .3652338
          7  |   .2560003   .0436827     5.86   0.000     .1703376    .3416629
          8  |   .1995557   .0671331     2.97   0.003     .0679062    .3312053
          9  |   .1431112   .0923075     1.55   0.121    -.0379059    .3241283
         10  |   .0866667   .1181086     0.73   0.463    -.1449469    .3182803
         11  |   .0302222   .1442004     0.21   0.834     -.252558    .3130024
         12  |   .0132889   .1520623     0.09   0.930    -.2849086    .3114864
------------------------------------------------------------------------------

```

下面进行绘图，附带 95% 置信区间。

```Python 
 marginsplot, xlabel(, format(%3.1f) angle(60)) //绘图,附带 95% 置信区间
      graph export "$Out\fig_Wage_marginplot_A.png", replace

      marginsplot, addplot(function y=0, range($min $max)  ///
                   lcolor(red) lpattern(dash) legend(off)) ///
                   xlabel(, format(%3.1f) angle(60)) ///
                   ylabel(, format(%2.1f) angle(0))
      graph export "$Out\fig_Wage_marginplot_B.png", replace
```


![图 4： 边际效应图示](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314003844.png)

> 图 4： 边际效应图示

**图 4** 采用另一种方式展示了 $x$ (**ttl_exp)** 对 $y$ (**wage**) 的边际效应。

在 **图 3** 中，我们展现的是 $y$ 和 $x$ 的直接关系，此时，纵轴是 $y$，横轴是 $x$。在 **图 3** 的任意一点做一条切线，其斜率就是 $x$ 对 $y$ 的边际影响。显然，随着 $x$ 的取值不断增加，其对 $y$ 的边际影响不断减少，并在转折点处减为 **0**，随后 $\partial{y}/\partial{x}<0$。 

转而对比观察 **图 4**，可以清晰地看出上述变化过程。由于本例中，转折点 (TP = 29.6)
与样本中 $x$ 的最大值非常接近，因此图中的边际效应到 0 处基本上就停止了。若 TP 发生在 $x$ 的平均值附近，则图 4 中的边际效应回呈现「由正转负」的特征。
&emsp;




## 5. 相关替代方法

在模型中加入变量的二次项是寻找 U 形关系的典型处理方法之一，然而，文献中也经常见到一些其他的处理方法，比如，分组回归、面板门槛模型等。他们之间有何关系？能否相互替代呢？



### 5.1 分组回归

在模型进行异质性分析时，通常的做法是按照核心解释变量 ($x$) 在特定点上取值将样本划分成两组或多组，进而执行分组回归，并比较各组系数的差异。

文献中常用的分组标准包括：样本均值，中位数，第 33 和 66 百分位数 (将样本分成三组)。当然，在某些情况下，可以根据理论分析或数据的具体情况，选择更有意义的分组界点 (cut-point)。比如，研究退休对健康的影响时，往往会以 **55 岁** 或 **60 岁** 作为分组依据。

对于 **图 5** 中的散点图而言，我们可以分别以 $x=x_1$，$x=x_0$ 和 $x=x_2$ 为分界点进行分组回归，虽然每种情况下的系数估计值会有一些差异，但总体的定性结论不会有太大的影响。从 **图 5b** 中对比可以看出，若选择 $x=x_0$ 作为分界点，则分组回归和加入二次项的效果会非常接近。

采用二次项的方式有一些独特的好处：
- 其一，我们可以借助 `margins` 和 `marginsplot` 命令分析边际效应的变化。相比之下，分组回归只能看到两个组内的平均边际效应 (分组回归假设每个组内的边际效应均为常数)。
- 其二，进行分组回归必然涉及到组间系数差异检验问题，而这在很多情况系并不容易实施，详情参见  [「连玉君专栏 - 如何检验分组回归后的组间系数差异？」](https://zhuanlan.zhihu.com/p/28502370)。此时，可以考虑将两个组混在一起回归 (需要假设其他控制变量的系数不存在组间差异)，以便通过交乘项来捕捉两组的系数差异。
- 其三，在分组回归中，我们必须明确分界点的选取依据，同时，还需在稳健性检验中论证文中的结果是否会因为分界点的变动而发生实质性的变化。

当然，使用二次项时也有一些局限，主要在于 $x^2$ 和 $x$ 之间往往存在严重的共线性问题，当样本较小 (如 N<200)，这可能会影响系数估计的精度，甚至导致系数无法识别。此外，使用二次项大体上只能刻画 **图 1** 所示的几种情形，但当 $x$ 和 $y$ 之间存在「Z 型关系」、「斜 N 型关系」等情形时，使用分组回归的优势就可以发挥出来了。
 

![图 5a：分组回归的拟合图](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314003749.png)

> 图 5a：分组回归的拟合图


![图 5b：分组回归与加入二次项](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314003806.png)

> 图 5b：分组回归与加入二次项

#### Stata 范例：分组回归 v.s. 平方项

为了让读者对上述分析有一点直观的感受，我们可以延续 **nlsw88.dta** 的例子，来对比一下分组回归和加入平方项两种设定下的估计结果有何差异。

在分组回归中，出于稳健性考虑，我们分别以 **ttl_exp** 的样本均值 (分组 1) 和第 75 百分位数 (分组 2) 为分组界点 ，将样本分成两组。从 **图 3** 的结果来看，「分组 2」的结果应该更接近于加入平方项的结果。 下表中的会结果证实按上述猜测，具体分析留给各位读者吧。

```stata
*-分组回归

. sysuse "nlsw88.dta", clear

* 根据 ttl_exp 的样本均值分组
  qui sum ttl_exp, detail
  gen G1 = (ttl_exp>r(mean))  // r(mean) 表示 ttl_exp 的样本均值
  *gen G2 = (ttl_exp>r(p75))  // r(mean) 表示 ttl_exp 的样本均值
  global cx "age hours i.race i.industry"
  
  reg wage ttl_exp $cx if G1==0 // Group 1
  est store Lmean
  
  reg wage ttl_exp $cx if G1==1 // Group 2
  est store Rmean
  
* 根据 ttl_exp 的第 75 百分位数分组 (p75)
  qui sum ttl_exp, detail
  gen G2 = (ttl_exp>r(p75))  // r(75) 表示 ttl_exp 的 p75
  
  reg wage ttl_exp $cx if G2==0 // Group 1
  est store Lp75
  
  reg wage ttl_exp $cx if G2==1 // Group 2
  est store Rp75
  
* 对比：加入二次项
  reg wage c.ttl_exp##c.ttl_exp $cx
  est store x2
  
*-----表：回归结果-------
  local m "Lmean Rmean  Lp75 Rp75  x2"
  esttab `m', mtitle(`m') nogap compress replace ///
         b(%6.3f) s(N r2_a) drop(`drop')   ///
         star(* 0.1 ** 0.05 *** 0.01)      ///
           addnotes("*** 1% ** 5% * 10%")    ///
         indicate("种族效应 =*.race" "行业效应 =*.industry" )

------------------------------------------------------------------------------
                      分组 1                      分组 2              平方项        
              -----------------------  -------------------------  ------------
                 (1)          (2)            (3)          (4)           (5)   
               Lmean        Rmean           Lp75         Rp75            x2   
-------------------------------------  -------------------------  ------------
ttl_exp        0.295***     0.152**        0.348***     0.118         0.423***
              (5.17)       (2.08)         (8.89)       (1.00)        (3.85)   
age           -0.063       -0.133**       -0.107**     -0.042        -0.107***
             (-1.21)      (-2.34)        (-2.36)      (-0.57)       (-2.77)   
hours          0.048***     0.055***       0.052***     0.044*        0.051***
              (3.37)       (3.00)         (3.98)       (1.77)        (4.47)   
ttl_exp^2                                                            -0.006   
                                                                    (-1.45)   
_cons          3.260        8.520***       4.427*       6.449         4.600** 
              (1.20)       (2.67)         (1.80)       (1.60)        (2.11)   
种族效应         Yes          Yes            Yes          Yes           Yes   
行业效应         Yes          Yes            Yes          Yes           Yes   
------------------------------------------------------------------------------
N           1019.000     1209.000       1671.000      557.000      2228.000   
r2_a           0.098        0.065          0.112        0.096         0.123   
------------------------------------------------------------------------------
t statistics in parentheses
*** 1% ** 5% * 10%
* p<0.1, ** p<0.05, *** p<0.01
```

&emsp;

### 5.2 面板门槛模型

上文介绍的「分组回归」方法存在一个明显的局限：我们需要事先人为设定分组界点。这显然是最容易被诟病的地方。Hansen (1999, JoE) 提出的「面板门槛模型」可以克服这一问题。其基本思想是：假设分组界点可以发生在 $x \in (x_{min}, x_{max})$ 的任何位置上，那么我们就以根据模型的拟合情况 (残差平方和 RSS 越小越好) 来确定最佳的分界点。例如，我们可以假设分组界点为 **图 6** 中的 $x = 1$ 位置，则可以据此产生一个虚拟变量 $D_1$：当 $x \leq 1$ 时，$D_1=0$，否则 $D_1=1$。采用示性函数 $I (\cdot)$ 可以简单表示为：$D_1=I(x > 1)$。随后我们就可以估计如下线性模型了 (不考虑结构变化的模型设定为 $y=\alpha_{0} + \beta_{1}x+ \varepsilon$)：

$$
y=\alpha_{0}+ \alpha_1  D_1  + \beta_{1}x+\beta_{2} x \times D_1+ \varepsilon
$$

由此，可以获得当 $x=1$ 时模型的残差平方和，记为 $RSS_1$。假设 $x$ 的取值为 ${1,2,\cdots, 10}$，我们可以依次计算出 $RSS_j = \{RSS_1, RSS_2, \cdots, RSS_{10} \}$，并选择 $RSS_j$ 取最小值是对应的 $x$ 的值。例如，$RSS_7$ 最小，则可以认为当 **x=7** 时，模型最可能发生结果变化。 

上面介绍的是「单一门槛模型」的估计思路，若模型中有多个门槛，我们可以先按上述方法搜索第一个门槛，进而固定之 (相当于在模型中加入一个已知界点的交乘项)，继而搜索第二个、第三个门槛值。**图 6** 展示了一个典型的「双重门槛模型」的估计结果。

由此看来，所谓的「门槛模型」本质上就是一个「分组界点未知时的分组回归模型」。它本着「让数据说话」的基本原则，预先通过模型的拟合情况 (RSS 最小) 来寻找门槛值，进而以门槛值为基础构造寻变量及其与 $x$ 的交乘项，最终采用 OLS 估计模型即可。 

当然，具体应用过程中，还需要基于 Bootstrap 执行「门槛效应检验」，以及「门槛值是否等于真实值」等检验。详情可以查阅 Hansen (1999) 原文，或用以实现该模型的 Stata 命令的帮助文件：`help xthreg`。


![图 6：面板门槛模型图示](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/image_20200314003650.png)

> 图 6：面板门槛模型图示

&emsp;

## 6. 小结
- 使用平方项时需要计算转折点，并结合 $x$ 的取值范围讨论其经济含义；
- 最好使用 `margins` 和 `marginsplot` 命令进行边际效应及其图示分析；
- 进行分组回归和门槛模型估计也可以刻画变量之间的线性关系，尤其是当 $x$ 和 $y$ 之间的结构变化不是因为 $x$ 本身导致时，只能用这两类模型进行分析。


## 7. 参考文献

- Acock（2014) A Gentle Introduction to Stata ， Chapter 10, 12.
- Wooldridge, Introductory Econometrics A Modern Approach. 6th Edition. Sec6.3, p.153-158.
- 陈强，《高级计量经济学及 stata 应用》，第二版，p.120-123.
-  连玉君，[「连玉君专栏 - 如何检验分组回归后的组间系数差异？」](https://zhuanlan.zhihu.com/p/28502370)
- 杨柳，连玉君，[「Stata：因子变量的全攻略」](https://www.jianshu.com/p/16b08797e591)

&emsp;

## 8. 相关推文

> Note：产生如下推文列表的 Stata 命令为：   
> &emsp; `lianxh 平方项 交乘项 边际效应, md2 nocat`  
> 安装最新版 `lianxh` 命令：    
> &emsp; `ssc install lianxh, replace` 
  
  - 伊凌雪, 2021, [调节效应是否需要考虑对控制变量交乘？](https://www.lianxh.cn/details/789.html).
  - 修博文, 2024, [交乘项困惑：交互模型中的控制变量如何选择？](https://www.lianxh.cn/details/1497.html).
  - 刘聪聪, 陈点点, 2020, [Stata：interflex-交乘项该这么分析!](https://www.lianxh.cn/details/121.html).
  - 周洋, 连玉君, 李森林, 2020, [平方项 = 倒U型 ？](https://www.lianxh.cn/details/70.html).
  - 唐勇军, 2020, [Stata：交乘项的对称效应与图示](https://www.lianxh.cn/details/465.html).
  - 展一帆, 周依仿, 2021, [Logit-Probit：非线性模型中交互项的边际效应解读](https://www.lianxh.cn/details/616.html).
  - 崔娜, 2020, [Stata：内生变量和它的交乘项](https://www.lianxh.cn/details/186.html).
  - 崔娜, 2020, [Stata：内生变量的交乘项如何处理？](https://www.lianxh.cn/details/377.html).
  - 崔娜, 2021, [内生变量的交乘项如何处理？](https://www.lianxh.cn/details/500.html).
  - 曹钰潇, 2022, [Stata分位数回归I：理解边际效应和条件边际效应](https://www.lianxh.cn/details/857.html).
  - 曾颖娴, 2020, [离散型调节变量——该如何设定模型？](https://www.lianxh.cn/details/80.html).
  - 杨柳, 连玉君, 2020, [Stata因子变量：虚拟变量-交乘项批量处理](https://www.lianxh.cn/details/180.html).
  - 江鑫, 2021, [Stata：边际效应知多少？f_able命令（上）](https://www.lianxh.cn/details/689.html).
  - 江鑫, 2021, [Stata：边际效应知多少？f_able命令（下）](https://www.lianxh.cn/details/690.html).
  - 浦进博, 2025, [理解变量转换与非线性效应的八个基本准则](https://www.lianxh.cn/details/1612.html).
  - 王萃芳, 2023, [交乘项：交互作用需要交互控制](https://www.lianxh.cn/details/1141.html).
  - 祁本章, 2021, [Logit-Probit中的交乘项及边际效应图示](https://www.lianxh.cn/details/575.html).
  - 肖志文, 2024, [interflex：交乘项值得信任吗？陷阱及应对方法](https://www.lianxh.cn/details/1492.html).
  - 胡杰, 2020, [Stata：虚拟变量交乘项生成和检验的简便方法](https://www.lianxh.cn/details/406.html).
  - 袁子晴, 2020, [interactplot：图示交乘项-交互项-调节效应](https://www.lianxh.cn/details/480.html).
  - 谭炜荣, 2024, [Stata：组间边际效应差异检验-gsem](https://www.lianxh.cn/details/1442.html).
  - 谭炜荣, 2025, [Stata：组间边际效应差异检验-mecompare命令](https://www.lianxh.cn/details/1576.html).
  - 连享会, 2021, [Stata-Python交互-5：边际效应三维立体图示](https://www.lianxh.cn/details/555.html).
  - 连享会, 2020, [Stata：交乘项该如何使用？-黄河泉老师PPT](https://www.lianxh.cn/details/373.html).
  - 连小白, 2025, [交互项要不要加？理论解释与实操建议](https://www.lianxh.cn/details/1707.html).
  - 连玉君, 2020, [Stata：图示连续变量的连续边际效应](https://www.lianxh.cn/details/189.html).
  - 连玉君, 2020, [交乘项-交叉项的中心化问题](https://www.lianxh.cn/details/137.html).
  - 连玉君, 2022, [我为什么越跑越肥？交乘项系数的直观解释](https://www.lianxh.cn/details/862.html).
  - 连玉君, 杨柳, 2020, [Stata: 边际效应分析](https://www.lianxh.cn/details/64.html).
  - 连玉君, 杨柳, 2020, [Stata：Logit模型一文读懂](https://www.lianxh.cn/details/170.html).
  - 郭盼亭, 2022, [Stata：面板Logit的边际效应和处理效应估计-mfelogit](https://www.lianxh.cn/details/1127.html).
  - 金钊, 2020, [多元回归系数：我们都解释错了？](https://www.lianxh.cn/details/161.html).
  - 陈佳慧, 2024, [Stata：边际效应的故事之IV-Probit-margins-ml](https://www.lianxh.cn/details/1519.html).
  - 陈希, 2022, [Stata：如何理解三个变量的交乘项？](https://www.lianxh.cn/details/1118.html).
  - 陈美琪, 2023, [Stata：展示OLS和GLM的交乘项(一)-icalc](https://www.lianxh.cn/details/1157.html).
  - 陈美琪, 2023, [Stata：展示OLS和GLM的交乘项(二)-icalc](https://www.lianxh.cn/details/1166.html).
  - 陈贤孟, 2022, [解读交互项及其边际效应](https://www.lianxh.cn/details/1075.html).
  - 高娜娜, 2020, [三个交乘项：边际效应的手动计算及图示](https://www.lianxh.cn/details/226.html).
 


---

## 附录：文中使用的 Stata 代码汇总

```stata

*------------------
*- 平方项知多少？
*------------------

* Author: 周洋、李森林、连玉君
* Date: 2019.12.23
* E-mail: StataChina@163.com


*-图示非线性关系
 . sysuse "nlsw88.dta", clear
 . twoway (scatter wage ttl) (qfit wage ttl, lcolor(red))

 
*-回归分析和图示转折点

 . sysuse "nlsw88.dta", clear
 . gen ttl_exp2 = ttl_exp*ttl_exp
 . reg wage ttl_exp ttl_exp2  // 也可以添加一些控制变量

 . global tp: dis %3.1f -_b[ttl_exp]/(2*_b[ttl_exp2]) //转折点
 . dis "Turn point = " $tp //能够得到 Turn point = 29.6
  
*---------------------------图示-----------------------------
 . local b0 = _b[_cons]
 . local b1 = _b[ttl_exp]
 . local b2 = _b[ttl_exp2]
 . sum ttl_exp
 . local min:  dis %3.1f r(min)
 . local max:  dis %3.1f r(max)
 . #delimit
 . twoway ( function y = `b2'*x^2 + `b1'*x + `b0',
           range(`min' `max') )
         ( function y = `b2'*x^2 + `b1'*x + `b0',
           range(`max' 40) lp(dash) )
       ,
       ytitle("wage") xtitle("ttl_exp")
       xline(`min',lc(red) lw(*1.5))
       xline($tp,  lp(dash) lc(green))
       xline(`max',lc(red) lw(*1.5))
       xlabel(`min' "Min (`min')" 5(5)25
              `max' "Max (`max')", angle(50))
       legend(off)
       ;
  . #delimit cr
  . graph export "fig_Ushape.png", replace  //保存图形
  
  
*-边际效应分析  
  
. sysuse "nlsw88.dta", clear
. global cx ""   //可以放入控制变量
. reg wage c.ttl_exp##c.ttl_exp $cx 
. sum ttl_exp
. global min: dis %4.1f r(min)
. global max: dis %4.1f r(max)
. margins, dydx(ttl_exp) at(ttl_exp=($min 1(3)28 $max)) 
. graph export "fig_margins.png", replace  //保存图形 


*-分组回归

. sysuse "nlsw88.dta", clear

* 根据 ttl_exp 的样本均值分组
  qui sum ttl_exp, detail
  gen G1 = (ttl_exp>r(mean))  // r(mean) 表示 ttl_exp 的样本均值
  *gen G2 = (ttl_exp>r(p75))  // r(mean) 表示 ttl_exp 的样本均值
  global cx "age hours i.race i.industry"
  
  reg wage ttl_exp $cx if G1==0 // Group 1
  est store Lmean
  
  reg wage ttl_exp $cx if G1==1 // Group 2
  est store Rmean
  
* 根据 ttl_exp 的第 75 百分位数分组 (p75)
  qui sum ttl_exp, detail
  gen G2 = (ttl_exp>r(p75))  // r(75) 表示 ttl_exp 的 p75
  
  reg wage ttl_exp $cx if G2==0 // Group 1
  est store Lp75
  
  reg wage ttl_exp $cx if G2==1 // Group 2
  est store Rp75
  
* 对比：加入二次项
  reg wage c.ttl_exp##c.ttl_exp $cx
  est store x2
  
*-----表：回归结果-------
  local m "Lmean Rmean  Lp75 Rp75  x2"
  esttab `m', mtitle(`m') nogap compress replace ///
         b(%6.3f) s(N r2_a) drop(`drop')   ///
         star(* 0.1 ** 0.05 *** 0.01)      ///
           addnotes("*** 1% ** 5% * 10%")    ///
         indicate("种族效应 =*.race" "行业效应 =*.industry" )
```

