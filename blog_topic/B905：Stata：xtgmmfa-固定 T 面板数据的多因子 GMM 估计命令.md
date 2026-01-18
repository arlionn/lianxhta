# B905：Stata：xtgmmfa-固定 T 面板数据的多因子 GMM 估计命令

> Juodis, A., & Sarafidis, V. (**2022**). A linear estimator for factor-augmented fixed-T panels with endogenous regressors. *Journal of Business & Economic Statistics*, 40(1), 1–15. [Link](https://doi.org/10.1080/07350015.2020.1766469), [PDF](http://sci-hub.ren/10.1080/07350015.2020.1766469), [PDF-wp](https://www.monash.edu/business/ebs/our-research/publications/ebs/wp05-2020.pdf), [Google](https://scholar.google.com/scholar?q=A+linear+estimator+for+factor-augmented+fixed-T+panels+with+endogenous+regressors+Juodis+Sarafidis+2022). Stata 命令: `xtgmmfa`

## 简介

`xtgmmfa` performs GMM estimation for fixed-T panel data model with multifactor structural errors and the presence of endogenous variables. The underlying methodology involves approximating the unobserved common factors using observed factor proxies. The resulting moment conditions are linear in the parameters. See [Juodis](https://doi.org/10.1080/07350015.2020.1766469) and Sarafidis ([2020](http://sci-hub.ren/10.1080/07350015.2020.1766469)) for more details.

## Stata 实操

### 安装 xtgmmfa

```stata
ssc install xtgmmfa, all replace 

help xtgmmfa
```

执行上述命令后，Stata 在安装 `xtgmmfa` 命令的同时，会自动将作者提供的范例数据 **** 下载到当前工作路径下。

```stata
use "js22data.dta", clear
```

### 语法格式

```stata
xtgmmfa depvar [indepvars] [if] [in] [, options]
```


### 实例

```stata
// Example 1: Water data (Joudis and Sarafidis, 2022)
  use "js22data.dta", clear

* Regularization: column M_F of table 1 in Joudis and Sarafidis (2022)
  xtgmmfa l(0/1).lcons price rain temp, ///
    gmm(l.lcons l.price rain temp)      ///
    unof(smi, type(1)) nocons wmat(1)

* Best-Subset Selection: column M1_c of table 1 in Joudis and Sarafidis (2022)
  xtgmmfa l(0/1).lcons price rain temp, ///
    gmm(l.lcons l.price rain temp)      ///
    unof(smi, bss type(1) lmax(4)) nocons wmat(1)

// Example 2: Arellano-Bond data
  use "abdata.dta", clear

* One-step GMM with robust standard errors
  xtgmmfa l(0/1).n k w ys, gmm(l.n l.k l.w l.ys) one r

* Two-step GMM with Windmeijer-corrected robust standard errors
  xtgmmfa l(0/1).n k w ys, gmm(l.n l.k l.w l.ys) r
```

## 参考文献

- Arellano, M., & Bond, S. R. (**1991**). Some tests of specification for panel data: Monte Carlo evidence and an application to employment equations. *The Review of Economic Studies*, 58(2), 277–297. [Link](https://doi.org/10.2307/2297968), [PDF](http://sci-hub.ren/10.2307/2297968), [Google](https://scholar.google.com/scholar?q=Some+tests+of+specification+for+panel+data:+Monte+Carlo+evidence+and+an+application+to+employment+equations+Arellano+Bond+1991).
- Juodis, A., & Sarafidis, V. (**2022**). A linear estimator for factor-augmented fixed-T panels with endogenous regressors. *Journal of Business & Economic Statistics*, 40(1), 1–15. [Link](https://doi.org/10.1080/07350015.2020.1766469), [PDF](http://sci-hub.ren/10.1080/07350015.2020.1766469), [PDF-wp](https://www.monash.edu/business/ebs/our-research/publications/ebs/wp05-2020.pdf), [Google](https://scholar.google.com/scholar?q=A+linear+estimator+for+factor-augmented+fixed-T+panels+with+endogenous+regressors+Juodis+Sarafidis+2022).
- Roodman, D. (**2009**). A note on the theme of too many instruments. *Oxford Bulletin of Economics and Statistics*, 71(1), 135–158. [Link](https://doi.org/10.1111/j.1468-0084.2008.00542.x), [PDF](http://sci-hub.ren/10.1111/j.1468-0084.2008.00542.x), [Google](https://scholar.google.com/scholar?q=A+note+on+the+theme+of+too+many+instruments+Roodman+2009).
- Windmeijer, F. (**2005**). A finite sample correction for the variance of linear efficient two-step GMM estimators. *Journal of Econometrics*, 126(1), 25–51. [Link](https://doi.org/10.1016/j.jeconom.2004.02.005), [PDF](http://sci-hub.ren/10.1016/j.jeconom.2004.02.005), [Google](https://scholar.google.com/scholar?q=A+finite+sample+correction+for+the+variance+of+linear+efficient+two-step+GMM+estimators+Windmeijer+2005).
