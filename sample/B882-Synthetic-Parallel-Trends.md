# Synthetic Parallel Trends：讲义式导读


> [ChatGPT 原始对话](https://chatgpt.com/share/691b438f-4408-8005-af00-e9ff7b8bd559)




## 1. 研究背景与核心问题

Liu（2025）这篇 job market paper 试图回答一个目前 DID / SC 文献里越来越突出的、但一直缺乏系统处理的问题：**不同识别策略背后的“权重选择”到底有多脆弱？有没有一种更弱、更统一的识别框架，既能囊括 DID、SC、SDID，又能在它们假设失败时仍然提供有效推断？**([arXiv][1])

在标准的面板数据政策评估中：

* DID 在“平行趋势假设”下，把处理组与对照组平均趋势之间的差异当成因果效应；
* SC 方法（Abadie, Diamond, Hainmueller 2010）用一组**凸权重**拼出“合成对照组”，要求处理前路径高度拟合，进而外推到处理后；([TandF Online][2])
* SDID（Arkhangelsky et al. 2021）则在 DID 与 SC 之间折中，利用双重权重构造“合成处理组”与“合成对照组”。([美国经济学会][3])

Arkhangelsky 等人已经指出，从估计形式看，这些估计量都可以写成“带权差分”的形式，只是权重完全不同。Liu 的切入点是：**把这些方法背后的“识别假设”统一写成对一个“真·人口权重向量”的假定**，然后问：

* 如果我们不事先假定那个具体的权重向量到底是哪一个，只要求它满足一些“弱得多”的条件，会得到怎样的识别集合？
* 如何在这样一个“部分识别”的框架下进行有效推断？

为此，文章提出了一个新的识别假设：**Synthetic Parallel Trends（SPT）**，并在此基础上构造了一个对“识别集合”的置信集。文章的主张可以概括为一句话：

> 不要把赌注压在某一个具体的 DID/SC 权重选择上，而是先退一步，承认一大类可能合理的权重，然后在这类权重下求出“所有可能的处理效应区间”，再做推断。

## 2. 基本设定与记号

设有一个处理单元和若干对照单元，观察到多个时期的面板或重复截面数据。记单位为

* 处理单元：记为单位 1；
* 对照单元：记为单位 $k=2,\dots,K$。

时间为 $t=1,\dots,T$，其中 $t\leq T_0$ 为处理前时期，$t>T_0$ 为处理后时期。记潜在结果为

$$Y_{kt}(0),;Y_{kt}(1)$$

分别表示在无处理和有处理状态下单位 $k$ 在时期 $t$ 的结果。实际观察到的结果为

$$Y_{kt}=Y_{kt}(0)+D_{kt}\bigl(Y_{kt}(1)-Y_{kt}(0)\bigr)$$

其中 $D_{kt}$ 是处理指示变量。本文关注**已处理单元在最后一期的平均处理效应**：

$$\tau=\mu_{1T}(1)-\mu_{1T}(0)$$

其中

$$\mu_{kt}(d)=E\bigl[Y_{kt}(d)\bigr]$$

是人口平均潜在结果。由于 $\mu_{1T}(1)$ 在数据中可观察，核心难点是识别反事实均值 $\mu_{1T}(0)$。

与传统 DID 不同，Liu 把焦点放在**“趋势”**上，而不是水平。定义未处理潜在结果的时间趋势：

$$\Delta\mu_{kt}(0)=\mu_{kt}(0)-\mu_{k,t-1}(0),\quad t=2,\dots,T$$

特别地，记处理单元的处理前趋势向量为

$$b_{\text{pre}}=\bigl(\Delta\mu_{1,2}(0),\dots,\Delta\mu_{1,T_0}(0)\bigr)^{\top}$$

把各对照单元的处理前趋势组成一个矩阵

$$A_{\text{pre}}=\bigl[\Delta\mu_{k t}(0)\bigr]_{t=2,\dots,T_0;,k=2,\dots,K}$$

类似地，处理后时期各对照单元在时期 $T$ 的趋势向量记为

$$a_{\text{post}}=\bigl(\Delta\mu_{2T}(0),\dots,\Delta\mu_{KT}(0)\bigr)^{\top}$$

**目标**是利用处理前的 $A_{\text{pre}}$ 与 $b_{\text{pre}}$，在一个合理的识别假设下，推断出 $\Delta\mu_{1T}(0)$，从而恢复 $\mu_{1T}(0)$，进而得到 $\tau$。

## 3. Synthetic Parallel Trends 假设

### 3.1 仿射权重下的 SPT

文章首先考虑一个“仿射权重”版本的 SPT。设

$$\omega=\bigl(\omega_2,\dots,\omega_K\bigr)^{\top}$$

是一个权重向量，满足

$$\sum_{k=2}^K\omega_k=1$$

但不对符号和大小做任何限制（可以为负或大于 1）。**Affine Synthetic Parallel Trends** 假设可以写成：

> 存在某个仿射权重向量 $\omega$，使得对所有时间点 $t=2,\dots,T$，处理单元的未处理趋势等于对照单元未处理趋势的加权平均：
>
> $$\Delta\mu_{1t}(0)=\sum_{k=2}^K\omega_k\Delta\mu_{kt}(0)$$

在处理前时期，这个假设意味着

$$b_{\text{pre}}=A_{\text{pre}}\omega$$

在处理后时期，则有

$$\Delta\mu_{1T}(0)=a_{\text{post}}^{\top}\omega$$

因此，在只使用处理前信息的前提下，**可行的权重集合**为

$$\Omega_{\text{aff}}=\bigl{\omega\in\mathbb{R}^{K-1}:1^{\top}\omega=1,;A_{\text{pre}}\omega=b_{\text{pre}}\bigr}$$

在这个集合中，每一个 $\omega$ 都对应一个可能的反事实趋势

$$\Delta\mu_{1T}(0)=a_{\text{post}}^{\top}\omega$$

于是，在纯粹的仿射 SPT 下，**反事实趋势的“识别集合”**为

$$M_{ID}=\bigl{a_{\text{post}}^{\top}\omega:\omega\in\Omega_{\text{aff}}\bigr}$$

Liu 证明了一个颇具“all-or-nothing”味道的结论：在只假定仿射 SPT 的情况下，$M_{ID}$ 要么是整个实数轴，要么是单点。后者发生在一个很强的“低秩”情形：大致上说，如果把所有单位（处理组 + 对照组）在所有时期的未处理趋势堆叠成一个矩阵，这个矩阵的秩非常低（例如满足一个严格的线性因子结构），那么 $\Omega_{\text{aff}}$ 只有一个元素，$M_{ID}$ 也就是单点；否则，$M_{ID}$ 就会非常宽甚至无界。([arXiv][1])

这恰好覆盖了经典的 TWFE 模型：如果潜在结果满足一个线性因子结构，那么在不加噪声的理论世界里，SPT 本身足以把反事实均值识别成单点；但只要对这个结构做一点微小扰动，这个“好事”就立刻消失，识别集合立刻“炸开”。这说明**仅靠仿射 SPT 的识别非常脆弱**。

### 3.2 加上凸性约束：凸权重 SPT

上述“要么单点、要么全空间”的结果太极端，这促使作者引入一个在因果推断文献中非常常见、但在这里被赋予“识别角色”的附加假设：**权重的凸性**。也就是说，除了 $\sum_{k}\omega_k=1$ 之外，还要求

$$\omega_k\geq 0,\quad k=2,\dots,K$$

于是，权重集合变为标准单纯形上的一个线性子集：

$$\Omega_{\text{conv}}=\bigl{\omega\in\mathbb{R}^{K-1}:\omega_k\geq 0,;1^{\top}\omega=1,;A_{\text{pre}}\omega=b_{\text{pre}}\bigr}$$

这时，SPT 可以表述为：

> 存在某个时间不变的凸权重向量 $\omega\in\Omega_{\text{conv}}$，使得处理单元的未处理趋势在所有时期都落在对照单元趋势的凸包内。([arXiv][1])

那么反事实趋势的识别集合变为

$$M_{ID}^+=\bigl{a_{\text{post}}^{\top}\omega:\omega\in\Omega_{\text{conv}}\bigr}$$

由于 $\omega$ 是凸权重，如果对照单元的处理后趋势是有界的，那么 $M_{ID}^+$ 一定是一个**非平凡的有界区间**，大致落在

$$\bigl[\min_k\Delta\mu_{kT}(0),;\max_k\Delta\mu_{kT}(0)\bigr]$$

之内。这一点与 Manski（1989）在选择模型里引入的“有界支持”假设有些相似：**凸性把反事实趋势限制在一个“可接受的范围”里，从而提供了识别力量，而不仅仅是“解释便利性”**。([arXiv][1])

这就是本文最核心的识别思想：

* 仿射 SPT 给出“结构性”的时间一致性约束；
* 凸性给出“跨单位”的有界性约束；
* 两者一起，把反事实趋势锁定在一个由线性规划刻画的区间里。

## 4. 与 DID、SC、SDID 的统一视角

在凸权重 SPT 框架下，Liu 展示了一个非常漂亮的“统一图景”：**DID、SC、SDID 其实都是在这个统一的权重集合中选取一个特定的凸权重向量，然后假定“真相”刚好等于这个选择。**

### 4.1 DID 与平行趋势

在最经典的两组两期 DID 设置里，“平行趋势假设”可以写为：

$$\Delta\mu_{1t}(0)-\Delta\mu_{0t}(0)\text{ 在 }t\text{ 上常数}$$

在多期多对照单元的情形下，可以把“控制组”细分为多个子单元，并考虑它们在总体中的人口份额。Liu 指出，标准多期 DID 在平行趋势下，等价于存在一个**特殊的凸权重**

$$\omega^{\text{DID}}_k=\text{单位 }k\text{ 在所有对照单位中的人口份额}$$

使得对所有处理前时期 $t$ 都有

$$\Delta\mu_{1t}(0)=\sum_{k=2}^K\omega_k^{\text{DID}}\Delta\mu_{kt}(0)$$

这恰好是 SPT 的一个特例。此时，识别集合 $M_{ID}^+$ 退化为单点，而这个单点正是 DID 的目标参数。换言之：

> DID 所做的，是在一开始就“强行”把权重固定为人口份额向量 $\omega^{\text{DID}}$，并相信在处理前后这个向量始终是正确的。

凸权重 SPT 则说：我们不预设 $\omega^{\text{DID}}$ 就是“真相”，而是允许所有能在处理前拟合趋势的凸权重存在，并用这些权重给出一组可能的处理效应。

### 4.2 合成控制（SC）与 SDID

对于 Abadie, Diamond, Hainmueller（2010）提出的 SC 方法，权重是通过匹配处理前水平（和可能的协变量）得到的：

$$\omega^{\text{SC}}=\arg\min_{\omega\geq 0,;1^{\top}\omega=1}\sum_{t\leq T_0}\bigl(\mu_{1t}(0)-\sum_{k=2}^K\omega_k\mu_{kt}(0)\bigr)^2$$([TandF Online][2])

在一个线性因子模型假设下，可以证明，当处理前时期足够多时，这个 SC 权重的期望会趋近于某个“真·凸权重” $\omega^{\star}$，而这个 $\omega^{\star}$ 满足 SPT。于是，SC 的识别可以理解为：

> 在一个特定的 DGP（因子模型）下，存在一个“真·凸权重” $\omega^{\star}$；SC 估计量试图恢复这个权重，并假定反事实趋势由 $a_{\text{post}}^{\top}\omega^{\star}$ 给出。

类似地，SDID（Arkhangelsky et al. 2021）通过引入**单位权重 + 时间权重**，在平衡处理前结果的同时提高鲁棒性。Liu 指出，在其因子模型假设下，SDID 的极限权重也可视为一种平衡“潜在因子负载”的凸权重，从而也是 SPT 权重集合中的一个元素。([美国经济学会][3])

因此，从 SPT 的角度看：

* DID：预设权重是人口份额向量；
* SC：在因子模型下选择一个能拟合处理前路径的凸权重；
* SDID：在因子模型下选择另外一种“平衡因子”的凸权重。

**这三者的共同点**是：都在 SPT 允许的权重集合中选了一个“点”，并把这个点当作真相。**不同点**是：
各自对 DGP 作出了不同的结构假设，从而让那个点成为“唯一合理的选择”。

### 4.3 与平行趋势鲁棒性文献的关系

文章还把 SPT 放在更广泛的“放松平行趋势”文献中加以对比。包括：

* Manski & Pepper（2018）提出的 bounded-variation 假设：限制处理后趋势偏离处理前趋势的幅度不超过某个上界；([JSTOR][4])
* Rambachan & Roth（2023）的“更可信的平行趋势”框架：把“平行趋势违背”视为可被预处理趋势上界约束的扰动，进而构造稳健的 DID 置信区间；([OUP Academic][5])
* Ban & Kédagni（2022）的“Robust DiD models”：用 pre-period 的选择偏差的凸包来界定 post-period 偏差；([arXiv][6])
* Ye et al.（2024）利用两个对照组负相关的策略，用两个对照趋势的最小值与最大值夹住处理组反事实趋势。([TandF Online][7])

Liu 的贡献在于：这些方法大多沿着“时间维度”放松平行趋势（比较 pre vs post 的差异），而 SPT 强调的是“跨单位维度”的结构：**存在一个时间不变的凸权重，使处理单元永远处在对照单元趋势的凸包内**。因此，SPT 提供了一种与上述方法**不嵌套、互补**的放松方式，理论上可以与它们叠加，得到更窄的识别区间。([arXiv][1])

## 5. 识别集合上的推断方法

识别集合 $M_{ID}^+$ 通过一组线性约束定义，本质上是一个**线性规划（LP）问题的像集**：给定 $A_{\text{pre}}$、$b_{\text{pre}}$ 和 $a_{\text{post}}$，找到所有 $\omega$ 满足约束，然后计算 $a_{\text{post}}^{\top}\omega$ 的最小值与最大值。

在实际应用中，我们只能观察到样本均值和样本趋势，需要把**样本信息**代入这些线性约束中，于是就变成了一个**“带估计系数的线性规划”**问题。Liu 的推断策略大致如下：([arXiv][1])

1. 把“存在权重 $\omega$ 使约束成立”重写成一组**矩条件等式**：例如，

   * 处理前：
     $$A_{\text{pre}}\omega-b_{\text{pre}}=0$$
   * 处理后：候选的处理效应 $\tau$ 满足
     $$\mu_{1T}(1)-\tau-\mu_{1T}(0)=0$$
     并用 $\mu_{1T}(0)=\mu_{1,T-1}(0)+\Delta\mu_{1T}(0)$ 和 $a_{\text{post}}^{\top}\omega$ 链接起来。

2. 用微观数据估计这些矩条件中的“人口均值”，得到 $\hat{A}*{\text{pre}}$、$\hat{b}*{\text{pre}}$、$\hat{a}_{\text{post}}$ 等，它们在 $\sqrt{n}$ 速率下收敛。

3. 对于每一个候选的处理效应值 $\tau$，考虑一个**最小化约束违背程度的二次规划问题**：

   $$Q_n(\tau)=\min_{\omega\in\Delta_{K-1}}\bigl|\hat{A}*{\text{pre}}\omega-\hat{b}*{\text{pre}}\bigr|^2+\text{（涉及 }\tau\text{ 的约束项）}$$

   其中 $\Delta_{K-1}$ 是标准单纯形（凸权重约束）。如果存在某个 $\omega$ 使得样本约束几乎完全满足，那么 $Q_n(\tau)$ 会非常小；否则就会显著偏离 0。

4. 真正的识别集合 $M_{ID}^+$ 可以刻画为所有满足 $Q(\tau)=0$ 的 $\tau$ 的集合。由于 $Q(\cdot)$ 是一个对估计量的**方向可微函数**（min 运算带来的非光滑性），作者借用了 Fang & Santos（2019）关于“方向可微映射推断”的一般理论，建立了 $Q_n(\tau)$ 的极限分布。([IDEAS/RePEc][8])

5. 在实践中，作者使用一种改进的 bootstrap 方法来逼近 $Q_n(\tau)$ 的分布，进而对检验统计量进行校准，构造一个满足

   $$P\bigl(M_{ID}^+\subseteq CS_{1-\alpha}\bigr)\approx 1-\alpha$$

   的置信集 $CS_{1-\alpha}$。这个置信集是对**整个识别集合**的置信集，而不是对某个点估计的置信区间。

从应用者角度理解这一套工具，可以这样想：

* 传统 DID/SC 给你的是一个**点估计 + 置信区间**；
* SPT 给的是：在一个较弱、较稳健的假设下，**所有可能的处理效应区间**，再加上一个对这个“区间集合”本身的置信集。

这意味着，如果你的实证背景让你**不敢确信某一种具体权重选择是对的**，可以退而求其次，问一句：在所有能拟合处理前趋势的权重下，处理效应大概能被夹在什么区间内？

如果这个区间依然非常窄且符号稳定，那么你的结论相对稳健；如果区间跨过 0，甚至非常宽，那就说明仅凭现有信息和 SPT 假设，很难对处理效应方向做出可信判断。

## 6. 模拟与实证：我们能从结果中学到什么

### 6.1 模拟设计

Liu 在一系列模拟中，把 SPT 与以下几类方法进行比较：([arXiv][1])

* 传统 DID（平行趋势）；
* SC 与其变体；
* SDID；
* 以及一些基于平行趋势放松的鲁棒估计方法。

模拟的设计刻意涵盖几类场景：

1. **理想情形**：数据生成过程符合 DID 或 SC 的“教科书情形”，例如低秩因子结构 + 干净的平行趋势。
2. **轻微违背**：平行趋势被“轻微扰动”，或因子结构只有近似成立。
3. **严重违背**：处理单元的趋势明显不可能由任何“统一权重”在所有时期拟合，或者存在强烈的动态选择行为。

结果显示：

* 在理想情形下，SPT 的置信集长度与传统方法相近，偶尔略宽，但覆盖率接近标称水平；
* 当平行趋势或 SC 所依赖的结构出现违背时，传统方法的置信区间往往**严重反复率不足**（严重欠覆盖），而 SPT 的置信集依然能维持接近标称的覆盖率，只是长度变大。

这基本符合“保守但稳健”的直觉：**弱识别假设带来的代价是区间变宽，收益是覆盖率更可靠。**

### 6.2 实证例子：重访 BDM（2004）与 Arkhangelsky et al.（2021）

在实证部分，作者使用 CPS 的 MORG 数据（1979–2018 年），以美国 50 个州中女性周薪为核心变量，重访 Bertrand, Duflo, Mullainathan（2004）以及 Arkhangelsky et al.（2021）曾使用过的一个 placebo 设计。([arXiv][1])

简要地说，这个 placebo 设计在数据中“虚构”一个政策，构造一些并不存在的“ treated vs control” 州组合，以检验 DID/SC 估计在“本应为 0 的情形下”会有怎样的表现。Liu 在这些数据上比较了：

* 传统 DID 与 TWFE；
* SC 与 SDID；
* 以及基于 SPT 的识别集合和置信集。

实证发现一方面确认了以往文献指出的问题：**在 placebo 情形下，DID 与某些 SC 变体仍然可能给出看似显著的非零效应**；另一方面，SPT 的置信集在这些设定下往往包含 0 且较宽，反映出“在弱假设下我们无法排除 0 效应”这一更谨慎的结论。

对应用研究者而言，这个例子提供了一个很实际的信号：当你在复杂面板数据、存在潜在动态选择或异质趋势的环境中使用 DID/SC 时，**最好至少做一次 SPT 式的“鲁棒性检查”**，看一看在更弱的 SPT 假设下，结论是否依然成立。

## 7. 对应用研究者的启示

从教学和应用角度，可以把这篇文章总结成几条操作性建议：

1. **把“权重视角”内化到 DID/SC 的理解中。**
   不要只停留在“平行趋势成立/不成立”的口头描述，而要从“我 implicitly 假定了哪些单位以什么权重构成了对照组”的角度重新审视识别假设。

2. **把 DID、SC 看成 SPT 权重集合中的“点识别极端”。**
   当你使用 DID 或 SC 的时候，你其实在选择某一个具体的权重向量，并假设它在所有时期都正确。SPT 提醒你：一旦这个假设稍有偏差，点识别就会失效，但在 SPT 下依然可以得到一个合理的区间。

3. **把 SPT 当成“稳健性工具箱”的一员，而不是替代品。**
   在实践中，一个比较自然的工作流是：

   * 先用你最熟悉的 DID/SC/SDID 做 baseline 估计；
   * 再在同一数据上跑一次 SPT，看看弱假设下的识别集合和置信集有多宽；
   * 如果 SPT 区间依然符号稳定，可以对 baseline 结论更有信心；如果不是，就需要在论文中诚实呈现这点。

4. **结合时间方向的放松（如 Rambachan & Roth 2023）与单位方向的 SPT 放松。**
   Liu 的框架与“bounded violations of parallel trends” 类方法并不互斥。理论上，把两种放松同时施加在同一数据上，可以进一步缩窄识别集合，但这一点目前还属于研究前沿。

5. **注意 SPT 本身也需要可解释性的实证理由。**
   SPT 的关键前提是：存在一个时间不变的凸权重，使处理单元的趋势一直处于对照单元趋势的凸包内。这对 data structure 和 policy 机制有实质含义，并不是“白送”的。实证应用时，仍然要用制度背景和预处理路径图来支持这一前提。

总的来说，这篇文章把“权重视角 + 部分识别 + 方向可微推断”结合在一起，为 DID/SC 文献提供了一个新的“上层建筑”。对于经常教 DID/SC 或在顶刊论文中应用这些方法的研究者，SPT 是一个值得严肃对待的新工具。

## 8. 相关参考文献（带 DOI）

1. Liu, Y. (2025). Synthetic Parallel Trends. *Economics*, forthcoming. [Link](https://doi.org/10.48550/arxiv.2511.05870), [PDF](https://arxiv.org/pdf/2511.05870), [Google](https://scholar.google.com/scholar?q=Synthetic+Parallel+Trends). ([arXiv][9])

2. Abadie, A., Diamond, A., & Hainmueller, J. (2010). Synthetic Control Methods for Comparative Case Studies: Estimating the Effect of California’s Tobacco Control Program. *Journal of the American Statistical Association*, 105(490), 493–505. [Link](https://doi.org/10.1198/jasa.2009.ap08746), [PDF](http://sci-hub.ren/10.1198/jasa.2009.ap08746), [Google](https://scholar.google.com/scholar?q=Synthetic+Control+Methods+for+Comparative+Case+Studies). ([TandF Online][2])

3. Arkhangelsky, D., Athey, S., Hirshberg, D. A., Imbens, G. W., & Wager, S. (2021). Synthetic Difference-in-Differences. *American Economic Review*, 111(12), 4088–4118. [Link](https://doi.org/10.1257/aer.20190159), [PDF](https://www.aeaweb.org/articles?id=10.1257/aer.20190159), [Google](https://scholar.google.com/scholar?q=Synthetic+Difference-in-Differences). ([美国经济学会][3])

4. Manski, C. F., & Pepper, J. V. (2018). How Do Right-to-Carry Laws Affect Crime Rates? Coping with Ambiguity Using Bounded-Variation Assumptions. *Review of Economics and Statistics*, 100(2), 232–244. [Link](https://doi.org/10.1162/REST_a_00689), [PDF](http://sci-hub.ren/10.1162/REST_a_00689), [Google](https://scholar.google.com/scholar?q=How+Do+Right-to-Carry+Laws+Affect+Crime+Rates). ([JSTOR][4])

5. Rambachan, A., & Roth, J. (2023). A More Credible Approach to Parallel Trends. *Review of Economic Studies*, 90(5), 2555–2591. [Link](https://doi.org/10.1093/restud/rdad018), [PDF](https://www.jonathandroth.com/assets/files/HonestParallelTrends_Main.pdf), [Google](https://scholar.google.com/scholar?q=A+More+Credible+Approach+to+Parallel+Trends). ([OUP Academic][5])

6. Ban, K., & Kédagni, D. (2022). Robust Difference-in-Differences Models. *arXiv Working Paper*, arXiv:2211.06710. [Link](https://doi.org/10.48550/arxiv.2211.06710), [PDF](https://arxiv.org/pdf/2211.06710.pdf), [Google](https://scholar.google.com/scholar?q=Robust+Difference-in-differences+Models). ([arXiv][6])

7. Ye, T., Keele, L., Hasegawa, T., & Small, D. S. (2024). A Negative Correlation Strategy for Bracketing in Difference-in-Differences. *Journal of the American Statistical Association*, 119(545), 123–136（页码以正式出版为准）. [Link](https://doi.org/10.1080/01621459.2023.2252576), [PDF](https://www.tandfonline.com/doi/pdf/10.1080/01621459.2023.2252576), [Google](https://scholar.google.com/scholar?q=A+Negative+Correlation+Strategy+for+Bracketing+in+Difference-in-Differences). ([TandF Online][7])

8. Fang, Z., & Santos, A. (2019). Inference on Directionally Differentiable Functions. *Review of Economic Studies*, 86(1), 377–412. [Link](https://doi.org/10.1093/restud/rdy049), [PDF](http://sci-hub.ren/10.1093/restud/rdy049), [Google](https://scholar.google.com/scholar?q=Inference+on+Directionally+Differentiable+Functions). ([IDEAS/RePEc][8])

9. Molinari, F. (2020). Microeconometrics with Partial Identification. *Journal of Economic Perspectives*, 34(4), 152–180. [Link](https://doi.org/10.1257/jep.34.4.152), [PDF](http://sci-hub.ren/10.1257/jep.34.4.152), [Google](https://scholar.google.com/scholar?q=Microeconometrics+with+Partial+Identification). ([arXiv][1])

10. Tamer, E. (2010). Partial Identification in Econometrics. *Annual Review of Economics*, 2(1), 167–195. [Link](https://doi.org/10.1146/annurev.economics.050708.143401), [PDF](http://sci-hub.ren/10.1146/annurev.economics.050708.143401), [Google](https://scholar.google.com/scholar?q=Partial+Identification+in+Econometrics). ([arXiv][1])

如果你后续希望，我可以专门写一节“如何在 Stata/R 中实现 SPT 的线性规划与置信集构造”，从教学角度把这套方法落实到代码层面。

[1]: https://arxiv.org/pdf/2511.05870.pdf "Synthetic Parallel Trends"
[2]: https://www.tandfonline.com/doi/abs/10.1198/jasa.2009.ap08746?utm_source=chatgpt.com "Synthetic Control Methods for Comparative Case Studies"
[3]: https://www.aeaweb.org/articles?id=10.1257%2Faer.20190159 "Synthetic Difference-in-Differences - American Economic Association"
[4]: https://www.jstor.org/stable/26616193?utm_source=chatgpt.com "how do right-to-carry laws affect crime rates? coping with ..."
[5]: https://academic.oup.com/restud/article-abstract/90/5/2555/7039335?utm_source=chatgpt.com "More Credible Approach to Parallel Trends - Oxford Academic"
[6]: https://arxiv.org/abs/2211.06710?utm_source=chatgpt.com "[2211.06710] Robust Difference-in-differences Models"
[7]: https://www.tandfonline.com/doi/abs/10.1080/01621459.2023.2252576?utm_source=chatgpt.com "A Negative Correlation Strategy for Bracketing in ..."
[8]: https://ideas.repec.org/a/oup/restud/v86y2019i1p377-412..html "Inference on Directionally Differentiable Functions"
[9]: https://arxiv.org/abs/2511.05870?utm_source=chatgpt.com "Synthetic Parallel Trends"
