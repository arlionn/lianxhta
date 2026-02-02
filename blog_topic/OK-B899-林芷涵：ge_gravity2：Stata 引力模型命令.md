# OK-B899-林芷涵：ge_gravity2：Stata 引力模型命令

- Campos, R. G., Reggio, I., & Timini, J. (2025). ge_gravity2: A command for solving universal gravity models. The Stata Journal, 25(4), 743–771. [Link](https://journals.sagepub.com/doi/10.1177/1536867X251398335), [PDF](https://journals.sagepub.com/doi/pdf/10.1177/1536867X251398335), [PDF-wp](https://rolf-campos.github.io/project/ge_gravity2/ge_gravity2.pdf), [Google](<https://scholar.google.com/scholar?q=ge_gravity2: A command for solving universal gravity models>).

>**Note-Important!**：
> - **初稿**：我已经生成过该文的初稿，可以到 [lianxhta/Sample](https://github.com/arlionn/lianxhta/tree/main/sample) 目录下查看该推文编号对应的相关文档。
> - AI 无法插入图片、表格等。这些内容需要你从论文原文中截图插入，有些图片需要执行代码后插入。
> - AI 无法直接运行代码，因此初稿中的代码需要你自行运行，并将结果采用代码块的形式插入推文中。你可以借助 AI 补充一些针对代码和运行结果的解释文字。
> - AI 生成的初稿中，可能会遗漏一些重要的细节和内容，请务必对照论文原文进行补充和完善，确保推文内容的完整性和准确性。
> - AI 的表述难免存在「机器味」，因此，你需要对推文进行润色和修改，使其更符合人类读者的阅读习惯和理解方式。

---

## 安装

执行如下命令后，

- 点击 `click here to install`，安装程序文件；
- 点击 `click here to get`，下载作者提供的示例数据文件，dofile 和 PDF 说明文档。 

```stata
net describe st0790, from(http://www.stata-journal.com/software/sj25-4)
```

你可以也可以直接执行如下命令安装：

```stata
net install st0790.pkg, from(http://www.stata-journal.com/software/sj25-4)
```

然后，执行如下命令下载示例数据文件到当前工作目录：

```stata
net get st0790.pkg, from(http://www.stata-journal.com/software/sj25-4)

* st0790/appendix_ge_gravity2.pdf
* st0790/examples.do
* st0790/ge_gravity2_example_data.dta
```

执行如下命令即可打开作者提供的 `examples.do` 示例文件：

```stata
doedit examples.do
```

推文中的例子可以从这里面选取，解释可以从作者的 PDF 原文中获取。
