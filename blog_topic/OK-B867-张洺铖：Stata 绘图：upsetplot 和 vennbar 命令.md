# B867：Stata 绘图：upsetplot 和 vennbar 命令

适当精简这篇论文，翻译为中文，酌情删减或补充。

写一篇推文介绍这个方法的基本思想、应用场景和 Stata 实操步骤，适当精简这篇论文，翻译为中文，酌情删减或补充。

写作过程中可以借助 AI 工具辅助完成，但请确保内容准确无误，并符合学术规范。
- AI 辅助读论文：参见 [连玉君-Research with AI](https://lianxhcn.github.io/research_with_AI/), Chatper 7-9.

Cox, N. J., & Morris, T. P. (2024). Speaking Stata: The joy of sets: Graphical alternatives to Euler and Venn diagrams. The Stata Journal, 24(2), 329–361. [Link](https://journals.sagepub.com/doi/10.1177/1536867X241258010), [PDF](https://journals.sagepub.com/doi/pdf/10.1177/1536867X241258010), [Google](<https://scholar.google.com/scholar?q=Speaking Stata: The joy of sets: Graphical alternatives to Euler and Venn diagrams>).

## 安装
```stata
net describe gr0095
net install gr0095.pkg, replace 
net get gr0095.pkg, replace   // 作者提供的 dofile
```

```stata
*-绘图模板
ssc install scheme_scientific, replace
set scheme scientific
```

## Stata 实例

这是用作者提供的 dofile 整理后得到的。

- 你可以在 Stata 中运行代码，得到图片
- 请调整代码的格式，每列宽度不要超过 60 列
- 在附录中统一放置一份完整的代码，以便读者可以直接粘贴到 Stata 中运行

```stata
*-自定义数据集
clear
input Rice Maize Sorghum Arabidopsis freq
    1 0 0 0 1110
    1 1 0 0 229
    0 1 0 0 465
    1 0 1 0 661
    1 1 1 0 2077
    0 1 1 0 405
    0 0 1 0 265
    1 0 1 1 304
    1 1 1 1 8494
    0 1 1 1 112
    0 0 1 1 34
    1 0 0 1 81
    1 1 0 1 96
    0 1 0 1 11
    0 0 0 1 1058
end
label variable Arabidopsis "{it:Arabidopsis}"
save "arms.dta", replace   // 保存以备后用
```

```stata
use "arms.dta", clear

global toptitle "t1title(Number of gene families)"
global paperopts matrixopts(mc(gs8 ..))

upsetplot Arabidopsis Rice Maize Sorghum [fw=freq], ///
          varlabels baropts($toptitle) $paperopts
graph export "B867-speak70a.png", replace width(800)

upsetplot Arabidopsis Rice Maize Sorghum [fw=freq], ///
          varlabels baropts($toptitle) $paperopts gsort(_decimal) 
graph export "B867-speak70b.png", replace width(800)

upsetplot Arabidopsis Rice Maize Sorghum [fw=freq], ///
          varlabels gsort(_degree -_freq) $paperopts baropts($toptitle) 
graph export "B867-speak70c.png", replace width(800)

upsetplot Arabidopsis Rice Maize Sorghum [fw=freq], ///
          varlabels gsort(-_degree -_freq) $paperopts baropts($toptitle) 



upsetplot Arabidopsis Rice Maize Sorghum [fw=freq], ///
          varlabels gsort(-_degree -_freq) $paperopts baropts($toptitle) savedata("schnable")

use "schnable", clear
graph hbar (asis) _setfreq,  ///
      over(_set, sort(1)) blabel(bar) ///
      ysc(off) $toptitle 


webuse "nlswork.dta", clear

foreach v in ind_code union wks_ue tenure wks_work {
    generate M`v' = missing(`v')
    label variable M`v' "`v'"
}

local toptitle "t1title(Number of missing values)"
local paperopts "matrixopts(mc(gs8 ..))"
upsetplot M*, ///
          varlabels baropts($toptitle) labelopts(mlabsize(vsmall)) $paperopts 
graph export "B867-speak70d.png", replace width(800)

upsetplot M* if missing(ind_code, union, wks_ue, tenure, wks_work), ///
          varlabels baropts($toptitle) labelopts(mlabsize(vsmall)) $paperopts gsort(_degree -_freq) 
graph export "B867-speak70e.png", replace width(800)

use "arms.dta", clear 
local toptitle "t1title(Number of gene families)"

vennbar Arabidopsis Rice Maize Sorghum [fw=freq], $toptitle varlabels ysc(alt) ysc(r(. 9200)) 
graph export "B867-speak70f.png", replace width(800)


vennbar Arabidopsis Rice Maize Sorghum [fw=freq], $toptitle varlabels sep("; ") ysc(r(0 10000)) recast(dot) linetype(line) lines(lc(gs8) lw(thin)) 

vennbar Arabidopsis Rice Maize Sorghum [fw=freq],  over(_text, sort(_freq) descending) over(_degree, descending) nofill $toptitle ysc(alt range(. 9200)) 
graph export "B867-speak70g.png", replace width(800)

webuse "nlswork.dta", clear
foreach v in ind_code union wks_ue tenure wks_work {
gen M`v' = missing(`v')
label variable M`v' "`v'"
}
local toptitle  "t1title(Number of missing values)"

vennbar M*, $toptitle  varlabels name(joyFig8, replace) 
graph export "B867-speak70h.png", replace width(800)

vennbar M* if missing(ind_code, union, wks_ue, tenure, wks_work), ///
    $toptitle varlabels ///
    over(_text, sort(_freq) descending) ///
    over(_degree) nofill ysc(r(. 9200)) 
graph export "B867-speak70i.png", replace width(800)
```
