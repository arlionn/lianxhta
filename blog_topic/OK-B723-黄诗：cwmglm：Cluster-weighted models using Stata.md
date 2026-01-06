> **作者**：黄诗 (暨南大学) 
> **邮箱**：<huangshi100484@126.com> 

&emsp;

> **编者按**：本文主要整理自以下内容，特此致谢！
> Spinelli, D., Ingrassia, S., & Vittadini, G. (2024). Cluster-weighted models using Stata. The Stata Journal, 24(4), 711–745. [Link](https://journals.sagepub.com/doi/10.1177/1536867X241297922), [PDF](https://d1wqtxts1xzle7.cloudfront.net/115819483/2024Stata_J_AAM-libre.pdf?1717952378=&response-content-disposition=inline%3B+filename%3DCluster_weighted_models_using_Stata.pdf&Expires=1762007528&Signature=T3gj5qLiFfzwoK23V~MuM8WvYKTTAwl5wR8h7as1fY9d0gYSF2KcpStd-urkxhbAZDX4cg-V3qKHHzfK94qGtpb~RcchsDHhamvKaCJDqr0ccsPGbUMXPvxgz0Zc3PT6Ryvi3nWH8lDJULGOysDyyYee3eVu-99p99Aa2Wpi1hUDE1eg8HvbjgctUgjTokezWpj3Y~N9sqPPjgWdRhsyPrbNQNJ5XBe5e-ISG1Dn2S1b5jr~SeD-43XJa2-q83-3GbEyZBJP3DNHti3xZ7dhM4LV2o4SafWiqhEsx43iJUY6SZkgqWPv2eTcf8hzGDnX9kD-TaRy8Zd0LbQmQImWCQ__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA), [Google](<https://scholar.google.com/scholar?q=Cluster-weighted models using Stata>).


&emsp;  


- **Title**: Stata 新命令推介：用 cwmglm 实现复杂的聚类回归
- **Keywords**: cwmglm, 聚类分析, 广义线性模型, 简约高斯模型
 
&emsp; 


## 1. 引言

在计量经济学实证研究中，我们经常面临一个棘手的问题：**样本的异质性**（**Heterogeneity**）。

例如，在研究企业的投资行为时，国企和私企的行为模式可能截然不同；在分析患者康复时间时，不同风险等级的病人群体差异巨大。如果你不知道样本中存在这些“潜类别”（Latent Classes），直接做全样本 OLS 回归，结果显然会有偏差。

传统的解决方案是使用**有限混合回归** (**Finite Mixture Regression**, **FMR**)。FMR 虽然强大，但它有一个很强的假设：**分配独立性** (**Assignment Independence**)。也就是说，FMR 认为个体的特征变量（如年龄、收入、身高等协变量 $\bm{X}$）不会影响他属于哪一类。

但在现实中，这个假设往往是不成立的。比如，男性和女性（潜在类别）不仅体重与身高的回归关系不同，**身高的分布本身也不同**（男性通常更高）。如果忽略了协变量分布的差异，FMR 的估计效果就会大打折扣。

为了解决这个问题，**聚类加权模型** (**Cluster-Weighted Models**, **CWM**) 应运而生。今天介绍的 `cwmglm` 命令，填补了 Stata 在这一领域的空白。

&emsp;

## 2. 理论背景

### 2.1 CWM 的联合概率密度

与传统回归关注 $p(Y|\bm{X})$ 不同，CWM 旨在对数据的联合分布 $(\bm{X}, Y)$ 进行建模。假设总体由 $K$ 个潜在类别组成，CWM 的概率密度函数定义为：

$$
f(\bm{x},y;\boldsymbol{\theta})=\sum_{j=1}^{K}\pi_{j} \cdot \underbrace{p(y|\bm{x};\boldsymbol{\zeta}_{j})}_{\text{回归模型}} \cdot \underbrace{q(\bm{x};\boldsymbol{\psi}_{j})}_{\text{协变量分布}}
$$

在这个公式中：
- $\pi_j$ ：第 $j$ 个潜在类别的混合比例 (权重)。
- $p(y|\bm{x};\boldsymbol{\zeta}_{j})$ ：第 $j$ 类中因变量的条件密度 (即我们熟悉的回归部分)。这可以通过广义线性模型 (GLM) 来设定，如高斯、Logit 或泊松回归。
- $q(\bm{x};\boldsymbol{\psi}_{j})$ ：第 $j$ 类中协变量的边际密度。这是 CWM 与 FMR 的关键区别，它允许协变量 $\bm{X}$ 在不同类别中服从不同的分布（比如不同组的均值和方差不同）。

### 2.2 进阶设定：简约高斯模型与特征值分解

当协变量 $\bm{X}$ 为多元连续变量时，通常假设其服从多元高斯分布。然而，直接估计每个类别的协方差矩阵往往面临**参数爆炸**的问题，容易导致过拟合。

为了解决这一难题，`cwmglm` 引入了 Celeux 和 Govaert (1995) 提出的`简约模型` (`Parsimonious Models`)。该方法通过特征值分解，将第 $j$ 类的协方差矩阵 $\bm{\Sigma}_j$ 分解为三个具有明确几何意义的参数：
$$
\bm{\Sigma}_{j}=\lambda_{j}\bm{D}_{j}\bm{A}_{j}\bm{D}_{j}^{\prime}
$$
这三个参数分别控制聚类的几何特征：
- $\lambda_j$ (**体积 Volume**)：控制聚类在空间中占据的大小。

- $\bm{A}_j$ (**形状 Shape**)：控制聚类散点的分布形态（如圆形、椭圆形）。
  
- $\bm{D}_j$ (**方向 Orientation**)：控制聚类的主轴方向。通过对这三个参数在不同类别间施加“**相等 (Equal)**”或“**可变 (Variable)**”等约束，我们可以组合出如下 14 种 不同的协方差结构模型。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/huangshi-B723-Fig01.png.png)

