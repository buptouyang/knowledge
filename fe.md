###浏览器network查看
####http://www.alloyteam.com/2015/05/wang-ye-xing-neng-zhi-html-css-javascript/#prettyPhoto
######蓝色竖线所处的时间为domComplete时间，红色竖线为dom的onload时间。由此可见两种事件的差异。而浏览器构建dom树所花费的时间可以算出即domComplete时间 减去 html下载完成后的时间
######图中红框内的这部分时间则表示浏览器从下载完成html之后开始构建dom，当发现一个image标签时所花费的时间，由此可见dom是顺序执行的，当发现image时便立即发起请求，而紫色透明条则是image发起请求时在网络传输时所消耗的时间。
***
###bind
####http://biancheng.dnbcw.info/javascript/463870.html
######bind与call很相似，例如，可接受的参数都分为两部分，且第一个参数都是作为执行时函数上下文中的this的对象。不同点有两个：
#####①bind返回值是函数

	//都是将obj作为上下文的this
    function func(name,id) {
        console.log(name,id,this);
    }
    var obj = "Look here";

    //什么也不加
    func("    ","-->");

    //使用bind是 返回改变上下文this后的函数
    var a = func.bind(obj, "bind", "-->");
    a();
    //使用call是 改变上下文this并执行函数
    var b = func.call(obj, "call", "-->");
######结果：
   -->window
bind -->string
call -->string
#####②后面的参数的使用也有区别
	function f(a,b,c){
    	console.log(a,b,c);
	}

    var f_Extend = f.bind(null,"extend_A")

    f("A","B","C")  //这里会输出--> A B C

    f_Extend("A","B","C")  //这里会输出--> extend_A A B

    f_Extend("B","C")  //这里会输出--> extend_A B C

    f.call(null,"extend_A") //这里会输出--> extend_A undefined undefined
######这个区别不是很好理解。call 是 把第二个及以后的参数作为f方法的实参传进去而bind 虽说也是获取第二个及以后的参数用于之后方法的执行，但是f_Extend中传入的实参则是在bind中传入参数的基础上往后排的。
	//这句代码相当于以下的操作
    var f_Extend = f.bind(null,"extend_A")

    //↓↓↓

    var f_Extend = function(b,c){
        return f.call(null,"extend_A",b,c);
    }
#####举一个应用场景：例如现在有一个方法 根据不同的文件类型进行相应的处理，通过bind 就可以创建出简化版的处理方法
	function FileDealFunc(type,url,callback){
        if(type=="txt"){...}
        else if(type=="xml"){...}
        .....
    }
    var TxtDealFunc = FileDealFunc.bind(this,"txt");

    //这样使用的时候更方便一些
    FileDealFunc("txt",XXURL,func);  //原来
    TxtDealFunc(XXURL,func); //现在
######以下是兼容bind处理，在EcmaScript5中扩展bind的方法（IE6,7,8不支持）
	if (!Function.prototype.bind) {
        Function.prototype.bind = function(obj) {
            var _self = this
                ,args = arguments;
            return function() {
                _self.apply(obj, Array.prototype.slice.call(args, 1));
            }
        }
    }
***
###js异步加载
####http://www.cnblogs.com/tiwlin/archive/2011/12/26/2302554.html
>异步加载（async loading），延迟加载（lazy loading），延迟执行（lazy execution）

####1.同步加载
######我们平时最常使用的就是这种同步加载形式：
	<script src="http://yourdomain.com/script.js"></script> 
######同步模式，又称阻塞模式，会阻止浏览器的后续处理，停止了后续的解析，因此停止了后续的文件加载（如图像）、渲染、代码执行。 js 之所以要同步执行，是因为 js 中可能有输出 document 内容、修改dom、重定向等行为，所以默认同步执行才是安全的。以前的一般建议是把 < script > 放在页面末尾</ body >之前，这样尽可能减少这种阻塞行为，而先让页面展示出来。简单说：加载的网络 timeline 是瀑布模型，而异步加载的 timeline 是并发模型。
####2.常见异步加载（script dom element）
	(function() {
         var s = document.createElement('script');
         s.type = 'text/javascript';
         s.async = true;
         s.src = 'http://yourdomain.com/script.js';
         var x = document.getElementsByTagName('script')[0];
         x.parentNode.insertBefore(s, x);
     })();
######异步加载又叫非阻塞，浏览器在下载执行 js 同时，还会继续进行后续页面的处理。这种方法是在页面中< script >标签内，用js创建一个 script 元素并插入到 document中。这样就做到了非阻塞的下载 js 代码。 
######async属性是HTML5中新增的异步支持，见后文解释，加上好（不加也不影响）。此方法被称为 Script DOM Element 法，不要求 js 同源。将js代码包裹在匿名函数中并立即执行的方式是为了保护变量名泄露到外部可见，这是很常见的方式尤其是在 js 库中被普遍使用。例如 Google Analytics 和 Google+ Badge 都使用了这种异步加载代码：
	(function() {
         var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
         ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
         var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);
     })();

    (function()
        {var po = document.createElement("script");
        po.type = "text/javascript"; po.async = true;po.src = "https://apis.google.com/js/plusone.js";
        var s = document.getElementsByTagName("script")[0];
        s.parentNode.insertBefore(po, s);
     })();
