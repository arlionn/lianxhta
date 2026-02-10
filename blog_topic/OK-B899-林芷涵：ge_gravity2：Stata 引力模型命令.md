
> **作者：** 林芷涵(首都经济贸易大学)    
> **邮箱：** <zhihan_lin0211@163.com> 
> 
&emsp;

> **编者按**：本文参考了如下资料，特此致谢！  
> Campos, R. G., Reggio, I., & Timini, J. (2025). ge_gravity2: A command for solving universal gravity models. *The Stata Journal*, 25(4), 743–771. [Link](https://doi.org/10.1177/1536867X251398335), [PDF](https://journals.sagepub.com/doi/pdf/10.1177/1536867X251398335), [Google](<https://scholar.google.com/scholar?q=ge_gravity2: A command for solving universal gravity models>).


&emsp; 

- **Title**: ge_gravity2：Stata 引力模型命令——求解一般均衡引力模型的利器
- **Keywords**: 引力模型, 一般均衡, 国际贸易, ge_gravity2, 反事实分析
  
&emsp; 

## 1. 简介

引力模型 (gravity model) 是国际贸易领域中最为成功的实证工具之一。自 Tinbergen (1962) 首次将牛顿万有引力定律的形式引入贸易流量分析以来，引力模型已经成为解释双边贸易流量的标准框架。其核心思想简洁而直观：两国之间的贸易量与两国的经济规模成正比，与两国之间的贸易成本成反比。然而，传统的引力模型虽然在拟合贸易数据方面表现出色，却难以回答政策制定者最关心的问题——某项贸易政策的实施会对各国的福利和贸易格局产生怎样的影响？

这正是一般均衡引力模型 (general equilibrium gravity model) 的用武之地。与传统的部分均衡分析不同，一般均衡框架能够捕捉贸易政策变化引发的连锁反应：贸易成本的下降不仅会直接影响双边贸易流量，还会通过价格指数、工资水平等渠道间接影响所有国家的贸易和福利。Anderson and van Wincoop (2003) 的开创性工作为引力模型奠定了坚实的微观基础，但求解这类模型需要处理复杂的非线性方程组，这在很长时间内限制了其在实证研究中的应用。

Campos, Reggio 和 Timini (2025) 在 *The Stata Journal* 发表的文章介绍了 `ge_gravity2` 命令，为研究者提供了一个功能强大且易于使用的工具来求解一般均衡引力模型。该命令不仅能够估计模型参数，还能进行反事实分析 (counterfactual analysis)，评估贸易政策变化对各国福利、贸易流量和价格指数的影响。更重要的是，`ge_gravity2` 支持多种贸易成本的函数形式，能够灵活处理关税、非关税壁垒等多维度的贸易摩擦。

**关键 Stata 命令**：
- 核心命令：`ge_gravity2`
- 辅助命令：`preserve`, `restore`, `merge`

## 2. 引力模型的理论基础

### 2.1 从 Armington 模型到一般均衡

现代引力模型的理论基础源于 Armington (1969) 的差异化产品模型。在这一框架下，每个国家生产的商品被视为不完全替代品。消费者对来自不同国家的商品具有恒替代弹性 (constant elasticity of substitution, CES) 偏好。这一假设虽然简化，却为引力方程提供了严格的微观基础。

Anderson and van Wincoop (2003) 在此基础上引入了多边阻力项 (multilateral resistance terms) 的概念。他们指出，两国之间的贸易流量不仅取决于双边贸易成本，还受到所有贸易伙伴的平均贸易成本的影响。这一洞见解决了传统引力模型忽视第三国效应的问题，使得模型能够更准确地预测贸易流量的变化。

在一般均衡框架下，引力模型需要同时求解三组关键变量：(1) 各国的产出和支出水平；(2) 向内和向外的多边阻力项；(3) 双边贸易流量。这些变量相互依赖，形成了一个非线性方程组系统。传统方法求解这类系统往往需要反复迭代，计算成本高昂且容易出现收敛问题。

### 2.2 ge_gravity2 的创新之处

`ge_gravity2` 命令采用了 Yotov et al. (2016) 提出的求解算法，该算法的核心思想是将一般均衡问题转化为一系列条件固定效应回归。具体而言，作者利用了引力方程的结构特征：在给定多边阻力项的情况下，可以通过泊松拟似最大似然法 (Poisson pseudo-maximum likelihood, PPML) 估计贸易弹性和固定效应。然后，利用这些固定效应更新多边阻力项，直至收敛。

这一方法的优势在于：第一，避免了直接求解非线性方程组，大大提高了计算效率；第二，PPML 估计量对异方差和零贸易流量具有稳健性，这在贸易数据中尤为重要；第三，算法的收敛性有理论保证，不会出现传统迭代方法常见的发散问题。

更进一步，`ge_gravity2` 允许用户灵活设定贸易成本的函数形式。例如，研究者可以将贸易成本建模为关税、距离、共同语言等多个因素的函数。命令还支持不对称的贸易成本 (如出口国和进口国的边境效应)，以及部门异质性 (不同产品的贸易弹性可能不同)。

## 3. 一般均衡引力模型的数学推导

### 3.1 模型设定与基本假设

为了理解 `ge_gravity2` 命令的工作原理，我们需要先建立一般均衡引力模型的数学框架。考虑一个包含 $N$ 个国家的世界经济，每个国家 $i$ 生产一种差异化产品。消费者对来自不同国家的产品具有 CES 偏好，效用函数可以写为：

$$U_j = \left[\sum_{i=1}^{N} \beta_i^{1/\sigma} q_{ij}^{(\sigma-1)/\sigma}\right]^{\sigma/(\sigma-1)}$$

其中，$q_{ij}$ 表示国家 $j$ 消费来自国家 $i$ 的产品数量，$\sigma > 1$ 是替代弹性，$\beta_i$ 是对国家 $i$ 产品的偏好参数。

消费者面临预算约束：

$$\sum_{i=1}^{N} p_{ij} q_{ij} = Y_j$$

其中，$p_{ij}$ 是国家 $j$ 的消费者购买国家 $i$ 产品的价格 (包含贸易成本)，$Y_j$ 是国家 $j$ 的总支出。

通过效用最大化，可以得到国家 $j$ 对来自国家 $i$ 产品的需求函数：

$$q_{ij} = \beta_i \left(\frac{p_{ij}}{P_j}\right)^{-\sigma} \frac{Y_j}{P_j}$$

其中，$P_j$ 是国家 $j$ 的 CES 价格指数：

$$P_j = \left[\sum_{i=1}^{N} \beta_i p_{ij}^{1-\sigma}\right]^{1/(1-\sigma)}$$

### 3.2 贸易成本与价格关系

设国家 $i$ 的出厂价格为 $p_i$，则国家 $j$ 消费者面临的价格为：

$$p_{ij} = p_i \tau_{ij}$$

其中，$\tau_{ij} \geq 1$ 是冰山贸易成本 (iceberg trade cost)，表示从国家 $i$ 运送到国家 $j$ 需要运输 $\tau_{ij}$ 单位产品才能让消费者获得 1 单位产品。贸易成本可以进一步分解为：

$$\tau_{ij} = \exp\left(\sum_{k} \gamma_k TC_{ij}^k\right)$$

其中，$TC_{ij}^k$ 是第 $k$ 个贸易成本因素 (如距离、关税、共同语言等)，$\gamma_k$ 是相应的弹性系数。这正是 `ge_gravity2` 命令中 `tc()` 选项所指定的变量。

### 3.3 引力方程的推导

将价格关系代入需求函数，国家 $j$ 从国家 $i$ 的进口额为：

$$X_{ij} = p_{ij} q_{ij} = \beta_i p_i^{1-\sigma} \tau_{ij}^{1-\sigma} P_j^{\sigma-1} Y_j$$

假设国家 $i$ 的总产出为 $Y_i$，市场出清条件要求：

$$Y_i = \sum_{j=1}^{N} X_{ij}$$

将需求函数代入并整理，可以得到：

$$Y_i = \sum_{j=1}^{N} \beta_i p_i^{1-\sigma} \tau_{ij}^{1-\sigma} P_j^{\sigma-1} Y_j$$

从这个条件可以解出生产者价格：

$$p_i = \left[\frac{Y_i}{\beta_i \sum_{j=1}^{N} \tau_{ij}^{1-\sigma} P_j^{\sigma-1} Y_j}\right]^{1/(1-\sigma)}$$

定义向外多边阻力项 (outward multilateral resistance)：

$$\Pi_i = \left[\sum_{j=1}^{N} \left(\frac{\tau_{ij}}{P_j}\right)^{1-\sigma} \frac{Y_j}{Y^W}\right]^{1/(1-\sigma)}$$

其中，$Y^W = \sum_{i=1}^{N} Y_i$ 是世界总产出。类似地，定义向内多边阻力项 (inward multilateral resistance)：

$$P_j = \left[\sum_{i=1}^{N} \left(\frac{\tau_{ij}}{\Pi_i}\right)^{1-\sigma} \frac{Y_i}{Y^W}\right]^{1/(1-\sigma)}$$

利用这些定义，可以将双边贸易流量改写为著名的结构引力方程 (structural gravity equation)：

$$X_{ij} = \frac{Y_i Y_j}{Y^W} \left(\frac{\tau_{ij}}{\Pi_i P_j}\right)^{1-\sigma}$$

取对数并利用贸易成本的分解式，得到可估计的引力方程：

$$\ln X_{ij} = \ln Y_i + \ln Y_j - \ln Y^W + (1-\sigma)\sum_{k} \gamma_k TC_{ij}^k + (1-\sigma)\ln\tau_{ij} - (1-\sigma)\ln\Pi_i - (1-\sigma)\ln P_j$$

### 3.4 与贸易弹性的联系

在实证文献中，研究者通常关心的不是替代弹性 $\sigma$，而是贸易弹性 $\theta = 1 - \sigma < 0$。贸易弹性衡量的是贸易成本变化 1% 时，贸易流量变化的百分比。将 $\theta$ 代入引力方程：

$$X_{ij} = \frac{Y_i Y_j}{Y^W} \left(\frac{\tau_{ij}}{\Pi_i P_j}\right)^{\theta}$$

这就是 `ge_gravity2` 命令中参数 `theta()` 的含义。注意到 $\theta < 0$，这意味着贸易成本越高，贸易流量越小，符合经济直觉。

### 3.5 一般均衡系统的求解

一般均衡引力模型需要同时求解以下三组方程：

**方程组 1：双边贸易流量**

$$X_{ij} = \frac{Y_i Y_j}{Y^W} \left(\frac{\tau_{ij}}{\Pi_i P_j}\right)^{\theta}$$

**方程组 2：向外多边阻力**

$$\Pi_i^{1-\theta} = \sum_{j=1}^{N} \left(\frac{\tau_{ij}}{P_j}\right)^{\theta} \frac{Y_j}{Y^W}$$

**方程组 3：向内多边阻力**

$$P_j^{1-\theta} = \sum_{i=1}^{N} \left(\frac{\tau_{ij}}{\Pi_i}\right)^{\theta} \frac{Y_i}{Y^W}$$

这是一个包含 $N^2 + 2N$ 个未知数和方程的非线性系统。传统的求解方法 (如 Newton-Raphson 迭代) 计算量大且容易出现收敛问题。

### 3.6 固定效应方法的巧妙转化

Anderson and van Wincoop (2003) 以及后续的研究者发现，可以通过固定效应回归来简化求解过程。关键观察是：引力方程可以改写为：

$$X_{ij} = \exp\left(\alpha_i + \alpha_j + \theta \sum_{k} \gamma_k TC_{ij}^k\right)$$

其中，出口国固定效应 $\alpha_i$ 和进口国固定效应 $\alpha_j$ 隐含地包含了多边阻力项的信息：

$$\alpha_i = \ln Y_i - \ln Y^W - \theta \ln \Pi_i$$

$$\alpha_j = \ln Y_j - \theta \ln P_j$$

这样，问题就转化为：给定多边阻力项的初值，通过 PPML 估计固定效应和贸易成本系数，然后利用固定效应更新多边阻力项，直至收敛。

具体算法如下：

**步骤 1**：初始化 $\Pi_i^{(0)} = P_j^{(0)} = 1$ for all $i, j$

**步骤 2**：运行 PPML 回归，估计固定效应 $\hat{\alpha}_i, \hat{\alpha}_j$ 和系数 $\hat{\gamma}_k$

**步骤 3**：利用固定效应更新多边阻力项：

$$\Pi_i^{(t+1)} = \exp\left(-\frac{\hat{\alpha}_i + \ln Y^W - \ln Y_i}{\theta}\right)$$

$$P_j^{(t+1)} = \exp\left(-\frac{\hat{\alpha}_j - \ln Y_j}{\theta}\right)$$

**步骤 4**：检查收敛性。若 $\max\{|\Pi_i^{(t+1)} - \Pi_i^{(t)}|, |P_j^{(t+1)} - P_j^{(t)}|\} < \epsilon$，则停止；否则返回步骤 2

这正是 `ge_gravity2` 命令默认采用的 `solver(yotov)` 方法的核心思想。

### 3.7 反事实分析的理论框架

反事实分析的目标是评估贸易成本从基准情景 $\{\tau_{ij}\}$ 变化到反事实情景 $\{\tau_{ij}'\}$ 时对经济的影响。关键变量是福利变化，可以用实际收入的变化来衡量：

$$W_j = \frac{w_j}{P_j}$$

其中，$w_j$ 是名义工资。在简单的禀赋经济中 (endowment economy)，$w_j = Y_j / L_j$，$L_j$ 是劳动力。

福利的对数变化可以分解为：

$$\ln\frac{W_j'}{W_j} = \ln\frac{w_j'}{w_j} - \ln\frac{P_j'}{P_j}$$

第一项反映了名义收入的变化 (通过贸易条件效应)，第二项反映了价格指数的变化 (通过多边阻力效应)。`ge_gravity2` 命令会自动计算这些变化，并输出为百分比形式，方便研究者解读。

需要注意的是，在更复杂的设定中 (如包含生产函数、要素流动等)，福利的计算会更加复杂。但基本逻辑保持不变：通过比较两个一般均衡状态下的关键变量，评估政策变化的影响。

### 3.8 数值稳定性与收敛性

在实际应用中，一般均衡系统的求解可能面临数值稳定性问题。`ge_gravity2` 采用了多种技术来提高收敛性：

**归一化处理**：通过选择某个国家作为基准 (numeraire)，将其多边阻力项固定为 1，避免系统的不定性。

**阻尼因子**：在更新多边阻力项时引入阻尼 (damping)，即 $\Pi_i^{(t+1)} = \lambda \Pi_i^{(新)} + (1-\lambda)\Pi_i^{(t)}$，其中 $0 < \lambda < 1$。这可以防止迭代过程中的振荡。

**多重初值**：当默认初值 (所有多边阻力项为 1) 无法收敛时，命令会自动尝试其他初值，如使用上一次估计的结果或随机扰动。

**精度控制**：用户可以通过 `tolerance()` 选项设定收敛标准。较松的标准会更快得到结果，但可能牺牲精度；较严的标准则相反。默认值通常在 $10^{-6}$ 到 $10^{-8}$ 之间，能够在精度和效率之间取得良好平衡。

通过理解这些数学原理，研究者可以更好地判断何时应该调整模型设定或参数假设，以及如何解释命令输出的结果。

## 4. ge_gravity2 命令的使用

### 4.1 命令安装

执行如下命令后，点击 `click here to install` 安装程序文件；点击 `click here to get` 下载作者提供的示例数据文件、dofile 和 PDF 说明文档。

```stata
net describe st0790, from(http://www.stata-journal.com/software/sj25-4)
```

你也可以直接执行如下命令安装：

```stata
net install st0790.pkg, from(http://www.stata-journal.com/software/sj25-4)
```

然后，执行如下命令下载示例数据文件到当前工作目录：

```stata
net get st0790.pkg, from(http://www.stata-journal.com/software/sj25-4)

* 下载的文件包括：
* st0790/appendix_ge_gravity2.pdf
* st0790/examples.do
* st0790/ge_gravity2_example_data.dta
```

执行如下命令即可打开作者提供的 `examples.do` 示例文件：

```stata
doedit examples.do
```

### 4.2 基本语法

`ge_gravity2` 的基本语法如下：

```stata
 ge_gravity2 exp_id imp_id flows partial [if] [in], theta(#) [psi(#) gen_options other_options]
```

其中：
- `exp_id` 出口国 / 地区标识（如 ISO 代码、国家名称）
- `imp_id` 进口国 / 地区标识（如 ISO 代码、国家名称）
- `flows` 双边贸易流量
- `partial` 局部均衡效应变量
- `theta(#)` 贸易弹性，必须为严格正数，需参考文献或前期估计校准
- `psi(#)` 总供给弹性，非负数值，选填，默认值 psi(0)
- `gen_options`生成变量选项：一类与通用引力模型相关，`gen_X(varname)`生成反事实贸易流量变量，`gen_rp(varname)`生成实际价格变化变量，`gen_y(varname)`生成收入变化变量，`gen_x(varname)`生成贸易流量变化率变量，`gen_p(varname)`生成产出价格变化变量，`gen_P(varname)`生成价格指数变化变量；另一类与原型贸易模型相关，`gen_w(varname)`生成福利变化变量，`gen_q(varname)`生成实际产出变化变量，`gen_rw(varname)`生成实际工资变化变量，`gen_nw(varname)`生成名义工资变化变量。
- `other_options`其他关键选项：`results`输出结果表格，含出口、进口、总贸易、国内贸易、实际产出、福利的百分比变化；`universal`假设“通用贸易赤字”，允许搭配`xi_hat(matrix)`设定贸易赤字变化；`multiplicative`假设贸易赤字满足`E_hat = Y_hat`，与旧命令 `ge_gravity`向后兼容；`c_hat(matrix)`设定供给转移因子变化矩阵，不可与`a_hat/l_hat`同时使用，默认全为 1；`a_hat(matrix)`设定生产率变化矩阵（原型贸易模型），不可与`c_hat`同时使用，默认全为 1；`l_hat(matrix)`设定劳动力规模变化矩阵（原型贸易模型），不可与`c_hat`同时使用，默认全为 1；`xi_hat(matrix)`设定贸易赤字变化矩阵，必须搭配`universal`选项使用，默认全为 1；`tol(#)`收敛容忍度（严格正数），默认 tol`(1e-12)`；`max_iter(#)`最大迭代次数（正整数），默认`max_iter(1000000)`。

### 4.3 实例分析1：贸易协定的事前效应模拟
贸易协定的事前效应评估是贸易政策分析的核心场景，本实例以北美自由贸易协定（NAFTA）为例，模拟1990年基准情景下，NAFTA对成员国及其他国家贸易流量、产出和福利的一般均衡影响。核心思路是先定义 NAFTA 成员国的双边贸易局部均衡效应，再通ge_gravity2命令求解一般均衡结果，对比基准与反事实情景的经济变量变化。
```stata
** 0. Load data
use ge_gravity2_example_data
** 1. Use a partial equilibrium effect of NAFTA
local beta = 0.500
** 2. Generate an indicator of NAFTA
generate nafta = 0
replace nafta = 1 if iso_o == "CAN" & (iso_d == "MEX" | iso_d == "USA")
replace nafta = 1 if iso_o == "MEX" & (iso_d == "CAN" | iso_d == "USA")
replace nafta = 1 if iso_o == "USA" & (iso_d == "CAN" | iso_d == "MEX")
** 3. Generate the partial equilibrium effect of NAFTA
generate partial_effect = `beta' * nafta
** 4. Obtain general equilibrium effects of NAFTA using data for the year 1990.
// Report a table with results, and generate variables with counterfactual
// flows and W_hat.
// We use a trade elasticity of 5.03 and a supply elasticity of 1.24.
ge_gravity2 iso_o iso_d flow partial_effect if year==1990, ///
	theta(5.03) psi(1.24) results gen_X(flow_nafta) gen_w(welfare)
** Comments:
// The table with results is also available as e(results)
// To see the list of all stored elements, type ereturn list
```
运行此代码后，将生成一个表格，显示样本中所有国家的贸易流量、产出和福利的变化，我们将展示表格前三行和参与贸易协定的国家。
```stata
|     |   Exports    Imports  IntlTrade   Domestic     Output    Welfare |
|-----+------------------------------------------------------------------|
| ARG |     0.066     -0.312     -0.020     -0.012     -0.002     -0.021 |
| AUS |    -0.121     -0.127     -0.124      0.010     -0.005     -0.009 |
| AUT |    -0.042     -0.008     -0.024      0.011     -0.001      0.006 |
| CAN |    38.065     42.002     40.006     -5.476      2.544      4.662 | 
| MEX |    37.509     48.714     42.518     -3.632      1.698      3.182 |
| USA |    16.074     13.065     14.397     -0.644      0.286      0.516 |
```
关键说明：

- 局部均衡效应设定：$\beta=0.5$ 表示 NAFTA 成员国间贸易流的局部均衡提升幅度为
$e^{0.5}−1≈65%$；
- 核心参数：贸易弹性 $\theta=5.03$、供给弹性 $\psi=1.24$ 为实证研究中北美贸易的经典参数设定；
- 命令选项：`results`直接输出出口、进口、产出、福利等变量的百分比变化表；`gen_X`生成反事实贸易流量，`gen_w`生成福利变化的比较静态值；
- 结果解读：成员国（CAN、MEX、USA）会出现显著的出口 / 进口提升，福利正向增长，而非成员国受贸易转移效应影响，贸易和福利出现小幅波动。
  
### 4.4 实例分析2：量化时变边界效应对福利影响(福利效应的敏感性分析)
一般均衡模型的结果对供给弹性 $\psi$ 等核心参数高度敏感，同时现实中贸易政策的影响具有时间异质性。本实例以弗朗哥时期西班牙的边境厚度（border thickness）为例，利用`ge_gravity2`的`by`前缀实现多年份的时变边界效应分析，并通过设定不同供给弹性（0、1、2）开展参数敏感性分析，量化西班牙封闭贸易政策的福利损失。
```stata
** 0. Load data
use ge_gravity2_example_data, clear
keep if year <= 1980
** 1. Use the estimates of the partial equilibrium effect of 
//	  Spain's border thickness
// Source: Campos, Reggio, and Timini (2023).
//         Taken from the replication materials for figure 5.
generate beta_Spain = .
replace beta_Spain = -1.254 if year == 1950
replace beta_Spain = -0.937 if year == 1960
replace beta_Spain = -0.604 if year == 1970
replace beta_Spain = -0.694 if year == 1980 
generate beta_Synthetic_Spain = .
replace beta_Synthetic_Spain = -0.665 if year == 1950
replace beta_Synthetic_Spain = -0.438 if year == 1960
replace beta_Synthetic_Spain = -0.428 if year == 1970
replace beta_Synthetic_Spain = -0.653 if year == 1980 
** 2. Generate an indicator of Spain's borders
generate border = (iso_o != iso_d)
generate border_Spain = border * (iso_o == "ESP" | iso_d == "ESP")
** 3. Generate the partial equilibrium effect of the difference of 
// Spain's actual border thickness  relative to that of Synthetic Spain
generate partial_effect = (beta_Spain - beta_Synthetic_Spain) * border_Spain
** 4. Obtain the general equilibrium effect for different values of the 
//	  supply elasticity. 
//    Campos, Reggio, and Timini (2023) use a trade elasticity of 4.
//    They (implicitly) use a supply elasticity of zero.
//    We use the by prefix to calculate welfare for all years.
quietly bys year: ge_gravity2 iso_o iso_d flow partial_effect, ///
			theta(4) psi(0) gen_w(W0)
quietly bys year: ge_gravity2 iso_o iso_d flow partial_effect, ///
			theta(4) psi(1) gen_w(W1)
quietly bys year: ge_gravity2 iso_o iso_d flow partial_effect, ///
			theta(4) psi(2) gen_w(W2)
** 5. Collapse and express in percentage points
collapse (first) W0 W1 W2, by(iso_o year)
replace W0 = 100 * (W0 - 1)
replace W1 = 100 * (W1 - 1)
replace W2 = 100 * (W2 - 1)
** 6. List welfare losses for Spain
list if iso_o == "ESP"
```
运行此代码后，将生成一个表格，展示不同价格弹性下贸易政策福利效应。
```stata
  +-----------------------------------------------------+
  | iso_o   year           W0           W1           W2 |  
  |-----------------------------------------------------|
  |   ESP   1950   -.77407265   -1.5351519    -2.283892 |
  |   ESP   1960   -.67859623   -1.3304747    -1.974018 |
  |   ESP   1970   -.38828961   -.69828344   -.99902147 |
  |   ESP   1980   -.12803479   -.23526378   -.33850947 |
  +-----------------------------------------------------+
```
关键说明:
- 边境厚度：负向系数表示边境带来的贸易成本提升，系数越小，贸易壁垒越高；
- `by`前缀的使用：实现对 1950/1960/1970/1980 四个年份的分组估计，无需循环命令，简化时变效应分析；
- `quietly`选项：抑制中间输出，仅保留核心结果变量，提升代码运行效率；
- 结果解读：供给弹性 $\psi$ 从0提升至2时，西班牙的福利损失绝对值显著增加，体现了供给侧弹性对贸易政策福利效应的放大作用。
### 4.5 实例分析3：生产率变化的一般均衡模拟
`ge_gravity2`不仅能分析贸易成本冲击，还能通过`a_hat()`选项模拟生产率供给冲击的全球一般均衡效应。本实例以中国生产率提升 10% 为例，模拟单一国家的生产率冲击对本国及全球其他国家贸易、产出和福利的传导效应，核心步骤为 “干运行确定国家位置→构建生产率冲击矩阵→执行带供给冲击的一般均衡求解”。
```stata
** 0. Load data
use ge_gravity2_example_data, clear
** 1. Perform a dry run to see in what position China is ordered
generate partial_effect = 0
ge_gravity2 iso_o iso_d flow partial_effect if year == 1990, theta(5.03) ///
			psi(1.24)
matrix list e(W_hat)            // China is in position 11
** 2. Generate the matrix with the productivity increase
matrix A_hat = J(`e(N)', 1, 1)  // Matrix of size N x 1 filled with ones
matrix A_hat[11, 1] = 1.1       // China's productivity increases by 10%
** 3. Run the command with the a_hat() option
ge_gravity2 iso_o iso_d flow partial_effect if year == 1990, theta(5.03) ///
			psi(1.24) a_hat(A_hat) results
```
运行此代码后，将生成一个表格，显示样本中所有国家的贸易流量、产出和福利的变化，我们将展示表格前三行的国家和中国。
```stata
|     |   Exports    Imports  IntlTrade   Domestic     Output    Welfare |
|-----+------------------------------------------------------------------|
| ARG |     0.109      0.029      0.091     -0.011      0.000     -0.010 |
| AUS |     0.130      0.134      0.132     -0.011      0.005      0.009 |
| AUT |     0.021      0.041      0.031     -0.001      0.003      0.011 |
| CHN |     4.609      7.269      5.575     11.439      9.838     10.776 | 
```
关键说明：

- 干运行的必要性：`ge_gravity2`会根据数据中的国家标识自动排序，干运行可精准获取目标国家的矩阵位置，避免冲击赋值错误；
- `a_hat()`选项：为原型贸易模型中的生产率参数赋值，是模拟供给侧冲击的核心选项，不可与`c_hat()`选项同时使用；
- 结果解读：中国生产率提升会带来本国出口、产出、福利的显著增长，同时通过国际贸易网络传导至其他国家，带来全球贸易的小幅扩张。

## 5. 命令的高级功能

### 5.1 处理多部门模型

现实中不同行业的贸易弹性和供给弹性存在显著异质性（如制成品贸易弹性高于农产品、工业品供给弹性高于初级产品）。`ge_gravity2`支持多部门一般均衡模型，避免了单部门模型的 “平均化偏误”，能精准识别贸易政策对不同行业的异质性影响，例如分析自贸区对制造业和农业的差异化福利效应。

### 5.2 处理内生性问题

双边贸易流量和贸易成本之间可能存在反向因果关系。例如，贸易量大的国家对之间更有可能签订贸易协定。为了解决这一问题，研究者可以使用工具变量法。`ge_gravity2` 与 Stata 的工具变量估计命令 (如 `ivpoisson`) 兼容，可以在估计阶段纳入工具变量。

### 5.3 生成预测的贸易矩阵

`ge_gravity2`提供了丰富的结果生成与存储选项，可将基准贸易矩阵、反事实贸易矩阵、价格 / 福利 / 产出的比较静态值等结果保存为 Stata 变量或矩阵，支持后续的可视化分析、计量检验和政策模拟。

## 6. 与现有工具的比较

在 `ge_gravity2` 出现之前，研究者主要依赖两类工具来求解一般均衡引力模型：一是自编程序，通常使用 Mata 或 Python；二是专门的软件包，如 R 语言的 `gravity` 包。相比之下，`ge_gravity2` 具有以下优势：

首先，它与 Stata 生态系统无缝集成。研究者可以利用 Stata 强大的数据处理和可视化功能，无需在不同软件之间切换。其次，命令的语法设计遵循 Stata 的惯例，学习曲线平缓。第三，`ge_gravity2` 的计算效率较高，即使对于包含数百个国家和地区的大型数据集，通常也能在几分钟内完成求解。

当然，该命令也有一定的局限性。例如，它目前主要支持 CES 偏好设定，对于更复杂的需求结构 (如 translog 偏好) 则需要研究者自行扩展。此外，虽然命令提供了多种求解算法选项，但对于高度非线性的模型，收敛性仍可能是一个挑战。

## 7. 实证研究中的应用前景

`ge_gravity2` 的推出为国际贸易领域的实证研究打开了新的可能性。以下是几个潜在的应用方向：

**贸易政策评估**：研究者可以利用该命令评估关税削减、非关税壁垒降低、贸易协定签订等政策的一般均衡效应。与传统的部分均衡分析相比，一般均衡框架能够更全面地捕捉政策的溢出效应和间接影响。

**全球价值链分析**：随着中间品贸易的兴起，全球价值链 (global value chain, GVC) 成为国际贸易研究的热点。`ge_gravity2` 可以扩展用于分析 GVC 中的上游和下游联系，评估贸易摩擦对生产网络的影响。

**脱钩情景模拟**：在当前地缘政治不确定性加剧的背景下，许多研究者关注经济脱钩 (decoupling) 的潜在后果。利用 `ge_gravity2`，研究者可以模拟不同脱钩情景 (如某些国家之间完全停止贸易) 对全球贸易格局和各国福利的影响。

**气候政策的贸易效应**：碳边境调节机制 (carbon border adjustment mechanism, CBAM) 等气候政策会改变国际贸易的成本结构。`ge_gravity2` 可以用于评估这类政策的一般均衡效应，包括对贸易流量、产业结构和碳排放的影响。

## 8. 小结

Campos, Reggio 和 Timini (2025) 开发的 `ge_gravity2` 命令为 Stata 用户提供了一个强大的工具来求解一般均衡引力模型。该命令不仅降低了模型求解的技术门槛，还通过灵活的选项设置和高效的算法设计，使得研究者能够将更多精力投入到经济问题本身的分析中。

随着国际贸易政策环境的日益复杂，一般均衡分析的重要性愈发凸显。`ge_gravity2` 的推出恰逢其时，必将在未来的实证研究中发挥重要作用。当然，如同任何工具一样，它的有效性最终取决于研究者对经济理论的理解和对数据质量的把控。正确使用该命令的前提是对引力模型的理论基础有深入的认识，并对所分析的政策问题有清晰的研究设计。

对于有志于从事国际贸易实证研究的学者而言，掌握 `ge_gravity2` 命令无疑是一项值得投资的技能。建议读者首先仔细阅读作者提供的示例代码和技术附录，理解命令的工作原理和参数设定的含义。然后，可以从简单的单部门模型入手，逐步过渡到更复杂的多部门、多政策工具的分析。在实际应用中，务必进行充分的稳健性检验和敏感性分析，确保结论的可靠性。

## 参考文献

1. Anderson, J. E., & van Wincoop, E. (2003). Gravity with Gravitas: A Solution to the Border Puzzle. *American Economic Review*, 93(1), 170–192. [Link](https://doi.org/10.1257/000282803321455214), [PDF](https://doi.org/10.1257/000282803321455214), [Google](<https://scholar.google.com/scholar?q=Gravity with Gravitas: A Solution to the Border Puzzle>).

2. Armington, P. S. (1969). A Theory of Demand for Products Distinguished by Place of Production. *Staff Papers - International Monetary Fund*, 16(1), 159–178. [Link](https://doi.org/10.2307/3866403), [PDF](https://doi.org/10.2307/3866403), [Google](<https://scholar.google.com/scholar?q=A Theory of Demand for Products Distinguished by Place of Production>).

3. Campos, R. G., Reggio, I., & Timini, J. (2025). ge_gravity2: A command for solving universal gravity models. *The Stata Journal*, 25(4), 743–771. [Link](https://doi.org/10.1177/1536867X251398335), [PDF](https://journals.sagepub.com/doi/pdf/10.1177/1536867X251398335), [Google](<https://scholar.google.com/scholar?q=ge_gravity2: A command for solving universal gravity models>).

4. Tinbergen, J. (1962). *Shaping the World Economy: Suggestions for an International Economic Policy*. The Twentieth Century Fund. [Link](), [PDF](), [Google](<https://scholar.google.com/scholar?q=Shaping the World Economy: Suggestions for an International Economic Policy>).

5. Yotov, Y. V., Piermartini, R., Monteiro, J.-A., & Larch, M. (2016). *An Advanced Guide to Trade Policy Analysis: The Structural Gravity Model*. World Trade Organization. [Link？？](), [PDF](https://www.wto.org/english/res_e/publications_e/advancedwtounctad2016_e.htm), [Google](<https://scholar.google.com/scholar?q=An Advanced Guide to Trade Policy Analysis: The Structural Gravity Model>).
