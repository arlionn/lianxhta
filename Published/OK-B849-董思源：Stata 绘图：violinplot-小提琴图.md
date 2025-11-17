
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
