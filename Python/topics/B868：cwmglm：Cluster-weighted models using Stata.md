# B868：cwmglm：Cluster-weighted models using Stata

写一篇推文介绍这个方法的基本思想、应用场景和 Stata 实操步骤，适当精简这篇论文，翻译为中文，酌情删减或补充。

写作过程中可以借助 AI 工具辅助完成，但请确保内容准确无误，并符合学术规范。
- AI 辅助读论文：参见 [连玉君-Research with AI](https://lianxhcn.github.io/research_with_AI/), Chatper 7-9.

Spinelli, D., Ingrassia, S., & Vittadini, G. (2024). Cluster-weighted models using Stata. The Stata Journal, 24(4), 711–745. [Link](https://journals.sagepub.com/doi/10.1177/1536867X241297922), [PDF](https://d1wqtxts1xzle7.cloudfront.net/115819483/2024Stata_J_AAM-libre.pdf?1717952378=&response-content-disposition=inline%3B+filename%3DCluster_weighted_models_using_Stata.pdf&Expires=1762007528&Signature=T3gj5qLiFfzwoK23V~MuM8WvYKTTAwl5wR8h7as1fY9d0gYSF2KcpStd-urkxhbAZDX4cg-V3qKHHzfK94qGtpb~RcchsDHhamvKaCJDqr0ccsPGbUMXPvxgz0Zc3PT6Ryvi3nWH8lDJULGOysDyyYee3eVu-99p99Aa2Wpi1hUDE1eg8HvbjgctUgjTokezWpj3Y~N9sqPPjgWdRhsyPrbNQNJ5XBe5e-ISG1Dn2S1b5jr~SeD-43XJa2-q83-3GbEyZBJP3DNHti3xZ7dhM4LV2o4SafWiqhEsx43iJUY6SZkgqWPv2eTcf8hzGDnX9kD-TaRy8Zd0LbQmQImWCQ__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA), [Google](<https://scholar.google.com/scholar?q=Cluster-weighted models using Stata>).



```stata
net install st0762.pkg, replace 

net get st0762.pkg, replace  // 作者提供的 dofile
```

```stata
covid.dta
cwmglm.do
multinorm.dta
online_supplementary_material.pdf
replication_files.do
students.dta
```
