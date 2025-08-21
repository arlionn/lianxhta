# B850：金融圈行话暗语大盘点

收集网上的资料，写篇推文。最好能有点趣味，但又不缺少知识性。比如，如下资料可以作为来源的一部分：
- [拒做金融小白，一些“行话”小科普（上）](https://zhuanlan.zhihu.com/p/103692672)
- [金融风控“行话”，一文掌握金融风控专业术语](https://zhuanlan.zhihu.com/p/347740212)
- [金融行业的暗语有哪些？](https://www.zhihu.com/question/65374298)
- [常在金融圈混，不懂点“黑话”怎么行——金融行话盘点](https://zhuanlan.zhihu.com/p/136020595)
- [一审、二审、三审、枪毙](https://www.zhihu.com/question/19635215/answer/135213405)
- [你所知道的行业黑话有哪些？ - 财柒的回答 - 知乎](https://www.zhihu.com/question/19635215/answer/173503200)
- [盘点：科研派“黑话”](https://zhuanlan.zhihu.com/p/64097739)
[有哪些科研圈的通用行话？](https://www.zhihu.com/question/386652986)



# B849-Stata 绘图：violinplot-小提琴图

```stata
net describe http://repec.org/bocode/v/violinplot
ssc install violinplot, replace all 

* violinplot requires dstat, moremata, palettes, and colrspace. 
* To install these packages, type:

  ssc install dstat
  ssc install moremata
  ssc install palettes
  ssc install colrspace
```

作者还提供了一个 zip 文件，执行 `ssc install violinplot, replace all ` 命令后会自动下载到当前工作路径下。可以作为推文的素材。
