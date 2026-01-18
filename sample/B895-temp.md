
> **作者：** 连享会课程组  
> **邮箱：** <lianxh@lianxh.cn>  

&emsp;

> **Source 1：** Cinelli, C., Ferwerda, J., & Hazlett, C. (2024). Sensemakr: Sensitivity Analysis Tools for OLS in R and Stata. *Observational Studies*, 10(2), 93–127. [Link](https://doi.org/10.1353/obs.2024.a946583), [PDF](https://muse.jhu.edu/pub/56/article/946583/pdf), [Google](<https://scholar.google.com/scholar?q=Sensemakr:+Sensitivity+Analysis+Tools+for+OLS+in+R+and+Stata>).  
> **Source 2：** Cinelli, C., & Hazlett, C. (2020). Making sense of sensitivity: Extending omitted variable bias. *Journal of the Royal Statistical Society: Series B (Statistical Methodology)*, 82(1), 39–67. [Link](https://doi.org/10.1111/rssb.12348), [PDF](http://sci-hub.ren/10.1111/rssb.12348), [Google](<https://scholar.google.com/scholar?q=Making+sense+of+sensitivity:+Extending+omitted+variable+bias>).  

这篇推文是 “遗漏变量？敏感性分析！新命令 sensemakr–T310 主页版已发布” 的姊妹篇。上一份推文用 Stata 演示 `sensemakr` 的基本用法；本篇完全基于 R 包 `sensemakr`，并且把理论部分讲得更细，同时加入 bootstrap 和交互项两类进阶场景。

---

# 遗漏变量敏感性分析：sensemakr 的理论与 R 实操

## 1. 为什么要做敏感性分析

在回归分析中，我们最常见的一种担忧是“遗漏变量偏误”(omitted variable bias, OVB)。例如，我们估计下面的线性回归：

$$
Y_i = \alpha + \tau D_i + X_i'\beta + \varepsilon_i
$$

- $Y_i$ 是结果变量
- $D_i$ 是我们关心的处理变量或核心解释变量
- $X_i$ 是控制变量向量
- $\tau$ 是我们关心的系数

如果存在某个未观测混杂因素 $Z_i$ 同时影响 $D_i$ 与 $Y_i$，那么真实模型更接近：

$$
Y_i = \alpha + \tau D_i + X_i'\beta + \gamma Z_i + u_i
$$

但由于 $Z_i$ 不可得，我们只能估计不包含 $Z_i$ 的回归，导致 $\hat\tau$ 可能有偏误。

问题是：

- 现实研究中，遗漏变量往往“说得出来，但拿不到数据”
- 这时简单地写一句 “可能存在遗漏变量” 对读者帮助不大
- 更有用的追问是：  
  “遗漏变量需要多强，才能把我们的结论推翻？”

`sensemakr` 的核心思想，就是把“遗漏变量有多强”这件事，用一组可以计算的量来刻画，并把结论转换成可复核、可讨论的数值。

---

## 2. 例子：Darfur 冲突与和平态度

### 2.1 背景与变量

`darfur` 数据来自对乍得东部达尔富尔难民的问卷调查，研究者关心：直接遭遇暴力伤害是否会影响人们对和平的态度。

一个常用的 OLS 设定是：

$$
Y_i = \alpha + \tau D_i + X_i'\beta + \mu_{v(i)} + \varepsilon_i
$$

- $Y_i$：`peacefactor`，和平态度指数
- $D_i$：`directlyharmed`，是否被直接伤害 (0/1)
- $X_i$：`female`、`age`、`pastvoted`、`farmer_dar`、`herder_dar`、`hhsize_darfur` 等控制变量
- $\mu_{v(i)}$：村庄固定效应 (`village`)

OLS 回归通常得到 $\hat\tau > 0$ 且显著，这看起来意味着“暴力经历反而提升了对和平的支持”。

但争议也很自然：

- 暴力冲突可能更集中于村庄中心，而住在中心的人可能有不同的政治倾向
- 袭击者可能更偏好攻击财富更高或更活跃的人群
- 既有政治态度可能影响个人行为，从而影响其暴露在暴力风险下的概率
- 上述因素之间可能还存在交互或非线性

如果这些关键因素不可观测，结论稳健性就需要更系统的讨论。

---

## 3. 理论基础：用 partial R2 描述遗漏变量强度

### 3.1 两个回归与两个 residual

`sensemakr` 的推导从两个“残差化”(residualization)开始。

 1. **处理残差**  
先把 $D$ 对 $X$ 回归，取残差：

$$
D = X\pi + r_D
$$

其中 $r_D$ 是在控制 $X$ 后，$D$ 剩下的那部分波动。

 2. **结果残差**  
再把 $Y$ 对 $(D, X)$ 回归，取残差：

$$
Y = \tau D + X\beta + r_Y
$$

其中 $r_Y$ 是在控制 $(D, X)$ 后，$Y$ 剩下的那部分波动。

我们担心的未观测混杂 $Z$，本质上就是能同时解释 $r_D$ 与 $r_Y$ 的东西。

---

### 3.2 两个 partial R2

`sensemakr` 用两个偏 $R^2$ (partial $R^2$) 来量化 $Z$ 的强度：

 1. **$Z$ 对处理的解释力**

$$
R^2_{D \sim Z \mid X}
$$

它表示在控制 $X$ 后，$Z$ 能解释 $D$ 剩余波动的比例。

 2. **$Z$ 对结果的解释力**

$$
R^2_{Y \sim Z \mid D, X}
$$

它表示在控制 $(D, X)$ 后，$Z$ 能解释 $Y$ 剩余波动的比例。

直觉非常直接：

- 如果 $R^2_{D \sim Z \mid X}$ 很小，说明 $Z$ 很难影响“谁会受到处理”
- 如果 $R^2_{Y \sim Z \mid D, X}$ 很小，说明 $Z$ 很难影响“在控制 $D, X$ 后，结果变量剩下的那部分”
- 两者任何一个很小，OVB 就不容易大到推翻结论

---

### 3.3 由 partial R2 得到偏误界 (bias bound)

在经典 OVB 框架下，Cinelli 和 Hazlett 给出一个非常关键的结论：

在给定 $(R^2_{D \sim Z \mid X}, R^2_{Y \sim Z \mid D, X})$ 时，$\tau$ 的偏误可以写成一个封闭形式的表达式。

通常我们更关心“最坏情况偏误”，它可以写成：

$$
\left|\text{Bias}(\hat\tau)\right|
=
\text{SE}(\hat\tau_{\text{res}})
\cdot
\sqrt{\frac{\text{df}}{1 - R^2_{D \sim Z \mid X}}}
\cdot
\sqrt{R^2_{Y \sim Z \mid D, X} \cdot R^2_{D \sim Z \mid X}}
$$

其中：

- $\hat\tau_{\text{res}}$ 是未控制 $Z$ 的受限回归估计
- $\text{SE}(\hat\tau_{\text{res}})$ 是其标准误
- $\text{df}$ 是自由度

这个式子给了一个明确的操作逻辑：

- 只要你给出一个“假想混杂强度”
- `sensemakr` 就可以告诉你：系数最多会被推走多少

于是，敏感性分析的核心问题变成：

> 你认为未观测混杂在两个 partial $R^2$ 上，最大可能达到多少？

---

### 3.4 一个非常实用的中间量：处理变量的 partial R2

很多时候，我们很难直接判断 “$R^2_{Y \sim Z \mid D, X} = 0.15$ 算强还是弱”。

`sensemakr` 给出一个更可解释的量：

$$
R^2_{Y \sim D \mid X}
$$

它表示在控制 $X$ 后，$D$ 对 $Y$ 的 partial $R^2$。

并且它和 t 统计量有直接关系：

$$
R^2_{Y \sim D \mid X} = \frac{t^2}{t^2 + \text{df}}
$$

这意味着你看到回归表里的 t 值，就能立刻得到 $D$ 在结果中“解释了多少剩余方差”。

---

## 4. Robustness Value：把敏感性分析压缩成一个数

### 4.1 RV 的含义

`sensemakr` 输出中最常被引用的两个数字是：

- $RV_q$：把点估计推到 0 所需的最小“对称混杂强度”
- $RV_{q,\alpha}$：把估计推到不显著 (给定 $\alpha$) 所需的最小“对称混杂强度”

“对称强度”指的是：

$$
R^2_{D \sim Z \mid X} = R^2_{Y \sim Z \mid D, X}
$$

换句话说，RV 把二维的敏感性空间压缩成一个单值：

- 若真实混杂同时在处理方程与结果方程中解释的残差比例都小于 RV，那么它不足以推翻结论

### 4.2 如何阅读 RV

以 $q = 1$ 为例：

- $RV_{q=1}$ 越大，说明要把估计完全推到 0，需要非常强的混杂
- $RV_{q=1,\alpha=0.05}$ 越大，说明要把显著性击穿，需要非常强的混杂

RV 不是告诉你“有没有遗漏变量”，而是告诉你：

> 若要反驳当前结论，你必须相信一个多强的遗漏变量存在

---

## 5. Benchmarking：用一个观测变量做参照物

### 5.1 为什么要 benchmark

很多争论点并不是 “$R^2 = 0.10$ 大不大”，而是：

- “这个未观测混杂真的可能比 `female` 还强吗？”
- “它真的可能比整组村庄固定效应还强吗？”

这时我们可以引入一个基准变量 $B$ (benchmark covariate)，用“倍数”做比较：

- 在处理方程中，假设 $Z$ 与 $D$ 的相关强度相当于 $B$ 的 $k_D$ 倍
- 在结果方程中，假设 $Z$ 与 $Y$ 的相关强度相当于 $B$ 的 $k_Y$ 倍

`sensemakr` 会把这样的倍数设定，自动转换成对应的 partial $R^2$，再进一步转换成偏误与调整后的估计结果。

### 5.2 读者更关心的表达方式

在论文写作中，benchmarking 产生的表达往往更“可读”：

- “即使存在一个未观测混杂，在处理方程和结果方程中的解释力都达到 `female` 的 2 倍，该结论仍然成立”
- “要推翻结论，需要一个比整组村庄固定效应更强的混杂，这在语境中不太可信”

---

## 6. R 实操：复现 Darfur 的敏感性分析

### 6.1 安装与载入数据

```r
# 第一次使用时安装
# install.packages("sensemakr")

library(sensemakr)

# 加载示例数据
data("darfur")

# 简单查看变量
str(darfur)
summary(darfur)
````

### 6.2 基准 OLS 回归

```r
# 基准模型：和平态度 ~ 是否被直接伤害 + 控制变量 + 村庄固定效应
darfur_model <- lm(
  peacefactor ~ directlyharmed + female + age + farmer_dar + herder_dar +
    pastvoted + hhsize_darfur + village,
  data = darfur
)

summary(darfur_model)
```

这一步得到的回归表，就是你平时论文中的 baseline 结果。

### 6.3 一行代码做敏感性分析

```r
# 对 directlyharmed 的系数做敏感性分析
darfur_sens <- sensemakr(
  model = darfur_model,
  treatment = "directlyharmed",      # 指定要分析的系数名称
  benchmark_covariates = "female",   # 选择 female 做 benchmark
  kd = 1:3,                          # 1x, 2x, 3x benchmark 的强度
  q = 1,
  alpha = 0.05,
  reduce = TRUE                      # 假定混杂导致“削弱效应”的方向 (更保守)
)

# 输出摘要结果
summary(darfur_sens)
```

在输出中，你会看到：

* `R2yd.x`：$R^2_{Y \sim D \mid X}$ (处理变量的 partial $R^2$)
* `RV_q`：$RV_{q=1}$
* `RV_qa`：$RV_{q=1,\alpha=0.05}$

并会列出在 `female` 的 1-3 倍强度假设下，系数如何变化。

### 6.4 画图：系数等值线图与 t 等值线图

```r
# 系数敏感性等值线
plot(darfur_sens)

# t 统计量敏感性等值线
plot(darfur_sens, sensitivity.of = "t-value")

# 最坏情况图 (extreme plot)
plot(darfur_sens, type = "extreme")
```

图形的坐标轴含义固定：

* 横轴：$R^2_{D \sim Z \mid X}$
* 纵轴：$R^2_{Y \sim Z \mid D, X}$

图上每一点，都对应一个“假想混杂强度”，并映射到一个“调整后的估计结果”。

---

## 7. 进阶一：把多组变量作为 benchmark

### 7.1 为什么要用变量组

单个 benchmark 有时太弱，比如 `female` 可能解释力有限。

更常见的稳健性表述是：

* “遗漏变量不太可能比地区固定效应还强”
* “遗漏变量不太可能比行业 + 年份固定效应还强”

`sensemakr` 支持用变量组作为 benchmark。

### 7.2 示例：把村庄固定效应整组作为 benchmark

```r
# 提取回归中与 village 相关的虚拟变量名
village_terms <- grep(
  pattern = "^village",
  x = names(coef(darfur_model)),
  value = TRUE
)

# 用 village 这一组做 benchmark
group_sens <- sensemakr(
  model = darfur_model,
  treatment = "directlyharmed",
  benchmark_covariates = list(village_block = village_terms),
  kd = c(0.2, 0.5, 1.0),    # 示例：0.2x, 0.5x, 1.0x
  q = 1,
  alpha = 0.05,
  reduce = TRUE
)

summary(group_sens)
plot(group_sens)
```

这种写法非常适合在实证论文中形成一句更强的稳健性表述：

* “需要一个在解释力上接近整组村庄固定效应的遗漏变量，才足以推翻结论”

---

## 8. 进阶二：Bootstrap 下的敏感性分析

这一部分的目标很明确：

* 不是只用 OLS 渐近标准误
* 而是把“抽样不确定性”也叠加进来

### 8.1 算法流程 (伪代码)

下面给出一个最常见的非参数 bootstrap 版本。

1. **Step 1**: 设定 bootstrap 次数 $B$
2. **Step 2**: 对 $b = 1, 2, \ldots, B$ 循环

   * 从样本中有放回抽取 $n$ 个观测，形成 bootstrap 样本 $S_b$
   * 在 $S_b$ 上拟合 OLS 回归，得到 $\hat\tau_{\text{res}}^{(b)}$
   * 在同样的 $k_D, k_Y$ 设定下运行 `sensemakr()`
   * 记录调整后的 $\hat\tau^{(b)}_{\text{adj}}$
3. **Step 3**: 用 ${\hat\tau^{(b)}*{\text{adj}}}*{b=1}^B$ 的分位数构造置信区间

这个流程有一个优势：

* 你可以自由改变 “假想混杂强度”
* 看 CI 是否仍远离 0

### 8.2 R 代码示例 (非聚类 bootstrap)

```r
set.seed(20250118)

B <- 500                         # bootstrap 次数 (教学演示用)
n <- nrow(darfur)

tau_adj_boot <- numeric(B)

for (b in 1:B) {
  # 1. 有放回抽样
  idx_b <- sample.int(n = n, size = n, replace = TRUE)
  dat_b <- darfur[idx_b, ]
  
  # 2. 回归
  model_b <- lm(
    peacefactor ~ directlyharmed + female + age + farmer_dar + herder_dar +
      pastvoted + hhsize_darfur + village,
    data = dat_b
  )
  
  # 3. 对同样的 treatment 做敏感性分析
  sens_b <- sensemakr(
    model = model_b,
    treatment = "directlyharmed",
    benchmark_covariates = "female",
    kd = 2,                        # 示例：假想混杂为 female 的 2 倍强
    q = 1,
    alpha = 0.05,
    reduce = TRUE
  )
  
  # 4. 记录调整后的点估计
  # bounds 表中的 adjusted_estimate 就是考虑假想混杂后的估计
  tau_adj_boot[b] <- sens_b$bounds[1, "adjusted_estimate"]
}

# 5. 输出 percentile bootstrap 置信区间
quantile(tau_adj_boot, probs = c(0.025, 0.5, 0.975))
```

解释：

* 上面代码相当于在 “female 2 倍混杂” 的设定下
* 得到一个 bootstrap 分布，再构造 CI
* 你可以把 `kd = 1, 2, 3` 分别跑一遍，对比 CI 的移动幅度

### 8.3 聚类 bootstrap (按 village 聚类)

在很多应用中，我们会对村庄层面做聚类抽样，思路是：

* 每次 bootstrap 抽取村庄
* 抽中哪个村庄，就把该村庄所有观测一起放进来

```r
set.seed(20250118)

clusters <- unique(darfur$village)
G <- length(clusters)

B <- 500
tau_adj_boot_cl <- numeric(B)

for (b in 1:B) {
  # 1. 聚类层面有放回抽样
  sampled_clusters <- sample(clusters, size = G, replace = TRUE)
  
  # 2. 取出抽中的村庄对应的所有观测
  dat_b <- darfur[darfur$village %in% sampled_clusters, ]
  
  # 3. 回归
  model_b <- lm(
    peacefactor ~ directlyharmed + female + age + farmer_dar + herder_dar +
      pastvoted + hhsize_darfur + village,
    data = dat_b
  )
  
  # 4. 敏感性分析
  sens_b <- sensemakr(
    model = model_b,
    treatment = "directlyharmed",
    benchmark_covariates = "female",
    kd = 2,
    q = 1,
    alpha = 0.05,
    reduce = TRUE
  )
  
  tau_adj_boot_cl[b] <- sens_b$bounds[1, "adjusted_estimate"]
}

quantile(tau_adj_boot_cl, probs = c(0.025, 0.5, 0.975))
```

从研究写作角度，这段程序常用于生成一句更“硬”的表述：

* “在聚类 bootstrap 推断下，并允许一个相当于 female 2 倍强的遗漏变量，结论仍保持为正且显著”

---

## 9. 进阶三：含交互项的敏感性分析

### 9.1 交互项模型与目标参数

考虑一个典型的异质性模型：

$$
Y_i = \alpha + \tau_1 D_i + \tau_2 M_i + \tau_3 (D_i \times M_i) + X_i'\beta + \varepsilon_i
$$

* $M_i$ 是调节变量，例如 `female`
* $\tau_3$ 是交互项系数，刻画异质性效应

现实写作中，读者常问：

* “你这个异质性结果会不会更容易被遗漏变量推翻？”

此时你可以直接对交互项系数做 `sensemakr`。

### 9.2 R 实操：对交互项系数做敏感性分析

```r
# 交互项模型
darfur_int <- lm(
  peacefactor ~ directlyharmed * female + age + farmer_dar + herder_dar +
    pastvoted + hhsize_darfur + village,
  data = darfur
)

summary(darfur_int)

# 对交互项 directlyharmed:female 做敏感性分析
int_sens <- sensemakr(
  model = darfur_int,
  treatment = "directlyharmed:female",  # 注意这里的系数名称
  benchmark_covariates = "female",
  kd = 1:3,
  q = 1,
  alpha = 0.05,
  reduce = TRUE
)

summary(int_sens)
plot(int_sens, sensitivity.of = "t-value")
```

说明：

* 交互项的名称在 R 中是 `directlyharmed:female`
* 这类分析可以直接回答：
  “需要多强的遗漏变量，才能让交互项结果变得不显著？”

如果你希望 benchmark 更贴近交互项语境，也可以把变量组作为 benchmark，例如把 `female` 与交互项一起作为一组 benchmark：

```r
int_sens2 <- sensemakr(
  model = darfur_int,
  treatment = "directlyharmed:female",
  benchmark_covariates = list(female_block = c("female", "directlyharmed:female")),
  kd = 1:3,
  q = 1,
  alpha = 0.05,
  reduce = TRUE
)

summary(int_sens2)
plot(int_sens2)
```

---

## 10. 对照：同一思想在 Stata 中如何写

虽然本篇重点是 R，但很多读者会希望把代码迁移回 Stata。

Stata 版的最小语法结构是：

```stata
sensemakr depvar covars, treat(varname) benchmark(varname)
```

以 Darfur 为例，写法类似：

```stata
net get sensemakr.pkg
use darfur.dta, clear

sensemakr peacefactor directlyharmed age farmer herder ///
         pastvoted hhsize female i.village_,           ///
         treat(directlyharmed) benchmark(female)
```

要画 t 值等值线图：

```stata
sensemakr peacefactor directlyharmed age farmer herder ///
         pastvoted hhsize female i.village_,           ///
         treat(directlyharmed) benchmark(female) tcontourplot
```

要画 extreme plot：

```stata
sensemakr peacefactor directlyharmed age farmer herder ///
         pastvoted hhsize female i.village_,           ///
         treat(directlyharmed) benchmark(female) extremeplot
```

---

## 11. 反思：敏感性分析应该用来做什么

敏感性分析最容易被误用为一种“必须通过的稳健性检验”，类似显著性检验被滥用的方式。

更合理的理解是：

* 它不是告诉你 “有没有遗漏变量”
* 而是把争论点转化为一个明确的条件句：

> “如果要推翻我的结论，你必须相信存在一个混杂因子，它在处理方程和结果方程中同时解释至少 $x%$ 的残差方差。”

这种量化表达的价值在于：

* 让讨论更透明
* 让不同研究者可以在同一尺度下比较“担忧程度”
* 避免陷入纯口水的 “可能有遗漏变量” 争论

---

## 12. 参考文献

* Cameron, A. C., & Miller, D. L. (2015). A Practitioner’s Guide to Cluster-Robust Inference. *Journal of Human Resources*, 50(2), 317–372. [Link](https://doi.org/10.3368/jhr.50.2.317), [PDF](http://sci-hub.ren/10.3368/jhr.50.2.317), [Google](https://scholar.google.com/scholar?q=A Practitioner’s Guide to Cluster-Robust Inference).

* Chernozhukov, V., Cinelli, C., Newey, W., Sharma, A., & Syrgkanis, V. (2022). Long Story Short: Omitted Variable Bias in Causal Machine Learning. *NBER Working Paper* 30302. [Link](https://doi.org/10.3386/w30302), [PDF](https://www.nber.org/system/files/working_papers/w30302/w30302.pdf), [Google](https://scholar.google.com/scholar?q=Long Story Short: Omitted Variable Bias in Causal Machine Learning).

* Cinelli, C., Ferwerda, J., & Hazlett, C. (2024). Sensemakr: Sensitivity Analysis Tools for OLS in R and Stata. *Observational Studies*, 10(2), 93–127. [Link](https://doi.org/10.1353/obs.2024.a946583), [PDF](https://muse.jhu.edu/pub/56/article/946583/pdf), [Google](https://scholar.google.com/scholar?q=Sensemakr:+Sensitivity+Analysis+Tools+for+OLS+in+R+and+Stata).

* Cinelli, C., & Hazlett, C. (2020). Making sense of sensitivity: Extending omitted variable bias. *Journal of the Royal Statistical Society: Series B (Statistical Methodology)*, 82(1), 39–67. [Link](https://doi.org/10.1111/rssb.12348), [PDF](http://sci-hub.ren/10.1111/rssb.12348), [Google](https://scholar.google.com/scholar?q=Making+sense+of+sensitivity:+Extending+omitted+variable+bias).

* Hazlett, C. (2019). Angry or weary? How violence impacts attitudes toward peace among Darfurian refugees. *Journal of Conflict Resolution*, 63(2), 460–489. [Link](https://doi.org/10.1177/0022002719879217), [PDF](http://sci-hub.ren/10.1177/0022002719879217), [Google](https://scholar.google.com/scholar?q=Angry+or+weary?+How+violence+impacts+attitudes+toward+peace+among+Darfurian+refugees).

* Cinelli, C., Ferwerda, J., & Hazlett, C. (2025). sensemakr: Sensitivity Analysis Tools for Regression Models (R package). [Link](https://CRAN.R-project.org/package=sensemakr), [PDF](https://cran.r-project.org/web/packages/sensemakr/sensemakr.pdf), [Google](https://scholar.google.com/scholar?q=sensemakr+R+package+sensitivity+analysis).

---

## 13. 相关推文

* 邹恬华, 2021, [遗漏变量？敏感性分析！新命令sensemakr](https://www.lianxh.cn/details/621.html).
* 陈卓然, 2022, [因果推断：混杂因素敏感性分析理论(上)](https://www.lianxh.cn/details/1031.html).
* 陈卓然, 2022, [因果推断：混杂因素敏感性分析实操(下)-tesensitivity](https://www.lianxh.cn/details/1032.html).
* 李适源, 2022, [Stata：敏感性分析-rcr](https://www.lianxh.cn/details/877.html).
* 王烨文, 2024, [Stata：平行趋势敏感性检验-honestdid](https://www.lianxh.cn/details/1467.html).