######但是，*这种加载方式在加载执行完之前会阻止 onload 事件的触发*，而现在很多页面的代码都在 onload 时还要执行额外的渲染工作等，所以还是会阻塞部分页面的初始化处理。
####3. onload 时的异步加载
######这和前面的方式差不多，但关键是它不是立即开始异步加载 js ，而是在 onload 时才开始异步加载。这样就解决了阻塞 onload 事件触发的问题。
	(function() {
     function async_load(){
         var s = document.createElement('script');
         s.type = 'text/javascript';
         s.async = true;
         s.src = 'http://yourdomain.com/script.js';
         var x = document.getElementsByTagName('script')[0];
         x.parentNode.insertBefore(s, x);
     }
     if (window.attachEvent)
         window.attachEvent('onload', async_load);
     else
         window.addEventListener('load', async_load, false);
	})();
####DOMContentLoaded 与 OnLoad 事件
######DOMContentLoaded : 页面(document)已经解析完成，页面中的dom元素已经可用。但是页面中引用的图片、subframe可能还没有加载完。
######OnLoad：页面的所有资源都加载完毕（包括图片）。浏览器的载入进度在这时才停止。这两个时间点将页面加载的timeline分成了三个阶段。
###4.异步加载的其它方法
######Javascript的动态特性，还有很多异步加载方法：
+ XHR Eval 
+ XHR Injection
+ Script in Iframe
+ Script Defer
+ document.write Script Tag
+ 还有一种方法是用 setTimeout 延迟0秒 与 其它方法组合。

#####XHR Eval ：通过 ajax 获取js的内容，然后 eval 执行。
    var xhrObj = getXHRObject(); 
     xhrObj.onreadystatechange =  
       function() {  
         if ( xhrObj.readyState != 4 ) return; 
         eval(xhrObj.responseText); 
       }; 
     xhrObj.open('GET', 'A.js', true); 
     xhrObj.send('');
 
#####Script in Iframe：创建并插入一个iframe元素，让其异步执行 js 。
    var iframe = document.createElement('iframe'); 
     document.body.appendChild(iframe); 
     var doc = iframe.contentWindow.document; 
     doc.open().write('<body onload="insertJS()">'); 
     doc.close();
 
#####GMail Mobile：页内 js 的内容被注释，所以不会执行，然后在需要的时候，获取script元素中 text 内容，去掉注释后 eval 执行。
    <script type="text/javascript"> 
     /* 
     var ...  
     */ 
     </script>
     
####defer
	<script src="file.js" defer></script> 
######defer属性声明这个脚本中将不会有 document.write 或 dom 修改。
######浏览器将会并行下载 file.js 和其它有 defer 属性的script，而不会阻塞页面后续处理。
#####注：所有的defer 脚本保证是按顺序依次执行的。
###async
	<script src="file.js" async></script>
######async属性是HTML5新增的。作用和defer类似，但是它将在下载后尽快执行
#####不能保证脚本会按顺序执行。它们将在onload 事件之前完成。
#####说明
+ 没有 async 属性，script 将立即获取（下载）并执行，然后才继续后面的处理，这期间阻塞了浏览器的后续处理。
+ 如果有 async 属性，那么 script 将被异步下载并执行，同时浏览器继续后续的处理。
+ HTML4中就有了defer属性，它提示浏览器这个 script 不会产生任何文档元素（没有document.write），因此浏览器会继续后续处理和渲染。
+ 如果没有 async 属性 但是有 defer 属性，那么script 将在页面parse之后执行。
+ 如果同时设置了二者，那么 defer 属性主要是为了让不支持 async 属性的老浏览器按照原来的 defer 方式处理，而不是同步方式。

###document.write
#####http://www.cnblogs.com/dh616854836/articles/2140349.html
>1.页面载入过程中，用脚本加入新的页面内容。
 2.用延时脚本创建本窗口或新窗口的内容。
 该方法需要一个字符串参数，它是写到窗口或框架中的HTML内容。这些字符串参数可以是变量或值为字符串的表达式，写入的内容常常包括HTML标记语言。