&emsp;

## 3. Stata 实操

### 3.1 命令简介

`cwmglm` 命令基于期望最大化 (EM) 算法，用于估计聚类加权模型。其语法设计灵活，允许用户分别指定因变量的条件分布和协变量的边际分布。基本语法结构如下：
```Stata
cwmglm depvar indepvars [if] [in], k(#) family(familyname) [marginalization options] [model options]
```
**核心选项**

**1. 模型基础设定**
- `k(#)`：设定潜在类别 (Latent Classes) 的数量 $K$ 。

  - 例如 `k(2)` 表示假设样本由 2 个子群体混合而成。

  - 若不指定，默认为随机初始化或根据 `start()` 选项确定。

**2. 因变量 ($Y|X$) 分布设定**
- `family(string)`：指定因变量 `depvar` 的条件分布类型。
  - `gaussian`：高斯分布（默认），适用于连续变量（如体重、收入）。

  - `binomial`：二项分布，适用于二元变量（如是否患病）。

  - `poisson`：泊松分布，适用于计数变量（如住院天数）。

**3. 协变量 ($\bm{X}$) 边际分布设定** CWM 的核心在于对协变量建模。用户需根据协变量的类型，将其放入对应的选项中：
- `xnormal(varlist)`：指定服从**多元正态分布**的连续协变量（如身高、年龄）。

- `xpoisson(varlist)`：指定服从**泊松分布**的计数协变量。

- `xbinomial(varlist)`：指定服从**二项分布**的二元协变量。

- `xmultinomial(varlist)`：指定服从**多项分布**的分类协变量。

**4. 简约高斯模型选项** (**Parsimonious Models**)当使用 `xnormal()` 指定了连续协变量时，我们需要设定各类别间协方差矩阵 $\Sigma_j$ 的约束形式。这是 CWM 的一大特色，共有 14 种模型可选，常见的包括：
- `vvv` (默认)：**全变模型**。假设各类的体积 (Volume)、形状 (Shape) 和方向 (Orientation) 均不同。这是最灵活但也最耗参数的模型。

- `eee`：**全等模型**。假设各类的体积、形状和方向均相同（即所有类别的协方差矩阵相等 $\Sigma_1 = \Sigma_2 = \dots = \Sigma_K$）。这是最简约的模型之一。

- 其他选项包括：`vii` (对角矩阵), `eei`, `vev` 等。

**注意**：在下文的学生体测数据实战中，我们将使用 `eee` 选项，这意味着我们假设男性和女性的身高分布虽然均值不同，但其方差-协方差结构是相似的。


### 3.2 具体应用：学生体测数据分类

以本例使用的 Students dataset 为例，数据包含 270 名大学生的体测数据：

- weight：体重（因变量）

- height：身高（协变量 1）

- heightf：父亲身高（协变量 2）

- gender：性别（潜在的真实类别）

研究目标：我们希望构建一个模型，仅利用身高和体重数据来预测体重，并试图**在不告知模型“性别”的情况下，自动识别出样本中的男性和女性群体。**

由于男性和女性在身高分布上存在显著差异（男性均值通常高于女性），且身高对体重的回归系数也可能不同，这正是 **CWM**大显身手的地方。

#### 3.2.1 CWM 模型估计

我们假设总体由 2 个潜在类别组成（$K=2$），并设定协变量（身高、父亲身高）服从多元正态分布。为了简化模型，我们采用简约设定 EEE（即假设两组的协方差矩阵在体积、形状和方向上均相等）。
```stata
. use students.dta, clear

* 估计 CWM 模型
* k(2): 设定 2 个潜在类别
* xnormal(...): 指定身高和父亲身高为服从正态分布的随机协变量
* eee: 设定协方差结构为全等 (Equal Volume, Shape, Orientation)
. cwmglm weight height heightf, k(2) xnormal(height heightf) eee

initializing EM...
...
EM iteration 17: log-likelihood= -2648.16080
```

