# B821：论文推介和复现：Abadie's Kappa 和加权局部平均处理效应估计

这是两篇推文的工作量。

1. 简要介绍该文的基本思想、模型假设和适用条件
2. 介绍模型设定和估计思路 (可以忽略技术细节和证明过程，但需要呈现最核心的模型设定)。这些工作可以通过 AI 辅助完成：把 PDF 原文发给 AI，让它撰写这部分内容。提示词写法参见 [如何借助 AI 工具来伴读一篇理论类的论文？](https://www.lianxh.cn/details/1571.html)
3. 重点介绍论文 Section 4 中复现的三篇论文。包括
   - 论文的研究问题和数据来源
   - 论文的主要结论和贡献
   - 模型设定的数学公式和变量界定等
   - 复现代码展示，结果和分析

> Słoczyński, T., Uysal, S. D., & Wooldridge, J. M. (2024). Abadie’s Kappa and Weighting Estimators of the Local Average Treatment Effect. Journal of Business & Economic Statistics, 43(1), 164–177. [Link](https://doi.org/10.1080/07350015.2024.2332763), [PDF](http://sci-hub.ren/10.1080/07350015.2024.2332763), [Google](<https://scholar.google.com/scholar?q=Abadie’s Kappa and Weighting Estimators of the Local Average Treatment Effect>).
- [Replication files](https://doi.org/10.6084/m9.figshare.25438511.v2)
  - Stata codes and data for three papers. 
  - `card.dta`
  - `README.pdf`
  - `sipp.dta`
  - `suw_jbes_replicate.do`
  - `suw_jbes_significance.do`
  - `ae98.dta`


## Tips

论文复现类推文写作指南参见 [连享会·助教答疑和推文指南](https://file.lianxh.cn/KC/lianxh_TA_Guide.pdf)，第 5 节。

可以参考 B813 的初稿生成过程，借助 AI 完成一部分内容。

- [B813-豆包对话过程](https://www.doubao.com/thread/w8e524bfc59a644e2))
- [B813-初稿](https://github.com/arlionn/lianxhta/blob/main/sample/B813-Stragged-DID.md)