######在载入页面后，浏览器输出流自动关闭。在此之后，任何一个对当前页面进行操作的document.write()方法将打开—个新的输出流，它将清除当前页面内容(包括源文档的任何变量或值)。因此，假如希望用脚本生成的HTML替换当前页面，就必须把HTML内容连接起来赋给一个变量，使用一个document.write()方法完成写操作。不必清除文档并打开一个新数据流，一个document.write()调用就可完成所有的操作。
######关于document.write()方法还有一点要说明的是它的相关方法document.close()。脚本向窗口(不管是本窗口或其他窗口)写完内容后，必须关闭输出流。在延时脚本的最后一个document.write()方法后面，必须确保含有document.close()方法，不这样做就不能显示图像和表单。并且，任何后面调用的document.write()方法只会把内容追加到页面后，而不会清除现有内容来写入新值。
###图片懒加载原理
######图片延迟加载的原理比较简单，先将图片的真实地址缓存在一个自定义的属性(lazy-src)中，而src地址使用一个1×1的全透明的占位图片来代替，当然占位图片也可以是其他的图片。
######页面初次加载时获取图片在页面中的位置并缓存(每次取offset的值会引发页面的reflow)，计算出可视区域，当图片的位置出现在可视区域中，将src的值替换成真实的地址，此时图片就开始加载了。
######当页面滚动的时候，再判断图片已经缓存的位置值是否出现在可视区域内，进行替换src加载。当所有的图片都加载完之后，将相应的触发事件卸载，避免重复操作引起的内存泄漏。将整个窗口看成是一个大容器，那么也可以在页面中设置一个小容器，在小容器中也同样可以实现图片的延迟加载。
###网页性能测试
####http://www.alloyteam.com/2015/05/wang-ye-xing-neng-zhi-html-css-javascript/
######domComplete时间必须等待css解析完成，也就是构建dom树必须等待css解析完成
######在引入外部的js文件之后domComplete时间又被延后了，结合renderTree，由于javascript代码可能会更改css属性或者是dom结构，所以在形成renderTree之前必须等待javascript解析完成才能接着构建renderTree。
######将javascript放在head内和body底部的区别也在于此，放在head里面，由于浏览器发现head里面有javascript标签就会暂时停止其他渲染行为，等待javascript下载并执行完成才能接着往下渲染，而这个时候由于在head里面这个时候页面是白的，如果将javascript放在页面底部，renderTree已经完成大部分，所以此时页面有内容呈现，即使遇到javascript阻塞渲染，也不会有白屏出现。
#####当浏览器从服务器接收到了HTML文档，并把HTML在内存中转换成DOM树，在转换的过程中如果发现某个节点(node)上引用了CSS或者 IMAGE，就会再发1个request去请求CSS或image,然后继续执行下面的转换，而不需要等待request的返回，当request返回 后，只需要把返回的内容放入到DOM树中对应的位置就OK。但当引用了JS的时候，浏览器发送1个js request就会一直等待该request的返回。因为浏览器需要1个稳定的DOM树结构，而JS中很有可能有代码直接改变了DOM树结构，浏览器为了防止出现JS修改DOM树，需要重新构建DOM树的情况，所以 就会阻塞其他的下载和呈现.
###target和currentTarget的区别
document

|

|--div

|  |

|  |--span

|  |  |

|  |  |--a

当点击a时，a上绑定的所有事件将会被触发，事件会冒泡到父元素，span上的事件被触发，接着div,document。假设span,div的margin,padding,border都是0,当点击链接时，无论是a,span,div上触发的事件，改事件的target属性永远都是a，因为a是事件的起源。而currentTarget属性将会随着冒泡过程改变，它永远是监听事件的元素。
	```<!DOCTYPE html>
	<html>
	<head>
		<title></title>
		<style>
		</style>
	</head>
	<body>
	<div><span><a href="">哈哈</a></span></div>
	
	</body>
	<script>
	;(function(){
	
	  var anchor = document.querySelectorAll( 'a' )[0],
	      span   = document.querySelectorAll( 'span' )[0],
	      div    = document.querySelectorAll( 'div' )[0];
	  
	  div.addEventListener( 'click', function( e ){
	    
	      console.log( e.target, e.currentTarget ); //anchor, div 
	      e.preventDefault();
	    
	  });
	  
	  span.addEventListener( 'click', function( e ){
	       
	      console.log( e.target, e.currentTarget ); //anchor, span
	      e.preventDefault();
	     
	  });
	  
	  anchor.addEventListener( 'click', function( e ){
	     
	      console.log( e.target, e.currentTarget ); //anchor, anchor
	      e.preventDefault();
	     
	  });
	  
	  document.addEventListener( 'click', function( e ){
	      
	      console.log( e.target, e.currentTarget ); //anchor, document
	      e.preventDefault();
	  });
	
	}()); 
	
	</script>
	</html>```
	
###npm package version 
#####使用npm insall --save 安装模块
#####版本号 MAJOR.MINOR.PATCH

+ #####MAJOR版本：当你的模块发生与API不兼容的改变
+ #####MINOR版本：当你添加后向兼容的功能
+ #####PATCH版本：当你修改后向兼容的bug

#####~号（tilde）匹配中间的数字，例如~1.2.3将会匹配所有1.2.x版本，但是不会匹配1.3.0。
#####^号（caret）匹配第一个数字，例如^1.2.3将会匹配所有1.x.x包括1.3.0,但是不会匹配2.0.0
#####详见[semantic versioning parser](https://github.com/npm/node-semver)
