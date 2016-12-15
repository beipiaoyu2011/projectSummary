#前言
前些时候，一个人一个月做过了一个app项目，刚开始接到任务一脸懵逼！在此之前从未接触过anglar 和 ionic;

由于曾经写过app的H5页面，所以意识到第一个难题就是如何适配琳琅满目的各种机型，后来遍历各种论坛网站，决定研究手机淘宝的flexble适配方案。

研究过适配方案，开始搭建整个框架，利用requireJs 按需加载js，配合gulp打包项目。刚开始不了解ionic，一直开始的时候就用ionic的命令去生成一个cordova的项目，一直配置不成功，终于有一天突然了解到可以把ionic的包引进来当做一个插件用，开发一个基于web h5的app项目。

# 坑的集合

1. [flexble适配](https://github.com/huainanhai/flexible) 

	可以参考文档，不再赘述；

2. 减少图片请求，制作spite 雪碧图

	因为请求过多，导致时间过长；无论是ios还是安卓，都是以webview的方式请求远程的url，当所有的包都被下载后才会出现内容。每增加一个request 必将增加 waiting time;所以要尽可能减少请求；

	**Plan A** 
	
	是可以借助工具[goPng](https://github.com/huainanhai/gopng)，这种方式不好地方，就是无节制的压缩空间，整体看起来很乱没有层理，pc端如果要用也是可以勉强用，手机端几乎是不能用，原因等下详述；

	**Plan B**

	手动通过photoshop精细合成。需要注意的是： 由于UI给的UI图都是@2X放大图，所以切得图也是2倍的；所以最好所有的diatace都是偶数，无论是距上还是距左；

	这里额外推荐一个切图神器

	





