title: CSS实现流程示意图
---


最低可兼容IE7。
Html标签：

```
<div class="flow-plan clearfix">
<ul class="plan-list">
	<div class="Tri-r"></div>
	<li class="start fin">
		<div class="Tri-bk">
			<div class="Tri-bk-r"><span></span></div>
		</div>
		<span class="setp">✓</span>查看购物车</li>
	<li class="active fin">
		<div class="Tri-bk">
			<div class="Tri-bk-l"><span></span></div>
			<div class="Tri-bk-r"><span></span></div>
		</div><span class="setp">2</span><span>确认订单</span></li>
	<li class="end">
		<div class="Tri-bk">
			<div class="Tri-bk-l"><span></span></div>
			<div class="Tri-bk-r"><span></span></div>
		</div>
		<span class="setp">3</span><span>成功提交订单</span></li>
</ul>
</div>
```

CSS样式：利用border边框绘制三角形

```
.flow-plan {
	float: right;
	margin-top: 10px;
	margin-right: 4px;
}
.plan-list{
	display: block;
	position: relative;
}
.plan-list>li {
	position: relative;
	display: block;
	float: left;
	font-size: 14px;
	height: 32px;
	padding: 0 20px;
	line-height: 32px;
	z-index: 10;
	margin-left: 24px;
}
.Tri-bk {
	position: absolute;
	top: 0;
	left: 0;
	right: 0;
	z-index: -1;
	width: 100%;
	height: 32px;
	background-color: #f0f0f0;
}
.Tri-bk>.Tri-bk-l,
.Tri-bk>.Tri-bk-r {
	position: absolute;
	height: 100%;
}
.Tri-bk>.Tri-bk-r {
	right: 0;
}
.Tri-bk>.Tri-bk-l>span,
.Tri-bk>.Tri-bk-r>span {
	position: absolute;
	display: block;
	padding: 0;
	height: 0;
	width: 0;
	border-width: 16px;
	border-style: solid;
	line-height: 0px;
}
.Tri-bk>.Tri-bk-l>span {
	left: -16px;
	border-color: #f0f0f0 #f0f0f0 #f0f0f0 transparent;
}
.Tri-bk>.Tri-bk-r>span {
	right: -32px;
	border-color: transparent transparent transparent #f0f0f0;
}
.active>.Tri-bk>.Tri-bk-l>span {
	border-color: #8fbd20 #8fbd20 #8fbd20 transparent;
}
.active>.Tri-bk>.Tri-bk-r>span {
	border-color: transparent transparent transparent #8fbd20;
}
.active>.Tri-bk {
	background-color: #8fbd20;
}
.plan-list>li>span.setp {
	font-family: arial;
	color: #007749;
	padding: 2px 6px;
	margin-right: 10px;
	background-color: white;
	border: 1px solid transparent;
	border-radius: 50px;
	-moz-border-radius: 50px;
}
```
效果：
![](/blog/img/note/flow.png)