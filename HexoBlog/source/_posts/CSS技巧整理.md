title: 常用方法
---

##<a name="index">目录索引</a>
1. [绝对居中自适应样式](#abscenter)
2. [对象深复制](#deepClone)
3. [返回字符串数组中重复最多的字符](#returnMaxNum)
2. [判断是否在iframe中](#hasiframe)   
3. [iCheke小插件的值获取和设置](#icheckset)
4. [对过多的输入元素在页面隐藏](#morecon)
5. [提示标签样式](#placetip)
6. [字母列表索引](#index-a-z)


####[[ ]](#top) <a name="abscenter">[绝对居中自适应样式]</a>

第一种方法，兼容性强

效果：

<style>
.alert-bg {
	display: table;
	text-align: center;
	height: 400px;
	width: 400px;
	background-color: rgba(0, 0, 0, .7);
}
.alert-cell {
	display: table-cell;
	vertical-align: middle;
}
.alert-cell .alert-center {
	border-radius: 15px;
	background: #F4F4F4;
	position: relative;
	max-width: 80%;
	min-width: 50%;
	margin: 0 auto;
}
.abs-both {
	position: absolute;
	left: 0;
	right: 0;
	top: 0;
	bottom: 0;
	width: 100%;
	height: 100%;
}
.flexbox {
	display: -moz-box;
	display: -ms-flexbox;
	display: -webkit-flex;
	display: -webkit-box;
	display: flex
}
.center {
	-webkit-flex-pack: center;
	-moz-flex-pack: center;
	-ms-flex-pack: center;
	-webkit-box-pack: center;
	-moz-box-pack: center;
	-ms-box-pack: center;
	justify-content: center
}
.middle {
	-webkit-flex-align: center;
	-moz-flex-align: center;
	-ms-flex-align: center;
	-webkit-box-align: center;
	-moz-box-align: center;
	-ms-box-align: center;
	align-items: center
}
.content{
background-color:#aaa;
text-align:center;
}
.boss{
background-color:red;
width:100px;
height:100px;
margin:auto;
}
.mask{
position:relative;
width:100%;
height:300px;
}
</style>
	<div class="alert-bg" style="width='200px' height='200px'">
	<div class="alert-cell">
		<div class="alert-center">
			需要居中控制的主体；
		</div>
	</div>
	</div>


HTML:

  ```

	<div class="alert-bg">
	<div class="alert-cell">
		<div class="alert-center">
		需要居中控制的主体；
		</div>
	</div>
	</div>

  ```
CSS:
----

```

	.alert-bg {
	display: table;
	text-align: center;
	position: absolute;
	z-index: 1400;
	height: 100%;
	width: 100%;
	top: 0;
	left: 0;
	bottom: 0;
	right: 0;
	background-color: rgba(0, 0, 0, .7);
	}
	.alert-cell {
	display: table-cell;
	vertical-align: middle;
	}
	.alert-cell .alert-center {
	border-radius: 15px;
	background: #F4F4F4;
	position: relative;
	max-width: 80%;
	min-width: 50%;
	margin: 0 auto;
	}

```

方法二：利用flexbox属性控制

效果：

<div class="mask">
<div class="flexbox middle center content abs-both">
<div class="boss">内容</div>
</div>
</div>

HTML:
```

	<div class="mask">
	<div class="flexbox middle center content abs-both">
	<div class="boss">内容</div>
	</div>
	</div>

```

CSS:

<pre>
<code>
.flexbox {
	display: -moz-box;
	display: -ms-flexbox;
	display: -webkit-flex;
	display: -webkit-box;
	display: flex
}
.center {
	-webkit-flex-pack: center;
	-moz-flex-pack: center;
	-ms-flex-pack: center;
	-webkit-box-pack: center;
	-moz-box-pack: center;
	-ms-box-pack: center;
	justify-content: center
}
.middle {
	-webkit-flex-align: center;
	-moz-flex-align: center;
	-ms-flex-align: center;
	-webkit-box-align: center;
	-moz-box-align: center;
	-ms-box-align: center;
	align-items: center
}
.content{
background-color:#aaa;
text-align:center;
}
.boss{
background-color:red;
width:100px;
height:100px;
margin:auto;
}
.mask{
position:relative;
width:100%;
height:300px;
}
</code>
</pre>

