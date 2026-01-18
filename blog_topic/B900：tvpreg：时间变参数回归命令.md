# B900：tvpreg：时间变参数回归命令

- Inoue, A., Rossi, B., Wang, Y., & Zhou, L. (2025). Parameter path estimation in unstable environments: The tvpreg command. The Stata Journal, 25(2), 374–406. [Link](https://journals.sagepub.com/doi/10.1177/1536867X251341170), [Google](<https://scholar.google.com/scholar?q=Parameter path estimation in unstable environments: The tvpreg command>). [Slides](http://fmwww.bc.edu/repec/econ2024/Econ24_Wang.pdf)
  - 需要自己想办法找到 PDF 文档

**提示**：撰写推文时，可以使用 Claude.ai 等 AI 工具，配合 [empirical-paper-keynotes-Prompt.md](https://github.com/lianxhcn/myprompt/blob/main/body/empirical-paper-keynotes-Prompt.md) 提示词来生成初稿，然后补充 Stata 运行结果，截图等内容，进行润色和完善。

## 安装和下载

执行如下命令进入安装和下载页面：

```stata
net sj 25-2 st0776
```

- 点击 `click here to install`，安装程序文件；
- 点击 `click here to get`，下载作者提供的示例数据文件，dofile 和 PDF 说明文档。

你可以也可以直接执行如下命令安装：

```stata
net install st0776.pkg, from(http://www.stata-journal.com/software/sj25-2)
```

然后，执行如下命令下载示例数据文件到当前工作目录：

```stata
net get st0776.pkg, from(http://www.stata-journal.com/software/sj25-2)

* st0776/data_fiscal.dta
* st0776/data_mp.dta
* st0776/data_pc_weak.dta
* st0776/demo.do
* st0776/readme.txt
```

推文中的例子可以从这里面选取，解释可以从作者的 PDF 原文中获取。
