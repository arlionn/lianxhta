# Difference-in-Differences With a Misclassified Treatment：讲义式导读

（Negi & Negi, Journal of Applied Econometrics, 2025，DOI: 10.1002/jae.3116）


> [ChatGPT 原始对话](https://chatgpt.com/share/691b469c-d38c-8005-8b71-17f50c0bc168)



---

## 1. 研究问题与贡献概览

这篇论文讨论的是一个在实际 DID 研究中极其常见、却长期被忽视的问题：**处理变量是错误的**。更具体地说，研究者想要用 DID 估计平均处理效应（ATT），但用于区分“处理组/对照组”的二元变量本身存在**内生误分类（endogenous misclassification）**。例如：

* 政府转移支付项目中，家庭是否“领到补贴”在问卷里被严重低报或高报（误报）；
* 针对穷人的目标性项目里，真正符合条件的家庭没有领到，而不符合条件的家庭反而拿到了（错配）。

文章把这种情形重新放在 DID 框架下系统分析，提出了两个核心结论：([Wiley Online Library][1])

* 一方面，只要处理变量有内生误分类，即使**潜在真实处理状态**满足平行趋势假设，**直接用观测到的 D 做 DID 回归，估计量一般既不无偏，也不是简单的“衰减偏误”**；
* 另一方面，在一个适度参数化的设置下，只要研究者手里有一个影响“是否被正确记录/分配”的工具变量，就可以通过一个**部分可观测 probit（Partial Observability Probit, POP）+ 两步 DID 估计量**，恢复“潜在真实处理组”的 ATT。

从文献上看，这篇论文把**误分类文献（Aigner, 1973；Battistin & Sianesi, 2011；Nguimkeu et al., 2019）**和**现代 DID 文献（Abadie, 2005；Callaway & Sant’Anna, 2021 等）**整合在了一起，是“DID + 误分类”这一交叉主题的一篇代表作。([IDEAS/RePEc][2])

---

## 2. 基本 DID 模型与误分类的形式化

### 2.1 潜在结果与目标参数

设有两期数据：基期 t=0 和后期 t=1。对每个个体 i，潜在结果写作

$$Y_{it}(1),;Y_{it}(0),\quad t\in{0,1}$$

其中 1 表示接受“真实处理”，0 表示未接受真实处理。定义潜在真实处理状态

$$D_i^*\in{0,1}$$

DID 设计下我们关注的目标是“真正被处理个体”的平均处理效应

$$\tau=E\big[Y_{i1}(1)-Y_{i1}(0)\mid D_i^*=1\big]$$

在标准面板 DID 中，若我们观察到的就是 $D_i^*$，则可用

$$\Delta Y_i=Y_{i1}-Y_{i0}$$

配合平行趋势假设，构造 first-difference 或两期回归去识别 $\tau$。

### 2.2 观测处理变量的误分类

论文的关键是：研究者看到的不是 $D_i^*$，而是一个被误分类的二元变量 $D_i\in \{0,1\}$。

作者重点讨论 **一侧误分类（one-sided misclassification）** 情形：

* 在“误报（misreporting）”场景中，有人没有领到补贴但说自己领到了，或者反之；
* 在“错配（mistargeting）”场景中，政府设计的目标人群是 $D_i^*=1$，但实际发放的对象 $D_i$ 与之不吻合。

为刻画误分类，文中引入一个潜在“分类正确性”指示变量 $S_i$，并把 $D_i$ 写成 $D_i^*$ 和 $S_i$ 的函数（一般形式允许两类错误：把 0 当 1，把 1 当 0）。在一侧误分类下，假定只有一种方向的错误可能发生，从而大大简化了识别问题。

---

## 3. 为什么“用错的 D 做 DID”会出大问题？

### 3.1 非差别误报下的简化偏误

先看最简单的基准：如果处理误报是 **非差别（non-differential）** 的——也就是给定真实处理 $D_i^*$ 后，误报与结果误差独立，则作者推导出

$$E[\Delta Y_i\mid D_i=1]-E[\Delta Y_i\mid D_i=0]=\tau\cdot q_0$$

其中 $q_0=P(D_i^*=0\mid D_i=0)$ 表示“观测为未处理时，真实未处理”的概率。可以看到，这种情况下 DID 估计量只是“衰减”：系数被乘了一个 $q_0$，直觉上与 Aigner（1973）那类经典误测分析类似。

### 3.2 内生误分类：偏误不再是“衰减”，甚至会破坏平行趋势

真正棘手的是 **内生误分类（endogenous misclassification）**。在文中的设定里：

* 误分类概率不仅依赖可观测特征，还和结果误差相关；
* 直观说，“更穷、更需要补贴”的家庭更可能误报或错配，而这部分异质性正好与结果 $Y$ 的误差共变。

作者证明，在这种情形下：

1. DID 回归中直接把 $D_i$ 当作处理，估计量的偏误不再是一个简单的缩放因子，而变成复杂函数，**偏误方向一般无法签定**；
2. 更重要的是，即使潜在真实处理 $D_i^*$ 满足平行趋势，**观测到的 $D_i$ 却可以违反平行趋势**。
   换句话说，“误分类 + DID”不是只是“系数缩小一点”的问题，而是**DID 的核心识别假设被根本破坏**的问题。

这也是为什么 Negi & Negi 会强调：DID 文献过去大量工作是在假定处理变量观测准确的前提下展开的，而现实中的很多项目评估（政府补贴、社会保障、福利项目）恰恰存在严重的误报或错配。([Wiley Online Library][1])

---

## 4. 识别思路：部分可观测 probit（POP）+ 工具变量

### 4.1 潜在模型设定

为了解决误分类问题，作者采用一个相对温和的参数化框架。令

* $R_i=(1,X_i)$：进入真实处理方程的协变量（含常数项）；
* $Z_i$：影响“是否正确记录/分配”的协变量（工具变量），至少有一个变量只出现在 $Z_i$ 里，不出现在 $R_i$。

设

$$D_i^*=\mathbf{1}{R_i\gamma+U_i\ge0},\quad S_i=\mathbf{1}{Z_i\alpha+V_i\ge0}$$

其中 $(U_i,V_i)$ 服从二维正态分布，相关系数为 $\rho$。在一侧误分类形式下，观测到的 $D_i$ 是 $(D_i^*,S_i)$ 的函数。于是可得到

$$P(D_i=1\mid R_i,Z_i)=F_{U,V}(R_i\gamma,Z_i\alpha;\rho)$$

这个概率恰好对应 **部分可观测 probit（partial observability probit, POP）** 模型，是 Poirier (1980) 提出的经典框架。

**关键识别条件**包括：

* 至少有一个只出现在 $Z_i$ 而不出现在 $R_i$ 的变量（排除限制）；
* $(R_i,Z_i)$ 含有足够“丰富”的变异（比如连续协变量），使得 POP 模型局部可识别；
* 误差项 $(\Delta\xi_i,U_i,V_i)$ 与 $(R_i,Z_i)$ 条件下服从三元正态分布，允许 $\Delta\xi_i$ 与 $V_i$ 相关（内生误分类），但不允许 $\Delta\xi_i$ 与 $U_i$ 相关（这是 DID 设计中允许 $D_i^*$ 与时间不变不可观测相关、但不允许与**时间变化**不可观测相关的标准假设）。

直觉上，**$Z_i$ 提供了“仅影响误分类而不直接影响结果”的外生变异**，从而使得我们能在观测 $D_i$ 的前提下，反推出真实处理 $D_i^*$ 的概率。

---

## 5. 两步估计量：从 POP 到 DID 回归

作者提出一个非常实用、且在 Stata 中可以相对容易实现的两步估计程序。

### 5.1 第一步：估计 POP 模型，预测“真实处理概率”

在样本为面板时，数据结构为

$$(Y_{i0},Y_{i1},D_i,X_i,Z_i),\quad i=1,\dots,N$$

第一步，通过极大似然估计 POP 模型

$$
\max _{\gamma, \alpha, \rho} \sum_{i=1}^N\left\{D_i \log F_{U, V}\left(R_i \gamma, Z_i \alpha ; \rho\right)+\left(1-D_i\right) \log \left[1-F_{U, V}\left(R_i \gamma, Z_i \alpha ; \rho\right)\right]\right\}
$$

得到估计值 $(\hat\gamma,\hat\alpha,\hat\rho)$，进而计算**真实处理概率**

$$\hat D_i^*=\Phi(R_i\hat\gamma)$$

（直觉上，给定 $R_i$，$D_i^*$ 的发生只取决于 $R_i\gamma+U_i$）。

> 论文特别指出，这一步在 Stata 中可以用 `biprobit` 命令实现 POP 模型的估计。

### 5.2 第二步：用 $\hat D_i^*$ 构造加权 DID 回归

定义“中心化协变量”：

$$\hat X_i^*=X_i-\bar X_1^*,\quad\bar X_1^*=\frac{\sum_i\hat D_i^*X_i}{\sum_i\hat D_i^*}$$

并构造

$$\hat R_i^*=(1,\hat X_i^*)',\quad\hat W_i^*=\hat D_i^*\hat R_i^*$$

在面板 first-difference 情形下，考虑回归

$$\Delta Y_i=\hat R_i^{*'}\delta+\hat W_i^{*'}\theta+\text{误差}$$

其中 $\theta$ 中对应“处理×截距”那一项，就是修正误分类后的 DID 估计量。

在重复横截面情形下，则在 $Y_i$ 上回归

$$Y_i=\hat R_i^{*'}\delta+\hat W_i^{*'}\theta+T_i\hat R_i^{*'}\pi_1+T_i\hat W_i^{*'}\pi_2+\text{误差}$$

再从交互项系数中恢复 ATT。作者证明，两步估计量在常规正则条件下是一致且渐近正态的，并给出了较为复杂但可实现的渐近方差公式。

直觉上，两步法可以理解为：

1. 用 POP 模型“清洗”出**每个个体真实被处理的概率** $\hat D_i^*$；
2. 在 DID 回归里，用这些概率构造“潜在处理组的加权平均”，使回归尽可能接近用 $D_i^*$ 而非 $D_i$ 的理想情形。

---

## 6. 与既有文献的关系与边际贡献

从文献脉络看，这篇文章主要站在两个传统之上：([IDEAS/RePEc][2])

* 一是**误分类与误报的因果推断文献**：

  * Aigner (1973) 讨论二元解释变量误测下的回归偏误；
  * Battistin & Sianesi (2011) 在匹配/选择模型下分析处理误分类；
  * Nguimkeu, Denteh & Tchernis (2019) 允许**处理本身和误报同时内生**，给出了一般的偏误表达式与修正方法；
  * Tommasi & Zhang (2024) 研究“参与误报”情形下的项目收益识别与区间估计。
* 二是**现代 DID 文献**：Abadie (2005)、Callaway & Sant’Anna (2021) 等对 DID 在多期、多时点、多组异质性情形下的识别与估计问题进行了系统梳理，但基本都假定处理变量观测准确。

Negi & Negi 的边际贡献，可以概括为三点：

1. 在 **DID 框架** 下系统刻画了误分类（尤其是内生误分类）对 ATT 识别的影响，给出偏误的解析表达式，并强调其不再是简单的衰减；
2. 提出了一个结合 POP 的**两步 DID 估计量**，在允许相当程度异质性的 parametric 模型下，可以恢复“潜在真实处理组”的 ATT；
3. 给出了两个真实政策项目（养老金项目 + 粮食补贴项目）的实证应用，说明在存在严重误报/错配背景下，新方法与“朴素 DID”在估计结果与政策含义上的差异。

---

## 7. 实证应用：印度两项全国性项目

论文在实证部分分析了印度的两项全国性计划：

1. **Indira Gandhi National Old Age Pension Scheme (IGNOAPS)**：

   * 针对没有稳定收入和家庭支持的老年人提供现金养老金；
   * 利用 Indian Human Development Survey (IHDS) 数据，可以看到家庭在问卷中报告的“是否领到养老金”与官方统计口径之间存在巨大差异，说明补贴存在严重**低报/误报**；
   * 在这种场景下，$D_i^*$ 可理解为“真实领到养老金”，$D_i$ 是问卷报告，误分类主要来自“误报/低报”。

2. **Targeted Public Distribution System (TPDS)**：

   * 为“贫困线以下（BPL）”家庭提供低价粮食；
   * 通过 BPL 卡识别目标户，但实践中存在大量**错配（mistargeting）**：部分真正贫困户拿不到卡，而部分非贫困户持有 BPL 卡；
   * 此时 $D_i^*$ 是“根据政策设计应被覆盖”，$D_i$ 是“实际享受补贴”，误分类体现为“目标与实际执行之间的错配”。

在这两个应用中，作者利用政策规则与地方执行差异构造 $Z_i$，估计 POP 模型，得到 $\hat D_i^*$，再用两步 DID 估计政策影响。结果表明：

* 若忽略误分类直接用观测的 $D_i$ 做 DID，估计结果不仅可能偏小，还可能**方向错误**；
* 新方法修正后得到的 ATT，与直觉和外部证据更吻合，也更稳定。

---

## 8. 对实证研究者的启示

对做 DID 的研究者，这篇论文有几条非常直接的启示：

* 如果处理变量来自**问卷自报**（如福利项目参与、工会加入、培训参与等），或者来自**目标性项目的执行记录**（如贫困补贴、优惠贷款），就要严肃考虑误分类；
* 简单地把误分类看成“经典测量误差 + 衰减偏误”在 DID 里通常是错误的，特别是在误分类概率与结果误差相关时，**平行趋势假设可能在潜在处理 $D^*$ 下成立，在观测 $D$ 下却被破坏**；
* 如果能找到一个影响“是否被正确记录/分配”的外生变量 $Z$，例如：

  * 与项目执行质量相关的地区变量；
  * 与行政能力、监测机制相关但不直接影响结果的变量；
    就有可能利用类似 Negi & Negi 的 POP + 两步 DID 思路，部分修正误分类带来的偏误；
* 在 Stata 实现上，第一步 POP 可由 `biprobit` 完成，第二步就是一次加权/扩展的 DID 回归；协方差估计可以按论文附录的渐近公式编写程序或用 bootstrap 近似。

对于你正在做或准备做的 DID 研究，特别是涉及政府项目、补贴、社保计划的场景，这篇文章几乎可以作为“**是否要认真处理处理变量误分类问题**”的一个分水岭。

---

## 9. 参考文献

1. Aigner, D. J. (1973). Regression with a binary independent variable subject to errors of observation. *Journal of Econometrics*, 1(1), 49–59. [Link](https://doi.org/10.1016/0304-4076%2873%2990005-5), [PDF](http://sci-hub.ren/10.1016/0304-4076%2873%2990005-5), [Google](https://scholar.google.com/scholar?q=Regression+with+a+binary+independent+variable+subject+to+errors+of+observation).

2. Battistin, E., & Sianesi, B. (2011). Misclassified treatment status and treatment effects: An application to returns to education in the United Kingdom. *Review of Economics and Statistics*, 93(2), 495–509. [Link](https://doi.org/10.1162/REST_a_00175), [PDF](http://sci-hub.ren/10.1162/REST_a_00175), [Google](https://scholar.google.com/scholar?q=Misclassified+Treatment+Status+and+Treatment+Effects).

3. Negi, A., & Negi, D. S. (2025). Difference-in-differences with a misclassified treatment. *Journal of Applied Econometrics*, 40(4), 411–423. [Link](https://doi.org/10.1002/jae.3116), [PDF](https://arxiv.org/pdf/2208.02412), [Google](https://scholar.google.com/scholar?q=Difference-in-Differences+with+a+Misclassified+Treatment).

4. Nguimkeu, P., Denteh, A., & Tchernis, R. (2019). On the estimation of treatment effects with endogenous misreporting. *Journal of Econometrics*, 208(2), 487–506. [Link](https://doi.org/10.1016/j.jeconom.2018.10.005), [PDF](http://sci-hub.ren/10.1016/j.jeconom.2018.10.005), [Google](https://scholar.google.com/scholar?q=On+the+estimation+of+treatment+effects+with+endogenous+misreporting).

5. Tommasi, D., & Zhang, L. (2024). Identifying program benefits when participation is misreported. *Journal of Applied Econometrics*, 39(6), 1123–1148. [Link](https://doi.org/10.1002/jae.3079), [PDF](https://docs.iza.org/dp15427.pdf), [Google](https://scholar.google.com/scholar?q=Identifying+program+benefits+when+participation+is+misreported).

6. Callaway, B., & Sant’Anna, P. H. C. (2021). Difference-in-differences with multiple time periods. *Journal of Econometrics*, 225(2), 200–230. [Link](https://doi.org/10.1016/j.jeconom.2020.12.001), [PDF](https://file-lianxh.oss-cn-shenzhen.aliyuncs.com/Refs/2025-08-Yang/Callaway_2021_Difference-in-Differences_with_multiple_time_periods.pdf), [Google](https://scholar.google.com/scholar?q=Difference-in-Differences+with+multiple+time+periods).

如果你接下来希望，我可以专门写一节“如何在 Stata 中实现 Negi & Negi 两步估计量”，给出示意代码和与常规 DID 的对比回归方程。

[1]: https://onlinelibrary.wiley.com/doi/full/10.1002/jae.3116?utm_source=chatgpt.com "Difference‐in‐Differences With a Misclassified Treatment"
[2]: https://ideas.repec.org/a/wly/japmet/v40y2025i4p411-423.html?utm_source=chatgpt.com "Difference‐in‐Differences With a Misclassified Treatment"
