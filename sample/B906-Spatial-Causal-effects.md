# B906：空间处理效应的因果推断：识别、对照组选择与推断

> **Source**: Butts, K. (2025). *Causal Inference with Spatial Treatments*. Blog. ([Kyle Butts][1])
> **说明**：本文在 Kyle Butts 的原文基础上做了中文翻译与扩展，补充了空间干预(spatial treatments)在因果推断中的关键假设、常见陷阱与一组可复用的实证写作框架。

---

 {推文标题}

> **作者：** xx (xxx)    
> **邮箱：** <xxx@xxx.com> 

> **Source**：本文参考了如下资料，特此致谢！  
> <插入引文信息，APA 格式，可以参考往期推文中的表述>

&emsp; 

- **Title**: {推文标题}
- **Keywords**: k1, k2, …… 用半角逗号分开, {推文标题}已经有的无需写入

&emsp; 

>**声明**：本文写作过程中借助了 AI 工具，但内容经过了严格的审核和润色，确保准确传达了原论文的核心思想和方法。  
- [ChatGPT 对话链接](https://chatgpt.com/share/696c5bc2-85e4-8005-96bd-42b24cdc2fad)

---

**Note-助教须知**：&#x1F34E; 

- 下文是由 AI 生成的初稿，你可以在此基础上修改、润色，也可以大幅修改。
  - AI 无法插入图片、表格等。这些内容需要你从论文原文中截图插入，有些图片需要执行代码后插入。
  - AI 无法直接运行代码，因此初稿中的代码需要你自行运行，并将结果采用代码块的形式插入推文中。你可以借助 AI 补充一些针对代码和运行结果的解释文字。
  - AI 生成的初稿中，可能会遗漏一些重要的细节和内容，请务必对照论文原文进行补充和完善，确保推文内容的完整性和准确性。
  - AI 的表述难免存在「机器味」，因此，你需要对推文进行润色和修改，使其更符合人类读者的阅读习惯和理解方式。


---

## 1. 问题背景：当“处理”发生在空间中的某个点

很多政策与经济事件并不是“给人、给企业、给城市”一个标签式的处理，而是发生在空间中的一个点(或一组点)：

* 新建一栋大型公寓，影响周边租金与人口流动(近邻房源可能“被分流”) ([MIT Direct][2])
* 新企业(或新零售业态)在某条街道开业，改变本地消费、就业与街区活力 ([OUP Academic][3])
* 清理废弃地块、环境整治等“点状更新”，对犯罪率与健康结局可能存在强烈的空间衰减效应

这类问题的共同特征是：**结果变量发生在一组空间单位上(如住房、企业、住户)，处理发生在空间坐标上的某些点位上(如新楼盘地块、门店地址、污染源位置)**。因此，研究者往往最关心这样一种效应：

* “距离处理点 $d$ 的单位，其结果变量如何变化？”

以“新建公寓对周边租金”的例子来说明：我们有一份微观住房数据，每套房屋 $i$ 在时间 $t$ 的租金为 $Y_{it}$，而处理(新公寓建设)发生在某个地块坐标 $s^*$。我们关心的是政策前后租金变化 $\Delta Y$，以及这种变化是否随距离 $d(i,s^*)$ 呈现明显衰减。

核心难点并不在于是否能写出回归，而在于：**如何构造可信的对照组(control group)**。因为“空间处理”天然引入了一个麻烦：离得近的单位可能被影响(溢出、干扰、外部性)，离得远的单位又可能不具有可比性(结构差异、趋势差异)。

---

## 2. 空间处理为何难：SUTVA 的破裂与“暴露程度”的定义

在经典因果推断语言中，我们通常写潜在结果为 $Y_{it}(1)$ 与 $Y_{it}(0)$。但空间问题里，这种二元处理往往不够用，因为“是否受影响”往往依赖距离，甚至依赖周边多点处理的叠加。

一种更贴近空间语境的写法是把潜在结果写为：

$$
Y_{it}(\mathbf{z}) \quad,\quad \mathbf{z} \in {0,1}^{\mathcal{S}}
$$

其中 $\mathbf{z}$ 表示空间中所有位置是否发生处理的向量。此时个体 $i$ 的结果不仅取决于“自己是否被处理”，还取决于“邻近位置是否被处理”。这就是干扰(interference)或溢出(spillovers)问题，意味着经典 SUTVA 在空间场景中经常不成立。关于干扰情形下的识别与估计，已有系统的统计学刻画，例如 Aronow and Samii (2017) 提出的 exposure mapping 框架就强调：需要用一个映射 $g_i(\mathbf{z})$ 把复杂的空间处理配置压缩成“可解释的暴露水平”。 ([NHH][4])

在应用层面，我们经常做的其实就是选择一个暴露规则，例如：

* **内圈处理**：$D_i=1{d(i,s^*) \le r_0}$
* **外圈对照**：$D_i=0{r_0 < d(i,s^*) \le r_1}$

这看起来像一个很普通的“地理缓冲区(buffer)DID”，但它背后隐含的识别逻辑非常强：你必须相信外圈真的没有被影响(或影响很弱)，同时必须相信内圈与外圈有足够的可比性。

下面两节分别介绍 Kyle Butts (2023) 和 Michael Pollmann 的策略，它们本质上都是在回答同一个问题：

* **空间处理下，什么样的对照组才是可信的？**

---

## 3. Butts (2023)：内外圈(ring)策略与“处理效应曲线”

### 3.1 思路概述：同一街区冲击(common neighborhood shocks)

Butts (2023) 研究的是城市经济学中非常常见的做法：以处理点为中心画两个圈(inner ring 与 outer ring)：

* **内圈**：被认为受到处理影响(例如距离新楼盘 0–200 米)
* **外圈**：被认为不受影响或影响可忽略(例如 200–600 米)

直觉是：只要外圈足够窄，你就在比较“同一街区(neighborhood)”里非常近的住房单元。于是，街区层面的共同冲击(新增配套、人口流动、局部需求变化、周边企业开业等)在内外圈应当差不多，差异主要来自“离处理点更近”这一事实。

在识别假设上，这可以被表述为一种“局部平行趋势”：

> 在没有处理的反事实世界里，只要住房单元都处于 outer ring 范围内，它们的租金变化趋势应当相同。

Butts (2023) 对这一策略做了形式化，并指出它在空间微观数据(geocoded microdata)中的适用条件与潜在失败方式。 ([IDEAS/RePEc][5])

### 3.2 用公式写清楚：从单圈 DID 到多圈“曲线效应”

在最简版本中，你会写一个两组两期 DID：

$$
Y_{it} = \alpha_i + \gamma_t + \beta \cdot (D_i \times Post_t) + \varepsilon_{it},
$$

其中：

* $D_i=1$ 表示内圈单位
* $Post_t=1$ 表示处理发生后

但 Butts 更强调的是：空间效应往往呈衰减形态，所以比起“只有一个内圈”，更合理的是做多个距离带(bins)：

令 $b=1,\dots,B$ 表示距离带，比如
$[0,100), [100,200),\dots,[500,600)$ 米。

定义指示变量 $D_{ib}=1{d(i,s^*) \in \text{bin } b}$，则可以估计：

$$
Y_{it} = \alpha_i + \gamma_t + \sum_{b=1}^{B}\beta_b \cdot (D_{ib}\times Post_t) + \varepsilon_{it}.
$$

此时 ${\beta_b}$ 就是一条“处理效应曲线(treatment effect curve)”：它刻画了处理效应如何随距离变化。换句话说，你不再只问“有没有效应”，而是问：

* 效应在多远范围内显著？
* 效应是否在某个距离后接近 0？
* 衰减速度是否与理论机制一致(可达性、噪音、污染扩散、商业聚集等)？

### 3.3 经典示例：性犯罪者迁入对房价的极局部影响

Butts 在文中用 Linden and Rockoff (2008) 的研究作为例子：当性犯罪者迁入某条街区后，房价的负向反应高度局部化——同一街区的影响最大，距离稍远后迅速趋近于 0。 ([美国经济协会][6])

**Figure：treatment effect curve 的示意图(原文中的例子)**

<img src="https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20260111162415.png" width="700" alt="Example of treatment effect curve using Linden and Rockoff paper">

这种结果的解释力很强：它既支持了“空间效应衰减”的常识，也为“外圈足够近时可作为对照组”提供了经验支撑。

### 3.4 这套方法何时会失灵：城市结构与“可比性”的崩塌

Butts 也强调，内外圈策略并非总是可信。一个非常典型的反例来自多中心(polycentric)城市：

* 处理(新建公寓)往往发生在商业街或高密度区域
* 从处理点向外扩展时，空间结构可能迅速切换到低密度住宅街区

于是出现一种尴尬局面：

* 内圈样本主要是公寓、临街商业、密集交通
* 外圈样本主要是独栋住宅、安静街区、不同的需求结构

即便外圈离得不远，它们也未必与内圈有相同趋势。更具体地说，如果存在“公寓市场冲击”或“独栋住宅偏好冲击”，那么：

$$
E[\Delta Y_{it}(0)\mid D_i=1] \neq E[\Delta Y_{it}(0)\mid D_i=0],
$$

平行趋势假设在局部也会失败。

这也是中国学术界在使用“缓冲区 DID”时最容易被审稿人追问的点之一：**你的 treated buffer 与 control buffer 是否真的处于同一市场结构？** 如果 treated 区域主要覆盖主干道/核心商圈，而 control 区域进入了不同功能分区，那么估计量很可能混入了“空间结构差异的趋势”。

---

## 4. Pollmann (WP)：寻找“可能被处理但没被处理”的反事实地点

### 4.1 核心转向：不要比较“更近 vs 稍远”，而要比较“实现 vs 未实现的候选地点”

Michael Pollmann 的策略在逻辑上很不一样。他不再执着于“内圈 vs 外圈”，而是尝试寻找一组 **在处理发生前就很像处理地点、但最终没有发生处理的地点**，并将其作为反事实处理点(counterfactual treatment locations)。

在新建公寓的例子里，他希望找到这样的地块：

* 从规划、区位、配套、土地条件来看，它“本来很可能建公寓”
* 但由于随机性或偶然原因，最终没有建

于是，对照组不再是“距离更远的住房”，而是：

* **距离真实处理点很近的住房**
  对比
* **距离一个‘未实现处理点’也很近的住房**

这在直觉上非常契合“可比性”的要求：你比较的可能是同一种街区类型(例如都是商业街)，而不是“商业街 vs 住宅街”。

Pollmann 的工作论文为 *Causal Inference for Spatial Treatments*，并配套公开了实现示例。 ([Michael Pollmann][7])
(其个人主页也显示该文已进入 Econometrica 的有条件接收进程。 ([Michael Pollmann][8]))

### 4.2 匹配(matching)为什么不够：处理地点往往由“邻域特征”共同决定

如果只按地块自身特征匹配(比如分区、地块面积、所在普查区特征)，你很容易忽略一个关键事实：**开发决策通常不仅取决于地块本身，还取决于周边的空间结构**。

例如：

* 地块本身适合开发(underdeveloped)，但周边是否有消费型配套(餐饮、交通节点)也很关键
* 同样是低收入住户密集，处于“单栋楼内部的低收入”与处于“整条街都是低收入”可能含义不同
* 对于门店开业、污染源布局、公共设施选址，邻域条件往往直接决定了处理点出现的概率

因此，真正合理的匹配对象应当同时满足：

* **自身协变量相似**：$X_i \approx X_j$
* **邻域协变量分布相似**：$\mathcal{N}(X)_i \approx \mathcal{N}(X)_j$

问题随之变得“高维”：即使 $X$ 本身维度不高，“邻域函数”也会把信息维度急剧放大，例如：

* 最近邻的 $X$
* 街道平均 $X$
* 若干半径内的 $X$ 分布
* 多尺度(100m、500m、1000m)下的聚合统计

这会导致传统匹配或倾向得分模型很难直接胜任。

### 4.3 CNN 倾向得分地图：把空间当作“图像”来建模

Pollmann 的亮点在于：他把空间协变量当作类似图像(image)的输入，用 **卷积神经网络(CNN)** 去学习“一个位置在给定周边特征下发生处理的概率”，从而构造 **倾向得分地图(propensity score map)**。 ([Michael Pollmann][7])

直觉上，CNN 的优势在于：

* 它天然擅长处理“以某点为中心，观察周边局部结构”的信息
* 权重共享与局部感受野使其能够以较少参数表示复杂空间模式
* 适合捕捉“空间同质性、局部聚集、方向性差异”等规律

训练完成后，你得到的不是一个单值倾向得分，而是一张“热力图(heat map)”：哪里更可能出现处理点，哪里几乎不可能。

原文用“闪电强度分布图”做类比：如果倾向得分图像在某些区域(例如德州、路易斯安那、佛罗里达)更亮，那么从这些区域抽到“反事实处理点”的概率也更大。

**Figure：倾向得分热力图的直觉示意(原文引用 Washington Post 闪电强度图)**

<img src="https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20260111163203.png" width="700" alt="Washington Post Figure highlighting lightning intensity">

随后你就可以按照倾向得分，在高概率区域抽取(placebo)候选点，再从中挑选与真实处理点在局部结构上最相似的一组，作为最终的反事实处理点集合。

---

## 5. 两条路线如何选：识别假设、优劣势与实证写作要点

把 Butts 与 Pollmann 放在一起看，会发现它们在“对照组构造”上体现了两类完全不同的哲学：

### 5.1 Butts 路线：近邻对照(局部比较)

* **比较对象**：处理点附近更近的单位 vs 稍远但仍在同一街区的单位
* **关键依赖**：局部平行趋势 + 外圈不受影响(或影响弱)
* **输出优势**：可以得到一条清晰的“距离-效应曲线”

适用场景通常包括：

* 处理的影响半径不大，且城市形态相对连续
* 你能够合理说服读者：内外圈在处理前非常相似
* 你希望估计衰减形态(例如 100m 内强，300m 后消失)

主要风险则包括：

* 内外圈在功能分区、住房类型、产业结构上并不一致
* 外圈并非真正“未处理”，而是存在部分溢出
* 空间选择偏误：处理点本身是高增长区域的结果，而非原因

### 5.2 Pollmann 路线：候选地点对照(反事实处理点)

* **比较对象**：真实处理点附近单位 vs “未实现候选处理点”附近单位
* **关键依赖**：候选地点的可比性(处理发生与否在候选集合中近似随机)
* **输出优势**：更像设计型(design-based)思想，避免“更近 vs 稍远”的结构差异

适用场景通常包括：

* 城市呈多中心结构，内外圈策略容易陷入街区类型切换
* 处理点出现机制较复杂，依赖邻域结构
* 你有丰富的空间协变量(POI、卫星、街景、地块属性等)，能支撑 CNN 学习

主要风险包括：

* 候选地点集合如何定义，可能高度主观
* 倾向得分模型容易“学到”历史与制度背景，从而过拟合
* CNN 方案在经济学读者中仍属于新工具，需要更充分解释与稳健性检验

---

## 6. 对中文应用研究的补充：三类常见问题与一个“写作母版”

空间处理在中文经管实证研究中非常普遍，但在写作与识别阐述上，最容易出现三类问题。

### 6.1 “缓冲区 DID”写得像地理操作，而不是识别策略

很多论文写法是：

* 以某处理点为中心画 0–500m treated，500–1000m control
* 回归一个 $D_i\times Post_t$ 就结束

但真正关键的识别问题从未被认真讨论：

* 为什么 500–1000m 可以当作反事实？
* 它是否仍然被部分影响(交通改善、噪音扩散、配套溢出)？
* 内外圈单位是否同质？是否存在明显的住房类型切换？
* 是否存在“处理点本身处于上升期”的趋势差异？

建议的写作方式是把“距离带”从地理描述转为统计识别描述。一个可复用的母版表达是：

> 本文以处理点为中心构造多重距离带，并将距离带视为不同暴露水平(exposure levels)。在识别上，我们假设在不发生处理的反事实情形下，处于同一外圈半径 $r_1$ 内的单位受到相同的街区共同冲击(common neighborhood shocks)，从而不同距离带之间的趋势差异可以归因于处理影响的空间衰减。与此同时，我们通过处理前拟合检验、不同外圈半径的稳健性、以及空间安慰剂(placebo)设计验证该假设的合理性。

这一段如果写清楚，审稿人通常就知道你不是“画圈凑回归”。

### 6.2 空间溢出与干扰：不能只说“控制空间固定效应”

当存在干扰(interference)时，传统做法会“控制区域固定效应”或“聚类稳健标准误”，但这并不能解决识别层面的 SUTVA 破裂。

更可靠的做法是明确“暴露映射(exposure mapping)”或“部分干扰(partial interference)”的结构假设。例如可以写：

* 单位 $i$ 的结果仅受半径 $R$ 内的处理点影响(局部干扰)
* 或者影响随距离单调衰减并在 $R$ 外为 0(截断式衰减)

这些假设并不是形式主义，它们直接决定了你对照组是否被污染、估计量是否有偏。

### 6.3 识别之外还要讲“推断”：空间相关下的标准误与随机化检验

空间数据几乎必然存在相关性：同一街区的房租、同一商圈的客流、同一片区的犯罪率，都不是独立的。因此推断层面通常需要更加谨慎：

* 聚类层级是否应更粗(街道、普查区、网格)？
* 是否需要空间 HAC 或随机化推断(randomization inference)？
* placebo 位置抽样是否能构造设计型的置信区间？

Pollmann 的设计型视角在这里非常有价值：它提醒我们，不必把一切都当作“误差项空间相关”来修补，而是可以尝试构造一个更接近实验的比较设计，并据此推导方差表达。 ([arXiv][9])

---

## 7. 一个可运行的示例：用 Stata 演示多圈(rings)处理效应曲线

下面用模拟数据演示“多圈处理效应曲线”的估计流程。目的不是拟合某个真实论文，而是给出一套可以直接迁移到实际研究中的代码骨架。

### 7.1 DGP(数据生成过程)

设单位 $i$ 在二维空间坐标为 $(x_i,y_i)$，处理点为 $s^*=(0,0)$。距离为：

$$
d_i=\sqrt{x_i^2+y_i^2}.
$$

处理在时间 $t \ge T_0$ 生效，且处理效应随距离衰减：

$$
\tau(d_i)=
\begin{cases}
-0.10, & d_i\in[0,0.2)\
-0.06, & d_i\in[0.2,0.4)\
-0.03, & d_i\in[0.4,0.6)\
0, & d_i\ge 0.6
\end{cases}
$$

于是结果变量(例如对数租金)为：

$$
Y_{it}=\alpha_i+\gamma_t+\tau(d_i)\cdot Post_t+\varepsilon_{it}.
$$

### 7.2 Stata 代码(可直接复制运行)

```stata
*******************************************************
* 示例：多圈(rings)空间处理效应曲线
* 目的：演示 Butts(2023) 风格的处理效应随距离衰减估计
*******************************************************

clear all
set more off
set seed 20260118

*-------------------------------*
* 1) 生成空间单位与面板结构
*-------------------------------*
set obs 2000

* 空间坐标：假设单位均匀分布在 [-1,1]×[-1,1]
gen x = runiform()*2 - 1
gen y = runiform()*2 - 1

* 到处理点(0,0)的距离
gen dist = sqrt(x^2 + y^2)

* 生成 8 期面板数据
expand 8
bysort x y: gen t = _n

* 处理发生时间：t>=5
gen post = (t >= 5)

* 个体固定效应与时间固定效应
bysort x y: gen alpha = rnormal(0, 0.3) if _n==1
bysort x y: replace alpha = alpha[1]

gen gamma = .
replace gamma = -0.10 if t==1
replace gamma = -0.05 if t==2
replace gamma =  0.00 if t==3
replace gamma =  0.05 if t==4
replace gamma =  0.10 if t==5
replace gamma =  0.12 if t==6
replace gamma =  0.15 if t==7
replace gamma =  0.18 if t==8

*-------------------------------*
* 2) 构造距离带(rings/bins)
*    这里用 0.2 为步长：0-0.2, 0.2-0.4, 0.4-0.6, 0.6+
*-------------------------------*
gen bin = .
replace bin = 1 if dist < 0.2
replace bin = 2 if dist >= 0.2 & dist < 0.4
replace bin = 3 if dist >= 0.4 & dist < 0.6
replace bin = 4 if dist >= 0.6

label define binlbl 1 "[0,0.2)" ///
                   2 "[0.2,0.4)" ///
                   3 "[0.4,0.6)" ///
                   4 "[0.6,+)"
label values bin binlbl

*-------------------------------*
* 3) 设定“真实”处理效应(用于生成数据)
*-------------------------------*
gen tau = 0
replace tau = -0.10 if bin==1
replace tau = -0.06 if bin==2
replace tau = -0.03 if bin==3
replace tau =  0.00 if bin==4

* 误差项
gen eps = rnormal(0, 0.15)

* 结果变量：log rent
gen ylog = alpha + gamma + tau*post + eps

*-------------------------------*
* 4) 回归：多圈处理效应曲线
*    基准圈设为最远的 bin==4
*    估计：sum_b beta_b * 1{bin=b}*post
*-------------------------------*
* 若你装了 reghdfe，可用下面写法
capture which reghdfe
if _rc==0{
    reghdfe ylog i.bin##i.post, ///
        absorb(x y t) vce(robust)

    * 提示：这里 absorb(x y t) 只是演示写法
    * 实战中你应使用真实的个体ID与时间ID
}
else{
    * 没有 reghdfe 的情况下，用 xtreg 作为近似演示
    egen id = group(x y)
    xtset id t
    xtreg ylog i.bin##i.post i.t, fe vce(robust)
}

*-------------------------------*
* 5) 结果解读提示(文字说明)
*-------------------------------*
* 关注系数：1.bin#1.post, 2.bin#1.post, 3.bin#1.post
* 它们分别对应距离带 [0,0.2)、[0.2,0.4)、[0.4,0.6)
* 相对于基准圈 [0.6,+) 的 post 期差异
*******************************************************
```

这段代码体现了 Butts 思路的最核心部分：**将空间距离离散化为多个 rings，并估计 $\beta_b$ 随距离变化的序列**。在真实应用中，你会把：

* $x,y$ 换成真实的经纬度投影坐标
* $t$ 换成真实年月季度
* bin 边界换成行业经验或基于机制的半径选择
* 并将推断层级设置为更合理的空间聚类(例如街区或网格)

---

## 8. 结语：两句“结论式”建议

Butts 与 Pollmann 的两条路线，可以被压缩成两句非常实用的经验结论：

* 如果你相信**处理点附近在结构上是连续的**，并且外圈足够窄能共享同一街区冲击，那么 Butts 的 rings 思路是最直接、最透明、也最容易呈现空间衰减机制的方案。 ([IDEAS/RePEc][5])
* 如果你怀疑**“更近 vs 稍远”会混入街区类型切换**，或者处理点出现高度依赖邻域结构，那么 Pollmann 的“反事实处理点”策略更接近设计型比较，值得认真考虑，尤其是在你拥有丰富空间协变量、能够训练倾向得分地图时。 ([arXiv][9])

---

# 参考文献(补充与扩展)

* Aronow, P. M., & Samii, C. (2017). Estimating average causal effects under general interference. *The Annals of Applied Statistics*, 11(4), 1912–1947. [Link](https://doi.org/10.1214/16-AOAS1005), [PDF](http://sci-hub.ren/10.1214/16-AOAS1005), [Google](https://scholar.google.com/scholar?q=Estimating average causal effects under general interference).

* Asquith, B. J., Mast, E., & Reed, D. (2023). Local Effects of Large New Apartment Buildings in Low-Income Areas. *The Review of Economics and Statistics*, 105(2), 359–375. [Link](https://doi.org/10.1162/rest_a_01055), [PDF](http://sci-hub.ren/10.1162/rest_a_01055), [Google](https://scholar.google.com/scholar?q=Local Effects of Large New Apartment Buildings in Low-Income Areas).

* Butts, K. (2023). JUE Insight: Difference-in-differences with geocoded microdata. *Journal of Urban Economics*, 133, 103493. [Link](https://doi.org/10.1016/j.jue.2022.103493), [PDF](http://sci-hub.ren/10.1016/j.jue.2022.103493), [Google](https://scholar.google.com/scholar?q=Difference-in-differences with geocoded microdata).

* Linden, L., & Rockoff, J. E. (2008). Estimates of the Impact of Crime Risk on Property Values from Megan’s Laws. *American Economic Review*, 98(3), 1103–1127. [Link](https://doi.org/10.1257/aer.98.3.1103), [PDF](http://sci-hub.ren/10.1257/aer.98.3.1103), [Google](https://scholar.google.com/scholar?q=Estimates of the Impact of Crime Risk on Property Values from Megan’s Laws).

* Pollmann, M. (2020, revised 2023). Causal Inference for Spatial Treatments. *arXiv working paper*. [Link](https://arxiv.org/abs/2011.00373), [PDF](https://arxiv.org/pdf/2011.00373), [Google](https://scholar.google.com/scholar?q=Causal Inference for Spatial Treatments Pollmann).

* Pollmann, M. (2023). Causal Inference for Spatial Treatments (latest PDF). *Working paper*. [PDF](https://michaelpollmann.github.io/files/pollmann_spatial_treatments.pdf), [Google](https://scholar.google.com/scholar?q=Causal Inference for Spatial Treatments Michael Pollmann).

* Rosenthal, S. S., & Ross, A. (2024). How do new entries of discount variety stores affect local markets? *Journal of the European Economic Association*, 22(4), 1798–1843. [Link](https://doi.org/10.1093/jeea/jvad074), [PDF](http://sci-hub.ren/10.1093/jeea/jvad074), [Google](https://scholar.google.com/scholar?q=How do new entries of discount variety stores affect local markets).

* South, E. C., Hohl, B. C., Kondo, M. C., MacDonald, J. M., & Branas, C. C. (2018). Effect of Greening Vacant Land on Mental Health of Community-Dwelling Adults: A Cluster Randomized Trial. *Proceedings of the National Academy of Sciences*, 115(11), 2940–2945. [Link](https://doi.org/10.1073/pnas.1718503115), [PDF](http://sci-hub.ren/10.1073/pnas.1718503115), [Google](https://scholar.google.com/scholar?q=10.1073/pnas.1718503115).

* 开源代码示例：Pollmann 的空间处理示例仓库(含 CNN 倾向得分地图与反事实地点抽样流程)。[Link](https://github.com/michaelpollmann/spatialTreat-example). ([GitHub][10])

[1]: https://www.kylebutts.com/?utm_source=chatgpt.com "Kyle Butts"
[2]: https://direct.mit.edu/rest/article/105/2/359/100977/Local-Effects-of-Large-New-Apartment-Buildings-in?utm_source=chatgpt.com "Local Effects of Large New Apartment Buildings in Low- ..."
[3]: https://academic.oup.com/jeea/article-abstract/22/4/1798/7472115?utm_source=chatgpt.com "How do New Entries of Discount Variety Stores Affect Local ..."
[4]: https://www.nhh.no/globalassets/departments/economics/papers/europris__final_version__for_posting_on_food_page_.pdf?utm_source=chatgpt.com "how do new entries of discount variety stores affect local"
[5]: https://ideas.repec.org/a/eee/juecon/v133y2023ics0094119022000705.html?utm_source=chatgpt.com "JUE Insight: Difference-in-differences with geocoded ..."
[6]: https://www.aeaweb.org/articles?id=10.1257%2Faer.98.3.1103&utm_source=chatgpt.com "Estimates of the Impact of Crime Risk on Property Values ..."
[7]: https://michaelpollmann.github.io/files/pollmann_spatial_treatments.pdf?utm_source=chatgpt.com "Causal Inference for Spatial Treatments"
[8]: https://michaelpollmann.github.io/?utm_source=chatgpt.com "Michael Pollmann | Assistant Professor, Department of ..."
[9]: https://arxiv.org/abs/2011.00373?utm_source=chatgpt.com "Causal Inference for Spatial Treatments"
[10]: https://github.com/michaelpollmann/spatialTreat-example?utm_source=chatgpt.com "michaelpollmann/spatialTreat-example"
