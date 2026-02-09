# U型曲线及其调节效应检验

---

>**作者：** 李彦杰（中山大学）
>**邮箱：** <liyj368@mail2.sysu.edu.cn>

> 编者按：本文内容主要参考下文，特此致谢！
> Sourse：李敬子,何祚宇,高重阳. 需求风险分散与中国出口企业创新[J]. 经济研究,2024,59(11): 106-121. [Link]([http://……](https://kns.cnki.net/kcms2/article/abstract?v=ZkvsKdWJ3ST_3oZyTMZHILSYgyKqscIaOzncJTirrRm7K5LG78Ze3BmrJbEtg2Q6wONZ5lqd5kfQ_E2jwxzV1_5q5aFbLVZ6z8UpL1wi9b98gpFpMj_2Plm_GJnfaNzu_i8lCjH82bqFOqzWS2CzAHGoehBCUEAFJzUnH-PdSvw=&uniplatform=NZKPT)).
---
## 导读

在实证研究中，U型（或倒U型）关系越来越常见，如“竞争与创新”、“库兹涅兹曲线”、“高管任期与企业绩效”等。但如何严谨地检验U型关系？又如何进一步检验调节变量对这一曲线形态的影响？本文以《需求风险分散与中国出口企业创新》（李敬子等，2024）为案例，系统讲解U型关系及其调节效应的检验框架与Stata实操。

---

## 一、为什么U型关系的检验需要"特殊对待"？

传统上，许多研究仅通过检验二次项系数β₂的显著性来判断U型关系（Y = β₀ + β₁X + β₂X²）。但这存在严重问题：

1. **无法排除伪非线性**：可能仅仅是单调凸/凹函数在样本区间内的局部表现
2. **转折点位置不确定**：转折点可能落在样本范围之外，导致"U型"无实际经济意义
3. **调节效应分析混乱**：调节变量究竟改变了曲线的"位置"还是"形状"？

Lind & Mehlum（2010）指出，检验U型关系需要**三个必要条件**，而Haans et al.（2016）进一步将调节效应明确区分为 **"转折点移动"** 和 **"曲率变化"** 两种机制。

---

## 二、U型关系检验的三步法（Lind & Mehlum, 2010）

以《需求风险分散与中国出口企业创新》为例，该文检验"需求风险分散指数"（riskdiver）与"企业创新"（innovation，用企业专利申请数衡量）的倒U型关系：

$$innovation_{ft} = \beta_0 + \beta_1 riskdiver_{ft} + \beta_2 riskdiver_{ft}^2 + \boldsymbol{\gamma} Controls_{ft} + \mu_f + \delta_{pt} + \varepsilon_{ft}$$


 **步骤1：二次项系数方向正确且显著**

```stata
* （如果尚未安装）安装非内置命令：
ssc install reghdfe  // 高维固定效应
ssc install ftools    // 辅助包
ssc install nlcom     // 非线性组合检验
ssc install utest     // U型检验
* 基准回归
reghdfe innovation riskdiver c.riskdiver#c.riskdiver, absorb(id_firm id_province_year) vce(cluster id_firm)
```
回归结果显示，在仅加入企业固定效应、省份—时间固定效应，不加入其他控制变量时，β₁ = 0.119（p<0.01）显著为正；β₂ = -0.128（p<0.01）显著为负，符合倒U型关系的初步判断。

**步骤2：曲线两端斜率显著**
计算解释变量在样本最小值（xₗ）和最大值（xₕ）处的斜率：
左端点斜率β₁ + 2β₂xₗ 必须显著为正，右端点斜率β₁ + 2β₂xₕ 必须显著为负。（否则可能出现Logit、Probit或者Poisson分布图像）
```stata
* 计算端点斜率
summarize riskdiver
local x_l = r(min)
local x_h = r(max)
* 左端点斜率（x取最小值）
lincom _b[riskdiver]+2*_b[c.riskdiver#c.riskdiver]*`x_l'  // 斜率为0.119显著为正
* 右端点斜率（x取最大值）
lincom _b[riskdiver]+2*_b[c.riskdiver#c.riskdiver]*`x_h'  // 斜率为-0.137显著为负
```
结果：两端斜率符号相反且均显著，排除单调凸函数可能。

**步骤3：转折点位于数据范围内**
计算转折点：x* = -β₁/(2β₂)，并计算其置信区间。这里推荐使用Lind & Mehlum开发的 utest 命令。作为U型/倒U型检验量身打造的 Stata 命令，它能一次性完成转折点计算、转折点是否在样本区间内的显著性检验、Fieller法置信区间估计，完全匹配文献的规范检验逻辑，比手动计算更精准、更贴合实证要求。同时也可完成对上述步骤2的计算。
```stata
* 使用Utest命令（Lind & Mehlum开发的Stata包）
gen sqrriskdiver = c.riskdiver#c.riskdiver //utest命令不支持c.riskdiver#c.riskdiver形式，需重新定义
reghdfe innovation riskdiver sqrriskdiver, absorb(id_firm id_province_year) vce(cluster id_firm)
utest riskdiver sqrriskdiver
```
结果：转折点x* = 0.465，95%置信区间[0.465, 0.466]完全落在[xₗ, xₕ]数据范围内，加入控制变量等进一步检验后，可确认倒U型关系成立。

---

## 三、调节效应检验：转折点移动 vs. 曲率变化
已有文献在进行U型关系的机制检验时，大都通过XM交互项回归系数的显著性，来判断M是否能够影响X与Y之间的U型或倒U型关系，如果交互项正向显著，则认为M正向调节了X与Y之间的U型或倒U型关系。

然而，这种正向影响是表明顶点向右移动，还是曲线变得更为陡峭？或是两种情况都有？Haans et al.（2016）认为要想清楚识别出M影响U型关系的类型，往往更适合采用调节效应模型来验证和分析。Folta & O’Brien（2004）、Henderson et al.（2006）对这两种不同调节类型及其背后的潜在机制作了说明：

假设M是影响X与Y之间U型或倒U型关系的机制变量，事实上，M可以通过两种不同的方式来调节这种非线性关系：
**①转折点移动**：它可以向左或向右移动曲线的顶点；
**②曲率变化**：它可以改变曲线的曲率，使曲线变得更为平坦或者陡峭。

为了验证M是否是调节X与Y之间U型关系的机制变量及其背后的具体调节类型，可以通过回归如下方程来进行验证：

$$Y=β_0+β_1X+β_2X^2+β_3XM+β_4X^2M+β_5M+αControls+ξ$$

### 检验1：转折点是否移动（检验β₁β₄ - β₂β₃是否显著）

**1. 顶点位置的推导**
调节效应模型一阶条件为：

$$\frac{\partial Y}{\partial X} = \beta_1 + 2\beta_2 X + \beta_3 M + 2\beta_4 XM = 0$$

整理可得顶点 \(X*\) 的表达式：

$$X^* = \frac{-\beta_1 - \beta_3 M}{2\beta_2 + 2\beta_4 M} $$


**2. 顶点移动方向的推导**
机制变量 M 对顶点 X* 的影响，可通过对 X* 关于 M 求导得到：

$$\frac{\partial X^*}{\partial M} = \frac{-\beta_3(2\beta_2 + 2\beta_4 M) - 2\beta_4(-\beta_1 - \beta_3 M)}{(2\beta_2 + 2\beta_4 M)^2}$$

化简后：

$$\frac{\partial X^*}{\partial M} = \frac{\beta_1\beta_4 - \beta_2\beta_3}{2(\beta_2 + \beta_4 M)^2} $$

由于分母 $$2(\beta_2 + \beta_4 M)^2$$ 恒为正，因此 $$\frac{\partial X^*}{\partial M}$$ 的符号完全由分子 $$\beta_1\beta_4 - \beta_2\beta_3$$ 决定：
- 若 $$\beta_1\beta_4 - \beta_2\beta_3 > 0$$，则 $$\frac{\partial X^*}{\partial M} > 0$$，顶点随M增大而**右移**（最优X水平提高）。
- 若 $$\beta_1\beta_4 - \beta_2\beta_3 < 0$$，则 $$\frac{\partial X^*}{\partial M} < 0$$，顶点随M增大而**左移**（最优X水平降低）。


因此问题简化，即检验统计量 β₁β₄ - β₂β₃ 是否显著不为0：
```stata

//创建变量
global Controls "c1 c2 c3 c4" 
gen sqrX = X^2
gen X_M = X*M
gen sqrX_M = sqrX*M
//回归
reghdfe Y X sqrX X_M sqrX_M M $Controls, absorb(id_firm id_province_year) vce(cluster id_firm)
//使用nlcom进行非线性组合检验
nlcom _b[X]*_b[sqrX_M] - _b[sqrX]*_b[X_M]
```
结果：若p<0.05，说明转折点在95%置信水平上显著移动。
方向判断：
若β₁β₄ - β₂β₃ > 0：转折点右移（最优风险分散水平提高）
若β₁β₄ - β₂β₃ < 0：转折点左移（最优风险分散水平降低）
论文发现：原始创新积累使转折点右移（β₁β₄ - β₂β₃ = 0.0017, p<0.05），说明企业原始创新积累的提高会提升市场需求风险对企业创新的正向效应（左半段上升区间变得更大了，X与Y正向关系的样本增多）。与之相反，国家产业政策支持却导致顶点在10%显著性水平上左移。

### 检验2：曲率是否变化（检验$\beta_4$是否显著）

曲率是否变动的检验思路是：首先设置两个不同的 M 值，来计算两个不同的顶点值；然后通过将两个顶点值左移或者右移相同的距离 d ，来比较移动后的两个点的斜率是否一致。如果不一致，则表明曲率发生了变化。进一步地，可以通过曲率的比较来判断是变得更为平坦还是陡峭。

假设 $M_2 > M_1$，当 $M = M_1$ 时，顶点值为：
$$
X_1^* = \frac{-\beta_1 - \beta_3 M_1}{2\beta_2 + 2\beta_4 M_1}
$$

当 $M = M_2$ 时，顶点值为：

$$
X_2^* = \frac{-\beta_1 - \beta_3 M_2}{2\beta_2 + 2\beta_4 M_2}
$$

假如存在倒U型关系，现在分别将两个顶点向左移动相同的距离 d（d > 0），并设 $S_1$ 为 $X_1^* - d$ 处的斜率，$S_2$ 为 $X_2^* - d$ 处的斜率。那么，可以计算 $S_1$ 和 $S_2$ 如下：

$$
\begin{aligned}
S_1 &= \beta_1 + 2\beta_2(X_1^* - d) + \beta_3 M_1 + 2\beta_4(X_1^* - d)M_1, \\
S_2 &= \beta_1 + 2\beta_2(X_2^* - d) + \beta_3 M_2 + 2\beta_4(X_2^* - d)M_2,
\end{aligned}
$$

如果 $S_2 > S_1$，那么倒U型图像会变得更为陡峭；如果 $S_2 < S_1$，那么倒U型图像会变得更为平坦。同样地，将两个顶点均向右移动相同的距离 d（d > 0），会得到相同的结论。 $S_2 - S_1$ 可得：

$$
S_2 - S_1 = 2\beta_2(X_2^* - X_1^*) + \beta_3(M_2 - M_1) + 2\beta_4\left[(X_2^* - d)M_2 - (X_1^* - d)M_1\right]
$$

将 $X_1^*$ 和 $X_2^*$ 代入 $S_1$ 和 $S_2$，化简可得：

$$
S_2 - S_1 = -2\beta_4(M_2 - M_1)d
$$

由于 $M_2 > M_1$，d > 0，因此曲率是否变动可以通过直接判断二次项与机制变量的交互项系数 $\beta_4$ 是否显著来进行验证，并且曲率变得更为平坦或陡峭可以通过 $\beta_4$ 的符号方向来判断。
因此，问题简化为直接检验交互项系数β₄：
```stata
* 检验系数β₄（也可直接使用回归表格结果）：
test _b[sqrX_M] = 0
```
- 在**倒U型关系**中：

如果 $$beta_4 > 0$$，那么当 M 较小时， X 对 Y 的非线性影响较小，随着 M 的增大，X 对 Y 的非线性影响变得更强，即曲率变得更加陡峭。
如果 (\beta_4 < 0)，那么\beta_4 < 0\)，那么当 \(M\) 较小时，\(X\) 对 \(Y\) 的非线性影响较小，随着 \(M\) 的增大，\(X\) 对 \(Y\) 的非线性影响变得更强，即曲率变得更加陡峭；
  - 如果 \(\beta_4 > 0\)，那么当 \(M\) 较小时，\(X\) 对 \(Y\) 的非线性影响较大，随着 \(M\) 的增大，\(X\) 对 \(Y\) 的非线性影响变得更弱，即曲率变得更加平坦。

- 在**正U型关系**中：
 
  - 如果 \(\beta_4 > 0\)，那么当 \(M\) 较小时，\(X\) 对 \(Y\) 的非线性影响较小，随着 \(M\) 的增大，\(X\) 对 \(Y\) 的非线性影响变得更强，即曲率变得更加陡峭。
  -  如果 \(\beta_4 < 0\)，那么当 \(M\) 较小时，\(X\) 对 \(Y\) 的非线性影响较大，随着 \(M\) 的增大，\(X\) 对 \(Y\) 的非线性影响变得更弱，即曲率会变得更加平坦；
  


论文关键发现：
流动性资产更强↑ → β₄ = 0.0841（p<0.05）→ 曲线变平坦 → 风险厌恶企业更能抵御需求波动
 
产业政策支持↑ → β₄ = -0.0335（p<0.05）→ 曲线变陡峭 → 政策依赖企业抗风险能力更脆弱

### 检验3：是否存在更高次项的非线性关系

如果调节变量存在内生性，那么调节效应检验可能会体现或隐含了解释变量和被解释变量之间更高次的非线性关系，而并非原有的异质性关系。因此，作者设计如下的计量思路进行检验：

假设调节变量 $$M_{ft}$$ 与需求风险分散指数 $$riskdiver_{ft}$$ 之间存在内生性，即 $$M_{ft}$$ 中包含了 $$riskdiver_{ft}$$ 的影响，那么可以将调节变量 $$M_{ft}$$ 表示为如下形式：

$$
M_{ft} = \alpha_1 + \alpha_2 riskdiver_{ft} 
$$

将其代入调节效应检验式，进一步整理、简化可得：

$$
innovation_{ft} = \beta_0' + \beta_1' riskdiver_{ft} + \beta_2' riskdiver_{ft}^2 + \beta_3' riskdiver_{ft}^3 + \alpha Controls + \mu_f + \delta_{pt} + \varepsilon_{ft}
$$

如果$$\beta_3'$$ 显著不为0，那么需求风险分散指数与出口企业创新之间可能存在三次甚至更高层次的非线性关系，那么 $$\beta_4$$ 的显著性可能带来的并不是由于机制变量变化所导致的曲率变动，而是由于机制变量内生性所隐含的需求风险分散指数与出口企业创新之间更高次的非线性关系。
因此，作者对$$\beta_3'$$ 进行检验，显示当在倒U型曲线中放入更高次的三次项时，三次项的回归系数并不显著，说明在需求风险分散指数与出口企业创新之间并不存在更高次的非线性关系，使得机制检验更能体现对倒U型曲线顶点和曲率的异质性影响，并在一定程度上缓解了机制变量内生性对调节效应分析的影响。

---

## 四、结论与启示
《需求风险分散与中国出口企业创新》为我们展示了较严谨的U型调节效应检验范式：
1. 理论先行：明确调节变量通过何种机制（移动/变形）影响主效应
2. 统计严谨：三步法确认U型存在 + 双重检验确认调节作用
3. 内生控制：高次项检验缓解机制变量内生性
4. 经济意义：不仅报告统计显著性，更解释转折点移动的实践含义
    
论文指出，当前中国整体需求风险分散指数处于倒U型曲线左侧（<0.465），且呈现波动降低趋势，表明逆全球化浪潮对中国企业创新的负面影响。因此，要积极发挥使得倒U型曲线变平坦的影响机制，如完善金融市场建设以提升企业流动性、建立更广泛的GVC间接渠道、畅通国内大循环等，努力"化风险为创新动力"。

---

## 参考文献
1. 李敬子,何祚宇,高重阳. 需求风险分散与中国出口企业创新[J]. 经济研究,2024,59(11): 106-121.
2. Aghion, P., N. Bloom, R. Blundell, R. Griffith, and P. Howitt, 2005, “Competition and innovation: An inverted-U relationship”, The Quarterly Journal of Economics, Vol.120（2）, 701—728.
3. Folta, T. B., and J. P. O’Brien, 2004, “Entry in the Presence of Dueling Options”, Strategic Management Journal, Vol.25（2）, 121—138.
4. Haans, R. F. J., C. Pieters, and Z. L. He, 2016, “Thinking About U: Theorizing and Testing U and Inversed U-Shaped Relationship in Strategy Research”, Strategic Management Journal, Vol.37, 1177—1195.
5. Henderson, A. D., D. Miller, and D.C. Hambrick, 2006, “How Quickly Do CEOs Become Obsolete? Industry Dynamism, CEO Tenure, and Company Performance”, Strategic Management Journal, Vol.27（5）, 447—460.
6. Lind, J. T., and H. Mehlum, 2010, “With or Without U? The Appropriate Test for a U-shaped Relationship”, Oxford Bulletin of Economics and Statistics, Vol.72（1）, 109—118.
