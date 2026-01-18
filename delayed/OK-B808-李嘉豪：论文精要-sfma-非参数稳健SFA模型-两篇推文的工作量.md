# B808：论文精要-sfma-非参数稳健SFA模型

两篇推文的工作量，要求 `2025/6/30` 前完成。

- 写一篇中文精要，介绍这个方法的背景、模型设定、估计方法和应用场景。
  - 格式和要求，参见 [论文复现和中文精要类推文写作指南](https://file.lianxh.cn/KC/lianxh_TA_replication_Guide.pdf) (内附往期范例文档).
- 写一个 `.ipynb` 文件，讲义格式，内嵌 Python 代码和结果解读。(Python 环境配置，参见 [Python：安装和环境配置](https://book.lianxh.cn/ds/body/01_1_install-Python-Anocanda.html))


--- - --


Robust non-parametric frontier estimation

> Zheng, P., Worku, N., Bannick, M., Dielemann, J., Weaver, M., Murray, C., & Aravkin, A. (2024). Robust Nonparametric Stochastic Frontier Analysis (Version 1). arXiv. [Link](https://doi.org/10.48550/arXiv.2404.04301) (rep), [PDF](https://arxiv.org/pdf/2404.04301.pdf), [Google](<https://scholar.google.com/scholar?q=Robust Nonparametric Stochastic Frontier Analysis (Version 1)>). [github](https://github.com/ihmeuw-msca/sfma)

- sfma
  - `pip install sfma`
  - `from sfma import SFA`
  - The SFMA package is available for general public use through the Python package `sfma`, available at <https://github.com/ihmeuw-msca/sfma>.

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20250610225636.png)

| Model  | Stochastic | Non-parametric | Specified Obs. SE | Outlier-Robust |
| :----- | :--------- | :------------- | :---------------- | :------------- |
| DEA    | x          | ✓              | x                 | x              |
| SFA    | ✓          | x              | x                 | x              |
| StoNED | ✓          | ✓              | x                 | x              |
| SFMA   | ✓          | ✓              | ✓                 | ✓              |

### sfma 的特点

The approach in this paper, dubbed SFMA, addresses three key aspects:

-   1. Nonparametric modeling of the frontier using splines
-   2. Detailed modeling of statistical errors, including reported sampling error, as well as unknown non-sampling error and inefficiencies
-   3. Automated outlier detection and removal.

To provide a robust software tool, we implement a customized optimization algorithm that exploits problem structure and performs a single unified analysis that determines outliers, estimates error statistics, and infers the spline coefficients of the frontier and other covariate multipliers of interest.

The SFMA approach can strongly influence our understanding and interpretation of the results, particularly for complex real datasets like those presented here: GDP vs life expectancy (LE), physician density vs universal health coverage (UHC), and nurse density vs health coverage. For these datasets, SFMA obtains helpful results, while a cross-section of tools available across Stata, R, and Python fall short: StoNED fails to converge, SFA and DEA return results that run counter to conventional wisdom in the field, as described below.

-   1. For LE vs. GDP, DEA shows no change in LE for different levels of GDP, while the SFA 'frontier' stays far below the upper bound of the data, and reports a very steep (almost vertical) increase in life expectancy at low levels of GDP per capita. The SFMA analysis suggests that LE increases gradually with GDP, albeit more at lowest levels of GDP than higher. That result is made possible due to the outlier-removal functionality available to SFMA.

-   2. For health coverage, the DEA analysis suggests that physicians have no effect on UHC above 10 physicians per 10,000 population, a clearly falsifiable conclusion. The SFA and SMFA analyses more plausibly show some benefit of more physicians; SFA gain fails to reach the upper limits of the data.
