---
layout: post
title: "JavaScript Native Drag and Drop"
---
####设置拖动属性
默认情况下images,links,texts是可以拖动的.Text必须是选中高亮后才能拖动,images和links则没有限制.

HTML5指定任意元素的*draggable*属性为*true*来让其能够被拖动,*draggable*属性支持IE 10+,Firefox 4+,Safari 5+,Chrome.Firefox下初始化时需要进行额外的设置,IE 9及以前的拖动以*mousedown*事件触发**dragDrop()**方法来实现.

{% highlight ruby %}
var self = this;
$("#drag-target").attr("draggable").on("dragstart",function(e){
	//如果需要改变默认拖动图标
	self.createDragImage();
}).on("dragend",function(e){
		//如果改变了默认拖动图标,完成拖动后要清除
		self.clearDragImage();
	});
{% endhighlight %}

####改变拖动时图标
如果需要改变拖动时鼠标光标下的图像,则可以使用**setDragImage()**方法:

{% highlight ruby %}
function createDragImage(){
	var dragImage = document.createElement("div"),template='<img src="" />';
	dragImage.className = "drag-helper"
	dragImage.innerHTML = template;

	//必须把创建的元素添加到DOM节点中
	$("#container").append(dragImage);
	
	//设置拖动时光标显示内容
	e.dataTransfer.setDragImage(dragImage,0,0);
}

function clearDragImage(){
	$(".drag-helper").remove();
}

//隐藏dragImage,但要保证拖动的时候能显示出来
//设置display:none,visibility:hidden,opacity:0都会隐藏dragImage,但拖动时也不会显示
//所以要把dragImage放在可视区中,坐标必须设置在可视区内
//style
.drag-helper,.cover{
	position:absolute;
	top:0;
	left:0;
	width:100px;
	height:20px;
}
	//用同级的元素覆盖dragImage
.drag-helper{
	z-index:0
}
.cover{
	z-index:1
}
{% endhighlight %}

####关于z-index

`z-index的值是相对于同级元素的,应用于设置了position的元素`

{% highlight ruby %}
    <div class="one">
        <p>one</p>
    </div>
    <div class="two">
        <p>two</p>
    </div>
	
	//style
	.one,.two{
		position: absolute;
        top: 50px;
        left: 50px;
        width: 100px;
	}
	.one{
		z-index:5;
	}
	.two{
		z-index:2;
	}
	.one p{
		position: absolute;
		z-index:1;
		background-color:blue
	}
	.two p{
		position: absolute;
		z-index:10;
		background-color:grey;
	}
{% endhighlight %}

可以看到显示的是**div.one**内的p元素,即使**div.two**内的p元素的**z-index**是最大的

####设置拖放区域
默认所有元素不允许拖放,可以通过重写*dragenter*和*dragover*事件让一个元素允许拖放:

{% highlight ruby %}
$("#drop-target").on("dragenter",function(e){
	e.preventDefault();
}).on("dragover",function(e){
		e.preventDefault();
	});
{% endhighlight %}

监听*drop*事件完成拖放后的处理

{% highlight ruby %}
$("#drop-target").on("drop",function(e){
	//TODO
});
{% endhighlight %}