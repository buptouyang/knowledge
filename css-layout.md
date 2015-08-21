##等高列布局
- ####假等高列
> #####使用背景图片，在列的父元素上使用这个背景图进行Y轴的铺放，从而实现等高列的假象

#####优点：
######实现方法简单，兼容性强，不需要太多的css样式就可以轻松实现。
#####缺点：
######使用这种方法不适合流体布局等高列的布局，另外如果你需要更换背景色或实现其他列数的等高列时，都需要重新制作过背景图。
*********
- #####给容器div使用单独的背景色（固定布局）
> ##### 主要给每一列的背景设在单独的< div >元素上。这种方法的实现的原则是：任何< div >元素的最大高度来撑大其他的< div >容器高度。

######html markup
	<div class="container">
    	<div class="rightWrap">
        	<div class="contentWrap">
                <div class="leftWrap">
                    <div class="aside column leftSidebar" id="left"></div>
                    <div id="content" class="column section"></div>
                    <div class="aside rightSidebat column" id="right"></div>
                </div>
            </div>
        </div>
    </div>
######css code
    <style type="text/css">
        .container {
            width: 960px;
            margin: 0 auto;
        }
        .rightWrap {
            width: 100%;
            float: left;
            background: green;
            overflow: hidden;
            position: relative;
        }

        .contentWrap {
            float: left;
            background: orange;
            width: 100%;
            position: relative;
            right: 320px;/*此值等于rightSidebar的宽度*/
        }

        .leftWrap{
            width: 100%;
            background: lime;
            float:left;
            position: relative;
            right: 420px;/*此值等于Content的宽度*/
        }
        #left {
            float: left;
            width: 220px;
            overflow: hidden;
            position: relative;
            left: 740px;
        }
        #content {
            float: left;
            width: 420px;
            overflow: hidden;
            position:relative;
            left: 740px;
        }
        #right {
            float: left;
            overflow: hidden;
            width: 320px;
            background: #333;
            position: relative;
            left: 740px;
        }
    </style>
    
###position属性
- ####relative

>#####relative称为相对定位，如果你给某个元素指定了postion的值为“relative”，那么你就可以通过“T-R-B-L”(也就是top,right,bottom,left)来设置元素的定位值。

####注意
- ##### 元素设置了relative时，是相对于元素本身位置进行定位；
- ##### 元素设置了relative后，可以通过“T-R-B-L”改变元素当前所在的位置，但元素移位后，同样点有当初的物理空间位；
- ##### 元素设置了relative后，如果没有进行任何的“T-R-B-L”设置，元素不会进行任何位置改变。
- ##### 没有脱离文档流，保留原来的物理空间，仍占据原始位置

- ####absolute浮动
>#####absolute是position中的第三个属性值，如果你给元素指定了absolute，整个元素就会漂出文档流，而且元素自身的物理空间也同时消失了。不像“relative”还具有原先的物理空间。

####注意
- ##### 如果一个元素绝对定位后，其参照物是以离自身最近元素是否设置了相对定位，如果有设置将以离自己最近元素定位，如果没有将往其祖先元素寻找相对定位元素，一直找到html为止。
- ##### 相对于最近的“positioned”祖先元素。如果绝对定位（position属性的值为absolute）的元素没有“positioned”祖先元素，那么它是相对于文档的 body 元素，并且它会随着页面滚动而移动。记住一个“positioned”元素是指position 值不是 static 的元素。

- ####absolute浮动
>#####absolute是position中的第三个属性值，如果你给元素指定了absolute，整个元素就会漂出文档流，而且元素自身的物理空间也同时消失了。不像“relative”还具有原先的物理空间。

###盒模型
#####页面被看做一个矩形框，这个框由元素的内容，内边距padding，边框border和外边距margin组成。如果在元素上添加背景，那么背景会应用于内容和内边距组成的区域
#####css2.1还包含outline属性，与border不同，轮廓绘制在元素框之上，所以它们不影响元素的大小或定位。因此轮廓有助于修复bug，因为他们不影响页面的布局
####width和height指的是内容区域的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加**元素框**的总尺寸
###IE非标准盒模型，包括IE6
####width不是内容的宽度，而是内容、内边距和边框的宽度之和，CSS3的box-sizing属性可以定义使用哪种盒模型
###外边距叠加
#####当两个或更多垂直外边距相遇时，它们形成一个外边距，这个外边距等于两个发生叠加的外边距的高度重的较大者
####注意：只有在普通文档流中块框的垂直外边距才会发生外边距叠加。行内框、浮动框或绝对定位框之间的外边距不会叠加。
###三种定位机制：普通流、浮动和绝对定位
###匿名块框
>####将一些文本添加到一个块级元素（比如div）的开头时，即使没有把这些文本定义为块级元素，它也会被当成块级元素对待

	<div>
        some text
        <p>som more</p>
    </div>