估计结果输出：
```Stata

Prior Probabilities
----------------------
       g1          g2
----------------------
 .4369902    .5630098
----------------------

Clustering Table
----------------------
       g1          g2
----------------------
      117         153
----------------------
```
结果解读： 模型成功收敛。估计出的两个类别的先验概率(Prior Probabilities)分别为 43.7% 和 56.3%。聚类表(Clustering Table)显示 g1 组有 117 人，g2 组有 153 人。
#### 3.2.2 结果可视化与验证

执行上述命令后，模型内部已经计算出了每个样本属于 g1 或 g2 的概率。为了验证分类效果，我们需要将预测结果提取出来，并与真实的 **gender** 变量进行交叉对比。

我们使用 `predict` 命令生成分类变量，其中选项 `map` 代表 **Maximum A Posteriori** (**最大后验概率**) 分类，即“硬分类”。

```Stata
* 生成分类预测结果
. predict group, map  // 生成硬分类结果

* 与真实性别进行交叉列表验证
. tab group gender    // 与真实性别交叉列表

          |     Gender       |
    group |      F         M |   Total
----------+------------------+---------
        1 |    149         4 |     153
        2 |      2       115 |     117
----------+------------------+---------
     Total|    151       119 |     270
```

结果显示，CWM 非常准确地重构了性别分组：

- **Group 1**：在被模型分到这一类的 153 人中，有 149 人是女性。这说明 **g1 对应的是“女性”群体**。

- **Group 2**：在被模型分到这一类的 117 人中，有 115 人是男性。这说明 **g2 对应的是“男性”群体**。

- **准确率**：模型仅凭身高和体重数据，就实现了 $(149+115)/270 \approx 97.8\%$ 的分类准确率。

这验证了 CWM 的核心假设：通过同时对 $Y|X$（体重|身高）和 $X$（身高分布）建模，模型成功捕捉到了男女在身高分布上的显著差异，从而实现了精准的无监督分类。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/huangshi-B723-Fig02.png.png)

#### 3.2.3 与 FMR 模型对比

如果我们使用传统的 有限混合回归 (FMR)，即假设身高的分布在男女之间没有差异（分配独立性），结果会怎样？

我们可以使用 `cwmcompare` 命令，将上述 CWM 模型与 FMR 模型（即省略 `xnormal` 选项的模型）进行 AIC/BIC 对比。
```Stata
* 1. 保存刚才的 CWM 结果
. estimates store cwm

* 2. 估计 FMR 模型 (不指定 xnormal 选项)
. cwmglm weight height heightf, k(2)
. estimates store fmm

* 3. 比较两个模型
. cwmcompare cwm fmm

information criteria for cwmglm estimates
       |       AIC        BIC
------------------------------
   cwm |  5328.322   5385.896
   fmm |  5629.813   5680.191
```
结论：

- **AIC/BIC 比较**：CWM 的 AIC (5328) 远小于 FMR 的 AIC (5629)。

- **决策建议**：数据强烈支持 CWM 模型。这表明在分析体重与身高的关系时，忽略“不同性别身高分布不同”这一事实（即使用 FMR），会导致模型拟合度显著下降。因此，考虑协变量分布的 CWM 是更优的选择。

#### 3.2.4 进阶功能

除了上述的基础高斯回归，`cwmglm` 还提供了许多强大的进阶功能，以适应复杂的经济学研究场景：

1. **支持多种分布族**： 如果你的因变量不是连续的，而是计数数据（如住院天数）或二元数据（如是否购买），可以通过 `family(poisson)` 或 `family(binomial)` 来设定。文中的 COVID-19 案例就成功应用了泊松 CWM 模型 。

2. **灵活的协变量设定**： 协变量不仅可以是连续的，还可以是二项分布（如性别哑变量）、泊松分布（如计数特征）或多项分布。命令选项 `xpoisson()`, `xbinomial() `等让建模更加灵活 。

3. **简约高斯模型** (**Parsimonious Models**)： 对于多元正态协变量，为了避免参数过多，`cwmglm` 引入了基于特征值分解的 14 种协方差结构（如 EEE, VVV, EVI 等）。这在 Stata 中是首创的功能，能有效防止过拟合 。

4. **稳健的推断**： 提供 `cwmbootstrap` 命令，支持通过自举法（Bootstrap）获取参数的标准误，确保统计推断的可靠性 。

&emsp;

## 4. 总结
`cwmglm` 为 Stata 用户提供了一个强大的工具，用于处理具有未观测异质性的复杂数据。相比于传统的混合回归，CWM 的核心优势在于：

1. **更符合现实的假设**：允许协变量分布在不同类别间变化 (分配依赖性)。

2. **更灵活的建模**：支持多种 GLM 分布和 14 种简约高斯协方差结构。

3. **完善的后估功能**：提供了 `cwmcompare` (模型比较) 和 `cwmbootstrap` (自举法推断) 等工具。

对于经济学研究者而言，如果您的样本可能由不同的子群体构成，且这些子群体在特征变量 (协变量) 上存在分布差异，那么 CWM 将是一个比传统固定效应或简单混合模型更优的选择。
