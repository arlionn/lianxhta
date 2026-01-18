
**Note-助教须知**：🍎 

- 下文是由 AI 生成的初稿，你可以在此基础上修改、润色，也可以大幅修改。
  - AI 无法插入图片、表格等。这些内容需要你从论文原文中截图插入，有些图片需要执行代码后插入。
  - AI 无法直接运行代码,因此初稿中的代码需要你自行运行，并将结果采用代码块的形式插入推文中。你可以借助 AI 补充一些针对代码和运行结果的解释文字。
  - AI 生成的初稿中，可能会遗漏一些重要的细节和内容，请务必对照论文原文进行补充和完善，确保推文内容的完整性和准确性。
  - AI 的表述难免存在「机器味」，因此，你需要对推文进行润色和修改，使其更符合人类读者的阅读习惯和理解方式。


----

# 遗漏变量敏感性分析：sensemakr 的理论与 R 实操

> **作者：** 连享会课程组    
> **邮箱：** <lianxh@lianxh.cn> 

> **Source 1**：Cinelli, C., Ferwerda, J., & Hazlett, C. (2024). Sensemakr: Sensitivity Analysis Tools for OLS in R and Stata. *Observational Studies*, 10(2), 93–127. [Link](https://doi.org/10.1353/obs.2024.a946583), [PDF](https://muse.jhu.edu/pub/56/article/946583/pdf), [Google](<https://scholar.google.com/scholar?q=Sensemakr:+Sensitivity+Analysis+Tools+for+OLS+in+R+and+Stata>).  

> **Source 2**：Cinelli, C., & Hazlett, C. (2020). Making sense of sensitivity: Extending omitted variable bias. *Journal of the Royal Statistical Society: Series B (Statistical Methodology)*, 82(1), 39–67. [Link](https://doi.org/10.1111/rssb.12348), [PDF](http://sci-hub.ren/10.1111/rssb.12348), [Google](<https://scholar.google.com/scholar?q=Making+sense+of+sensitivity:+Extending+omitted+variable+bias>).  

&emsp; 

- **Title**: sensemakr——OLS 回归的敏感性分析 R 包
- **Keywords**: 遗漏变量偏误, 敏感性分析, partial R2, robustness value, sensemakr, 因果推断

&emsp; 

>**声明**：本文写作过程中借助了 AI 工具，但内容经过了严格的审核和润色，确保准确传达了原论文的核心思想和方法。  
- [Claude 对话链接]()
- [ChatGPT 对话链接]()

---

## 1. 简介

在实证研究中，遗漏变量偏误 (omitted variable bias, OVB) 是最常见也最令人头疼的问题之一。即便我们控制了诸多协变量，读者和审稿人仍会质疑："是否还存在某个未观测的混杂因素，使得你的结论不再成立？" 面对这一诘问，传统的做法往往只能笼统回应"可能存在遗漏变量"，却难以给出更具体、更量化的讨论。

`sensemakr` 为这一困境提供了系统性的解决方案。该工具由 Cinelli 和 Hazlett (2020) 提出，并发展为同时支持 R 和 Stata 的软件包 (Cinelli, Ferwerda, and Hazlett, 2024)。其核心思想是将"遗漏变量有多强"这一抽象问题转化为可计算、可讨论的数值指标，从而让敏感性分析变得透明、可复现。

本文是连享会关于 `sensemakr` 系列推文的 R 语言版本。相较于此前的 Stata 版本 (邹恬华, 2021)，本文不仅更详细地介绍理论基础，还涵盖了两类进阶应用场景：

1. **Bootstrap 推断**：将抽样不确定性纳入敏感性分析框架
2. **交互项分析**：对异质性效应系数进行敏感性检验

通过 Darfur 难民调查数据的实例，我们将展示如何在 R 中运用 `sensemakr` 完成从基础到进阶的全流程分析。

**关键 R 包**：
- 敏感性分析：`sensemakr`
- 数据处理与可视化：`dplyr`, `ggplot2`

---

## 2. 理论基础：用 partial R² 量化遗漏变量强度

### 2.1 问题设定

考虑标准的线性回归设定：

$$
Y_i = \alpha + \tau D_i + X_i'\beta + \varepsilon_i
$$

其中 $Y_i$ 是结果变量，$D_i$ 是我们关心的处理变量或核心解释变量，$X_i$ 是控制变量向量，$\tau$ 是待估参数。如果存在未观测混杂 $Z_i$ 同时影响 $D_i$ 与 $Y_i$，真实模型应为：

$$
Y_i = \alpha + \tau D_i + X_i'\beta + \gamma Z_i + u_i
$$

由于 $Z_i$ 不可观测，我们只能估计不包含 $Z_i$ 的受限模型，导致 $\hat\tau$ 可能存在偏误。问题的关键不在于"是否存在遗漏变量"——这几乎总是存在——而在于：**这个遗漏变量需要多强，才能实质性改变我们的结论？**

### 2.2 两个残差化过程

`sensemakr` 的理论推导始于两个"残差化" (residualization) 步骤：

**第一步：处理残差**  
将 $D$ 对 $X$ 回归，得到残差：

$$
D = X\pi + r_D
$$

$r_D$ 代表在控制 $X$ 后，$D$ 剩余的波动部分。

**第二步：结果残差**  
将 $Y$ 对 $(D, X)$ 回归，得到残差：

$$
Y = \tau D + X\beta + r_Y
$$

$r_Y$ 代表在控制 $(D, X)$ 后，$Y$ 剩余的波动部分。

未观测混杂 $Z$ 之所以能产生偏误，本质上是因为它能同时解释 $r_D$ 与 $r_Y$。如果 $Z$ 只与其中一个相关，或相关性很弱，那么即便 $Z$ 被遗漏，其造成的偏误也有限。

### 2.3 两个 partial R²

为量化 $Z$ 的强度，`sensemakr` 引入两个偏 $R^2$ (partial $R^2$)：

**$Z$ 对处理的解释力**

$$
R^2_{D \sim Z \mid X} = \frac{\text{Var}(r_D) - \text{Var}(r_D \mid Z)}{\text{Var}(r_D)}
$$

它衡量在控制 $X$ 后，$Z$ 能解释 $D$ 剩余波动的比例。

**$Z$ 对结果的解释力**

$$
R^2_{Y \sim Z \mid D, X} = \frac{\text{Var}(r_Y) - \text{Var}(r_Y \mid Z)}{\text{Var}(r_Y)}
$$

它衡量在控制 $(D, X)$ 后，$Z$ 能解释 $Y$ 剩余波动的比例。

这两个指标具有清晰的直觉：
- 若 $R^2_{D \sim Z \mid X}$ 很小，说明 $Z$ 几乎无法影响"谁接受处理"
- 若 $R^2_{Y \sim Z \mid D, X}$ 很小，说明 $Z$ 几乎无法影响"在控制处理后的结果"
- 两者任一很小，OVB 就难以大到足以推翻结论

### 2.4 偏误界 (Bias Bound)

在给定 $(R^2_{D \sim Z \mid X}, R^2_{Y \sim Z \mid D, X})$ 时，Cinelli 和 Hazlett (2020) 证明了 $\tau$ 的偏误可写成封闭形式。最坏情况下的偏误界为：

$$
\left|\text{Bias}(\hat\tau)\right| \leq \text{SE}(\hat\tau) \cdot \sqrt{\frac{\text{df}}{1 - R^2_{D \sim Z \mid X}}} \cdot \sqrt{R^2_{Y \sim Z \mid D, X} \cdot R^2_{D \sim Z \mid X}}
$$

其中 $\text{SE}(\hat\tau)$ 是受限模型估计的标准误，$\text{df}$ 是自由度。这一公式将"遗漏变量有多强"的抽象讨论转化为具体的数值计算：只要给定假想混杂的强度，就能得出系数最多会被推移多少。

### 2.5 处理变量的 partial R² 与 t 统计量

实践中，我们很难直接判断"$R^2_{Y \sim Z \mid D, X} = 0.15$ 算强还是弱"。`sensemakr` 提供了一个更易解释的中间量——处理变量的 partial $R^2$：

$$
R^2_{Y \sim D \mid X} = \frac{t^2}{t^2 + \text{df}}
$$

这意味着从回归表的 t 值就能立即推断出 $D$ 在结果中解释了多少剩余方差，从而为评估遗漏变量强度提供参照基准。

---

## 3. Robustness Value：压缩为单一指标

### 3.1 RV 的定义

虽然理论上我们需要同时考虑两个 partial $R^2$，但在实际报告中，往往希望用一个数字概括结论的稳健性。`sensemakr` 输出的 **Robustness Value (RV)** 正是为此设计：

- **$RV_q$**：把点估计推到原值 $q$ 倍 (通常 $q=1$，即推到 0) 所需的最小"对称混杂强度"
- **$RV_{q,\alpha}$**：把估计推到不显著 (给定显著性水平 $\alpha$) 所需的最小"对称混杂强度"

"对称强度"指：

$$
R^2_{D \sim Z \mid X} = R^2_{Y \sim Z \mid D, X}
$$

### 3.2 如何解读 RV

以 $RV_{q=1}$ 为例：
- 数值越大，说明要完全推翻结论 (使估计变为 0)，需要极强的混杂
- $RV_{q=1,\alpha=0.05}$ 越大，说明要消除显著性，需要极强的混杂

RV 不是告诉你"有没有遗漏变量"，而是告诉你：**若要反驳当前结论，必须相信一个多强的遗漏变量存在。** 这将讨论从定性转向定量，从而使争论更加透明。

---

## 4. Benchmarking：用观测变量做参照

### 4.1 为什么需要 benchmark

抽象的 $R^2$ 数值往往难以直观理解。更有说服力的表述方式是将未观测混杂与某个已知的协变量进行比较。例如：
- "这个遗漏变量真的可能比 `female` 还强吗？"
- "它真的可能比整组村庄固定效应还强吗？"

这时可引入基准变量 $B$ (benchmark covariate)，假设：
- 在处理方程中，$Z$ 与 $D$ 的相关强度是 $B$ 的 $k_D$ 倍
- 在结果方程中，$Z$ 与 $Y$ 的相关强度是 $B$ 的 $k_Y$ 倍

`sensemakr` 会自动将这些倍数转换为对应的 partial $R^2$，进而得出调整后的估计。

### 4.2 更具可读性的表达

在论文写作中，benchmarking 能产生更易被接受的表述：
- "即使存在一个在两个方程中解释力均达到 `female` 2 倍的混杂，结论仍然成立"
- "要推翻结论，需要一个比整组村庄固定效应更强的混杂，这在研究背景下不太可信"

这种表达比单纯报告 RV 数值更具说服力，因为它将抽象的统计量锚定在读者熟悉的变量上。

---

## 5. 案例：Darfur 冲突与和平态度

### 5.1 研究背景

`darfur` 数据来自 Hazlett (2019) 对乍得东部 Darfur 难民的问卷调查，研究核心问题是：**直接遭遇暴力伤害是否会影响人们对和平的态度？**

常用的 OLS 设定为：

$$
Y_i = \alpha + \tau D_i + X_i'\beta + \mu_{v(i)} + \varepsilon_i
$$

- $Y_i$：`peacefactor`，和平态度指数
- $D_i$：`directlyharmed`，是否被直接伤害 (0/1)
- $X_i$：`female`、`age`、`pastvoted`、`farmer_dar`、`herder_dar`、`hhsize_darfur` 等控制变量
- $\mu_{v(i)}$：村庄固定效应 (`village`)

OLS 回归通常得到 $\hat\tau > 0$ 且显著，似乎意味着"暴力经历反而提升了对和平的支持"。

### 5.2 可能的混杂因素

这一结论容易遭受质疑：
- 暴力冲突可能集中于村庄中心，而中心居民可能有不同的政治倾向
- 袭击者可能偏好攻击财富较高或社会活跃度更高的人群
- 既有政治态度可能影响个人行为，从而改变其暴露在暴力风险下的概率

若这些因素不可观测，结论的稳健性就需要更系统的评估。

---

## 6. R 实操：基础敏感性分析

### 6.1 安装与载入数据

```r
# 首次使用时安装
# install.packages("sensemakr")

library(sensemakr)

# 加载示例数据
data("darfur")

# 简单查看
str(darfur)
summary(darfur)
```

### 6.2 基准 OLS 回归

```r
# 基准模型：和平态度 ~ 直接伤害 + 控制变量 + 村庄固定效应
darfur_model <- lm(
  peacefactor ~ directlyharmed + female + age + farmer_dar + 
                herder_dar + pastvoted + hhsize_darfur + village,
  data = darfur
)

summary(darfur_model)
```

这一步得到的就是常规论文中的 baseline 回归结果。

### 6.3 一行代码完成敏感性分析

```r
# 对 directlyharmed 系数进行敏感性分析
darfur_sens <- sensemakr(
  model = darfur_model,
  treatment = "directlyharmed",
  benchmark_covariates = "female",
  kd = 1:3,                    # 1 倍、2 倍、3 倍 benchmark 强度
  q = 1,
  alpha = 0.05,
  reduce = TRUE                # 假定混杂削弱效应 (保守假设)
)

# 输出摘要
summary(darfur_sens)
```

**输出解读**：
- `R2yd.x`：处理变量的 partial $R^2$，即 $R^2_{Y \sim D \mid X}$
- `RV_q`：使点估计变为 0 所需的最小对称混杂强度
- `RV_qa`：使结果变为不显著所需的最小对称混杂强度
- 表格列出在 `female` 1-3 倍强度假设下，调整后的估计值

### 6.4 可视化：等值线图

```r
# 系数敏感性等值线图
plot(darfur_sens)

# t 统计量敏感性等值线图
plot(darfur_sens, sensitivity.of = "t-value")

# 最坏情况图 (extreme plot)
plot(darfur_sens, type = "extreme")
```

**图形说明**：
- 横轴：$R^2_{D \sim Z \mid X}$ (混杂对处理的解释力)
- 纵轴：$R^2_{Y \sim Z \mid D, X}$ (混杂对结果的解释力)
- 图上每个点对应一个假想混杂强度，并映射到相应的调整后估计

【此处预留插入图片位置】  
**图 1**：系数敏感性等值线图  
说明：等值线表示在不同混杂强度下，调整后的估计值。红色三角标记代表 `female` benchmark。

【此处预留插入图片位置】  
**图 2**：t 统计量敏感性等值线图  
说明：展示在不同混杂强度下 t 值的变化，虚线表示临界值边界。

---

## 7. 进阶一：变量组 benchmark

### 7.1 为什么用变量组

单个 benchmark 有时解释力有限。更常见的稳健性表述是：
- "遗漏变量不太可能比地区固定效应还强"
- "遗漏变量不太可能比行业 + 年份固定效应还强"

`sensemakr` 支持将多个变量组合为 benchmark。

### 7.2 示例：村庄固定效应作为 benchmark

```r
# 提取回归中与 village 相关的虚拟变量名
village_terms <- grep(
  pattern = "^village",
  x = names(coef(darfur_model)),
  value = TRUE
)

# 用 village 整组作为 benchmark
group_sens <- sensemakr(
  model = darfur_model,
  treatment = "directlyharmed",
  benchmark_covariates = list(village_block = village_terms),
  kd = c(0.2, 0.5, 1.0),       # 0.2 倍、0.5 倍、1.0 倍
  q = 1,
  alpha = 0.05,
  reduce = TRUE
)

summary(group_sens)
plot(group_sens)
```

**解释**：这种写法适合形成更强的稳健性表述："需要一个在解释力上接近整组村庄固定效应的遗漏变量，才足以推翻结论"。

---

## 8. 进阶二：Bootstrap 敏感性分析

### 8.1 为什么需要 Bootstrap

标准的 `sensemakr` 基于 OLS 渐近标准误。若希望将抽样不确定性也纳入敏感性分析，可结合 bootstrap 方法。

### 8.2 算法流程

1. 设定 bootstrap 次数 $B$
2. 对 $b = 1, 2, \ldots, B$：
   - 从样本中有放回抽取 $n$ 个观测，形成 bootstrap 样本 $S_b$
   - 在 $S_b$ 上拟合 OLS 回归，得到 $\hat\tau^{(b)}$
   - 在相同 $k_D, k_Y$ 设定下运行 `sensemakr()`，记录调整后的 $\hat\tau^{(b)}_{\text{adj}}$
3. 用 $\{\hat\tau^{(b)}_{\text{adj}}\}_{b=1}^B$ 的分位数构造置信区间

### 8.3 R 代码：非聚类 bootstrap

```r
set.seed(20250118)

B <- 500                         # bootstrap 次数
n <- nrow(darfur)

tau_adj_boot <- numeric(B)

for (b in 1:B) {
  # 有放回抽样
  idx_b <- sample.int(n = n, size = n, replace = TRUE)
  dat_b <- darfur[idx_b, ]
  
  # 回归
  model_b <- lm(
    peacefactor ~ directlyharmed + female + age + farmer_dar + 
                  herder_dar + pastvoted + hhsize_darfur + village,
    data = dat_b
  )
  
  # 敏感性分析
  sens_b <- sensemakr(
    model = model_b,
    treatment = "directlyharmed",
    benchmark_covariates = "female",
    kd = 2,                      # 假想混杂为 female 2 倍
    q = 1,
    alpha = 0.05,
    reduce = TRUE
  )
  
  # 记录调整后的估计
  tau_adj_boot[b] <- sens_b$bounds[1, "adjusted_estimate"]
}

# Percentile bootstrap 置信区间
quantile(tau_adj_boot, probs = c(0.025, 0.5, 0.975))
```

**说明**：可将 `kd = 1, 2, 3` 分别运行，对比 CI 的移动幅度，评估结论在不同混杂强度下的稳健性。

### 8.4 聚类 bootstrap

当数据存在聚类结构 (如按村庄)，应采用聚类层面的 bootstrap：

```r
set.seed(20250118)

clusters <- unique(darfur$village)
G <- length(clusters)

B <- 500
tau_adj_boot_cl <- numeric(B)

for (b in 1:B) {
  # 聚类层面有放回抽样
  sampled_clusters <- sample(clusters, size = G, replace = TRUE)
  
  # 提取被抽中村庄的所有观测
  dat_b <- darfur[darfur$village %in% sampled_clusters, ]
  
  # 回归与敏感性分析
  model_b <- lm(
    peacefactor ~ directlyharmed + female + age + farmer_dar + 
                  herder_dar + pastvoted + hhsize_darfur + village,
    data = dat_b
  )
  
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

这可产生更"硬"的稳健性表述："在聚类 bootstrap 推断下，允许相当于 `female` 2 倍强度的遗漏变量，结论仍保持为正且显著。"

---

## 9. 进阶三：交互项的敏感性分析

### 9.1 异质性效应模型

实证研究中常需检验异质性效应：

$$
Y_i = \alpha + \tau_1 D_i + \tau_2 M_i + \tau_3 (D_i \times M_i) + X_i'\beta + \varepsilon_i
$$

其中 $M_i$ 是调节变量 (如 `female`)，$\tau_3$ 是交互项系数。读者常会质疑：**"异质性结果会不会更容易被遗漏变量推翻？"**

此时可直接对交互项系数进行 `sensemakr` 分析。

### 9.2 R 实操

```r
# 交互项模型
darfur_int <- lm(
  peacefactor ~ directlyharmed * female + age + farmer_dar + 
                herder_dar + pastvoted + hhsize_darfur + village,
  data = darfur
)

summary(darfur_int)

# 对交互项系数做敏感性分析
int_sens <- sensemakr(
  model = darfur_int,
  treatment = "directlyharmed:female",    # 交互项系数名称
  benchmark_covariates = "female",
  kd = 1:3,
  q = 1,
  alpha = 0.05,
  reduce = TRUE
)

summary(int_sens)
plot(int_sens, sensitivity.of = "t-value")
```

**说明**：交互项在 R 中的名称是 `directlyharmed:female`。这类分析直接回答："需要多强的遗漏变量，才能让交互项结果变得不显著？"

### 9.3 使用变量组 benchmark

若希望 benchmark 更贴近交互项语境，可将 `female` 与交互项一起作为变量组：

```r
int_sens2 <- sensemakr(
  model = darfur_int,
  treatment = "directlyharmed:female",
  benchmark_covariates = list(
    female_block = c("female", "directlyharmed:female")
  ),
  kd = 1:3,
  q = 1,
  alpha = 0.05,
  reduce = TRUE
)

summary(int_sens2)
plot(int_sens2)
```

---

## 10. Stata 对照：同一思想的不同实现

虽然本文聚焦 R 实现，但许多读者需要在 Stata 中完成类似分析。Stata 版 `sensemakr` 的最小语法为：

```stata
sensemakr depvar covars, treat(varname) benchmark(varname)
```

以 Darfur 数据为例：

```stata
net get sensemakr.pkg
use darfur.dta, clear

sensemakr peacefactor directlyharmed age farmer_dar herder_dar ///
          pastvoted hhsize_darfur female i.village, ///
          treat(directlyharmed) benchmark(female)
```

画 t 值等值线图：

```stata
sensemakr peacefactor directlyharmed age farmer_dar herder_dar ///
          pastvoted hhsize_darfur female i.village, ///
          treat(directlyharmed) benchmark(female) tcontourplot
```

画 extreme plot：

```stata
sensemakr peacefactor directlyharmed age farmer_dar herder_dar ///
          pastvoted hhsize_darfur female i.village, ///
          treat(directlyharmed) benchmark(female) extremeplot
```

详细的 Stata 用法可参见邹恬华 (2021) 的推文。

---

## 11. 敏感性分析的正确理解

敏感性分析最易被误用为一种"必须通过的稳健性检验"，类似显著性检验的滥用。更合理的理解是：

- 它**不是**告诉你"有没有遗漏变量"
- 而是将争论转化为**明确的条件句**：

> "如果要推翻我的结论，你必须相信存在一个混杂因子，它在处理方程和结果方程中同时解释至少 $x\%$ 的残差方差。"

这种量化表达的价值在于：
- 让讨论更透明
- 让不同研究者可在同一尺度下比较"担忧程度"
- 避免陷入纯粹口水的"可能有遗漏变量"争论

实证研究的目标不是彻底"消除"遗漏变量偏误——这几乎不可能——而是通过系统的敏感性分析，让读者清楚地了解：**要推翻当前结论，需要多强的替代性解释。** 若所需混杂强度在研究语境下难以成立，结论便具备了更强的说服力。

---

## 12. 小结

本文系统介绍了 `sensemakr` 的理论基础与 R 语言实现，涵盖从基础到进阶的完整流程：

1. **理论框架**：用 partial $R^2$ 量化遗漏变量强度，通过偏误界计算调整后的估计
2. **Robustness Value**：将敏感性分析压缩为单一指标，便于报告与讨论
3. **Benchmarking**：用观测变量作参照，使表述更具可读性与说服力
4. **Bootstrap 推断**：将抽样不确定性纳入敏感性分析框架
5. **交互项分析**：对异质性效应系数进行敏感性检验

通过 Darfur 案例，我们展示了如何在实证研究中系统化地进行敏感性分析。这不仅是对审稿人质疑的回应，更是让因果推断更加透明、可信的重要工具。

---

## 参考文献

1. Cameron, A. C., & Miller, D. L. (2015). A Practitioner's Guide to Cluster-Robust Inference. *Journal of Human Resources*, 50(2), 317–372. [Link](https://doi.org/10.3368/jhr.50.2.317), [PDF](http://sci-hub.ren/10.3368/jhr.50.2.317), [Google](<https://scholar.google.com/scholar?q=A+Practitioner's+Guide+to+Cluster-Robust+Inference>).

2. Chernozhukov, V., Cinelli, C., Newey, W., Sharma, A., & Syrgkanis, V. (2022). Long Story Short: Omitted Variable Bias in Causal Machine Learning. *NBER Working Paper* 30302. [Link](https://doi.org/10.3386/w30302), [PDF](https://www.nber.org/system/files/working_papers/w30302/w30302.pdf), [Google](<https://scholar.google.com/scholar?q=Long+Story+Short:+Omitted+Variable+Bias+in+Causal+Machine+Learning>).

3. Cinelli, C., & Hazlett, C. (2020). Making sense of sensitivity: Extending omitted variable bias. *Journal of the Royal Statistical Society: Series B (Statistical Methodology)*, 82(1), 39–67. [Link](https://doi.org/10.1111/rssb.12348), [PDF](http://sci-hub.ren/10.1111/rssb.12348), [Google](<https://scholar.google.com/scholar?q=Making+sense+of+sensitivity:+Extending+omitted+variable+bias>).

4. Cinelli, C., Ferwerda, J., & Hazlett, C. (2024). Sensemakr: Sensitivity Analysis Tools for OLS in R and Stata. *Observational Studies*, 10(2), 93–127. [Link](https://doi.org/10.1353/obs.2024.a946583), [PDF](https://muse.jhu.edu/pub/56/article/946583/pdf), [Google](<https://scholar.google.com/scholar?q=Sensemakr:+Sensitivity+Analysis+Tools+for+OLS+in+R+and+Stata>).

5. Cinelli, C., Ferwerda, J., & Hazlett, C. (2025). sensemakr: Sensitivity Analysis Tools for Regression Models (R package). [Link](https://CRAN.R-project.org/package=sensemakr), [PDF](https://cran.r-project.org/web/packages/sensemakr/sensemakr.pdf), [Google](<https://scholar.google.com/scholar?q=sensemakr+R+package+sensitivity+analysis>).

6. Hazlett, C. (2019). Angry or weary? How violence impacts attitudes toward peace among Darfurian refugees. *Journal of Conflict Resolution*, 63(2), 460–489. [Link](https://doi.org/10.1177/0022002719879217), [PDF](http://sci-hub.ren/10.1177/0022002719879217), [Google](<https://scholar.google.com/scholar?q=Angry+or+weary?+How+violence+impacts+attitudes+toward+peace+among+Darfurian+refugees>).

---

## 相关推文

- 邹恬华, 2021, [遗漏变量？敏感性分析！新命令 sensemakr](https://www.lianxh.cn/details/621.html).
- 陈卓然, 2022, [因果推断：混杂因素敏感性分析理论 (上)](https://www.lianxh.cn/details/1031.html).
- 陈卓然, 2022, [因果推断：混杂因素敏感性分析实操 (下)-tesensitivity](https://www.lianxh.cn/details/1032.html).
- 李适源, 2022, [Stata：敏感性分析-rcr](https://www.lianxh.cn/details/877.html).
- 王烨文, 2024, [Stata：平行趋势敏感性检验-honestdid](https://www.lianxh.cn/details/1467.html).
