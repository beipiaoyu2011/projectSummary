#前言
前些时候，一个人一个月做过了一个app项目，刚开始接到任务一脸懵逼！在此之前从未接触过anglar 和 ionic;

由于曾经写过app的H5页面，所以意识到第一个难题就是如何适配琳琅满目的各种机型，后来遍历各种论坛网站，决定研究手机淘宝的flexble适配方案。

研究过适配方案，开始搭建整个框架，利用requireJs 按需加载js，配合gulp打包项目。刚开始不了解ionic，一直开始的时候就用ionic的命令去生成一个cordova的项目，一直配置不成功，终于有一天突然了解到可以把ionic的包引进来当做一个插件用，开发一个基于web h5的app项目。

# 坑的集合

1. [flexble适配](https://github.com/huainanhai/flexible) 

	[![](https://camo.githubusercontent.com/5ac077a46fc12456b71725f70cab904abcffb6e7/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d312e6a7067)](https://github.com/huainanhai/flexible)

	可以参考文档，不再赘述；

2. 减少图片请求，制作sprite 雪碧图

	因为请求过多，导致时间过长；无论是ios还是安卓，都是以webview的方式请求远程的url，当所有的包都被下载后才会出现内容。每增加一个request 必将增加 waiting time;所以要尽可能减少请求；

	**Plan A** 
	
	是可以借助工具[goPng](https://github.com/huainanhai/gopng)，这种方式不好地方，就是无节制的压缩空间，整体看起来很乱没有层理，pc端如果要用也是可以勉强用，手机端几乎是不能用，原因等下详述；

	**Plan B**

	手动通过photoshop精细合成。需要注意的是： 由于UI给的UI图都是@2X放大图，所以切得图也是2倍的；所以最好所有的distace都是偶数，无论是距上还是距左；

	这里额外推荐一个[切图神器](https://github.com/huainanhai/projectSummary/tree/master/tool)，打开photoshop alt+F9打开动作如图：


	[![](http://i.imgur.com/Rswa6Vv.png)](https://github.com/huainanhai/projectSummary/tree/master/tool) 

	把下载好的[**切图神器.atn**](https://github.com/huainanhai/projectSummary/tree/master/tool)拖到动作窗体内即安装成功，成功同如上图。其余操作的自己体会！
	
	关于雪碧图背景定位优选一下方案：
	[移动端适配之雪碧图(sprite)背景图片定位](http://www.jianshu.com/p/d3b19968a4c2)

3. [**angularjs中$http模块POST请求request payload转form data**](http://www.360doc.com/content/15/0521/12/203871_472172841.shtml)
	
	在app.config()里面加上

        $httpProvider.defaults.transformRequest = function(obj) {
            var str = [];
            for (var p in obj) {
                str.push(encodeURIComponent(p) + "=" + encodeURIComponent(obj[p]));
            }
            return str.join("&");
        };
		$httpProvider.defaults.headers.post = {
           'Content-Type': 'application/x-www-form-urlencoded'
        };





	【原坑重现（例子）】运行代码：
    
   		 $http({
      		method:'post',
       		url:'post.php',
       		data:{
    	 		name:"aaa",
    	 		id:1,
    	 		age:20
       		}
   		 }).success(function(req) {
       		console.log(req);
    	});


	运行结果（chrome→network）：

	![](http://image85.360doc.com/DownloadImg/2015/05/2112/53806604_1.jpg)

	这样的话在后端中用是取不到数据的。

	解决方案：

	a.配置$httpProvider：

			var myApp = angular.module('app',[]);  
 			myApp.config(function($httpProvider){  
     			$httpProvider.defaults.transformRequest = function(obj){  
    				var str = [];  
     				for(var p in obj){  
       					str.push(encodeURIComponent(p) + "=" + encodeURIComponent(obj[p]));  
     				}  
    			 return str.join("&");  
   				};  
     			$httpProvider.defaults.headers.post = {  
        			'Content-Type': 'application/x-www-form-urlencoded'  
   				}; 
  			});  

	b.在post中配置:

			$http({  
  				 method:'post',  
  				 url:'post.php',  
  				 data:{name:"aaa",id:1,age:20},  
  				 headers:{'Content-Type': 'application/x-www-form-urlencoded'},  
   				 transformRequest: function(obj) {  
    				 var str = [];  
    				 for(var p in obj){  
       					str.push(encodeURIComponent(p) + "=" + encodeURIComponent(obj[p]));  
     				 }  
    				 return str.join("&");  
  				 }  
				 }).success(function(req){  
      				 console.log(req);  
				 })  


	运行结果（chrome→network）:

	![](http://image85.360doc.com/DownloadImg/2015/05/2112/53806604_2.jpg)

	至此成功换成form data;

4. [angularjs中错误总结](http://www.jianshu.com/p/85220470cbfe)

5. 路由数据之间通信

	除了通过stateParam 路由间传递（缺点是只是单向路由一次有效，再刷新浏览器参数不复存在）。还有 [Web Storage：浏览器端数据储存机制](http://javascript.ruanyifeng.com/bom/webstorage.html)
	
6. [ionic中定义路由的问题（嵌套路由）](http://www.cnblogs.com/gdhydx-ljm/p/5347852.html)
	
7. [Ionic 常见问题及解决方案 ](http://blog.csdn.net/molong421/article/details/51587853)
	
8. [angularjs学习总结 详细教程](http://blog.csdn.net/yy374864125/article/details/41349417) 

9.	[如果滚动区域不会回滚：](https://zhidao.baidu.com/question/458435795.html?fr=iks&word=%3B-webkit-overflow-scrolling%3A+touch&ie=gbk)
	
	[WebKit私有的属性“-webkit-overflow-scrolling: touch”（允许独立的滚动区域和触摸回弹）；](https://zhidao.baidu.com/question/458435795.html?fr=iks&word=%3B-webkit-overflow-scrolling%3A+touch&ie=gbk) 


		





