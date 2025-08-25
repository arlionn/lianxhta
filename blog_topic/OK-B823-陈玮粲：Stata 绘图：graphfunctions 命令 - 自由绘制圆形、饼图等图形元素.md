# B823：Stata 绘图：graphfunctions 命令 - 自由绘制圆形、饼图等图形元素

写一篇推文介绍 `graphfunctions` 命令。

- github: <https://github.com/asjadnaqvi/stata-graphfunctions>，作者在这个仓库里放置了很多绘图实例。可以插入推文中介绍。

除了这些例子之外，建议自己思考一下，这个命令可以和那些命令结合 绘制一些有用的图形。比如，制作课件，数据可视化等。我认为作者自己呈现的例子都没有实际应用价值。比如，如下代码：

```stata
shapes square, len(8) rotate(90) replace

twoway ///
	(area _y _x, nodropbase fcolor(%50))	///
	(scatter _y _x, mlab(_order))	///
		, ///
		legend(off) ///
		xlabel(-10(2)10) ylabel(-10(2)10) xsize(1) ysize(1) aspect(1)
```

![20241106225314](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/20241106225314.png)

可以用来生成 [Stata 绘图：唯美的函数图 - 自定义水平附加线和竖直附加线](https://www.lianxh.cn/details/1064.html) 文中的图 6。

![](https://fig-lianxh.oss-cn-shenzhen.aliyuncs.com/function_addline_06.png)

<br>
<br>

## 简介


## 安装

## Installation

The package can be installed via SSC or GitHub. The GitHub version, *might* be more recent due to bug fixes, feature updates etc, and *may* contain syntax improvements and changes in *default* values. See version numbers below. Eventually the GitHub version is published on SSC.

SSC (**v1.4**):

```Stata 
ssc install graphfunctions, replace
```

GitHub (**v1.5**):

```Stata 
net install graphfunctions, from("https://raw.githubusercontent.com/asjadnaqvi/stata-graphfunctions/main/installation/") replace
```

See the help file `help graphfunctions` for details.

If you want to make a clean figure, then it is advisable to load a clean scheme, especially if you are not using newer Stata versions. My own setting is the following:

```Stata 
ssc install schemepack, replace
set scheme white_tableau  
graph set window fontface "Arial Narrow"
```
