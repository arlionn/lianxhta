# B901：xthbtest：检验固定效应模型斜率异质性偏差的命令

- Alejo, J., Galvao, A. F., & Montes-Rojas, G. (2025). Testing for slope heterogeneity bias in the fixed-effects estimator. The Stata Journal, 25(4), 836–846. [Link](https://journals.sagepub.com/doi/10.1177/1536867X251398607), [PDF](https://journals.sagepub.com/doi/pdf/10.1177/1536867X251398607), [Google](<https://scholar.google.com/scholar?q=Testing for slope heterogeneity bias in the fixed-effects estimator>).
  - 需要自己想办法找到 PDF 文档
  
## 安装和下载

执行如下命令进入安装和下载页面：

```stata
net sj 25-4 st0793
```

- 点击 `click here to install`，安装程序文件；
- 点击 `click here to get`，下载作者提供的示例数据文件，dofile 和 PDF 说明文档。

你可以也可以直接执行如下命令安装：

```stata
net install st0793.pkg, from(http://www.stata-journal.com/software/sj25-4)
```

然后，执行如下命令下载示例数据文件到当前工作目录：

```stata
net get st0793.pkg, from(http://www.stata-journal.com/software/sj25-4)

* st0793/growth_example_dataset.dta
* st0793/growth_example.do
```

推文中的例子可以从这里面选取，解释可以从作者的 PDF 原文中获取。
