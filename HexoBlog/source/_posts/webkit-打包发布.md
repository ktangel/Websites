title: webkit系列之打包发布
---

经过一整天各种填坑，大晚上的总算是把webkit的web app顺利发布并在XP系统下也跑通了。
现在来梳理一下遇到过的问题，先介绍下环境。

APP的环境及主要功能：

1.  APP版本自动更新：node-webkit-updater插件；安装方法： `npm install node-webkit-builder -g` ；项目地址： `https://github.com/edjafarov/node-webkit-updater`。
2.  主要业务代码在服务器，通过iframe加载入口，本地只有提示音文件和消息提示功能代码。
3.  消息提示：当有新订单时弹消息提示，并同时播放提示音。这个是重点功能，因这个功能而挖坑比较多，我用的是webkit标准化之后的Notification方法，比较新的浏览器都支持，也正因此出现了调试过程一切顺利，打包后一但调用此方法就出现程序崩溃，反复调试后发现是打包插件的版本太低（1.2.1），下载1.2.2和1.2.3重新打包后就正常了。
3.  因需要隐藏地址栏工具栏，于是自己做了个前进、后退、刷新功能的新导航栏。
4.  支持iframe间的跨域传数据，这里用到的是postMessage方法，既然webkit支持很多最新的浏览器特性，咱就不用费工夫去用一些所谓兼容好方法了，postMessage有很大一个好处，它是通过全局"message"事件来把数据传送过去，对于实现功能非常方便，于是我通过对传递信息进行定义转换，实现了跨域子母页面之间互相调用方法并可以传递执行参数。

问题汇总：

*	消息推送：在做它的API方法时，刚开始使用的是第一次握手后缓存对方的e.source(发送方的window全局对象），跨域环境下它是只读的，而且不可被引用，在这里可以把它缓存作后面的postmessage的回传用，在后来发现了这种握手式方法的缺点：换页面或reload刷新环境后需要子页面重新握手初始化API环境；我这里只用了一个iframe，所以后来就不缓存它了，省去握手的问题，父传子可以通过getifrmae DOM对象的contentWindow引用POSTMSG过去，而子传父的话就直接用命top.postMeassage传递。以后有需要再去尝试多级iframe的方法。

```
//消息推送函数
function postmsg(msg, pram, callback) {
		if (!fn.appwin) {
			setappwin();
		}
		var _msg = msg;
		if (typeof msg === "string") {
			var toarr = _msg.indexOf("()") >= 0;
			if (toarr || pram) {
				var msgobj = {};
				msgobj.fn = toarr ? _msg.split('()')[0] : _msg;
				msgobj.type = "fn";
				pram && (msgobj.pram = pram);
				_msg = msgobj;
			}
			if (callback) {
				_msg.callback = true;
				callback.fn = _msg.fn;
				push_callback(callback);
			}
			fn.appwin && fn.appwin.postMessage(_msg, "*");
		}
	}

//
var apprun = {
	back: function() {
		postmsg("hiback") //为了急着解决问题，我自己约定好关键字，执行在frame中判断。；
	},
	forward: function() {
		postmsg("hiforward")
	},
	reload: function() {
		postmsg("reload")
	}
}

初始化
window.onload = function() {
	if (!window.appwin) {
		var appbody = document.getElementById("appbody");
		appbody.contentWindow.postMessage('hi,init', "*"); //初始化握手；
	}
//添加事件处理；
	window.addEventListener("message", function(e) {
		if (e.data === "hi,appinit") {
			!window.appwin && (window.appwin = e.source);
		} else {
			manage_msg(e.data);
		}
		console.log("for appwin msg:", e.data);
	}, false)
}

```

####子iframe使用到的函数####

```

function postmsg(msg, pram, callback) {
	var _msg = msg;
	if (typeof msg === "string") {
		var toarr = _msg.indexOf("()") >= 0;
		if (toarr || pram) {
			var msgobj = {};
			msgobj.fn = toarr ? _msg.split('()')[0] : _msg;
			msgobj.type = "fn";
			pram && (msgobj.pram = pram);
			_msg = msgobj;
		}
		if (callback) {
			_msg.callback = true;
			callback.fn = _msg.fn;
			push_callback(callback);
		}
		top.postMessage(_msg, "*");
		console.log(_msg);
	}
}

	function initpost() {
		top.postMessage('hi,appinit', "*");
	}
//添加对message事件处理；
window.addEventListener("message", function(e) {
	console.log("for parntwin msg:", e.data);
	if (e.data === "hi,init") {
		!window.parentwin && (window.parentwin = e.source);
		postmsg("hi,appinit");
	} else {
		manage_msg(e.data);
	}
}, false)
//由子窗口初始化握手，好告诉父子窗口已经换页。
initpost()
```

####然后是处理msg消息的方法####
```
function manage_msg(msg) {
		if (typeof msg == "object") {
			if (msg.type === "fn") { //执行函数
				var _fn = msg.fn,
					type = $.type(_fn);
				var _pram = msg.pram || null;
				var ptype = $.type(_pram);
				msgfn = _fn;
				msgcallback = msg.callback || null;
				//记录现在调用的fn名，POST callback时带上
				if (type === "string") {
					if (!msgself) { //msgself是对是否可以直接运行其它函数的开关，true时允许直接访问运行全局下的所有函数；false时禁止，只允许运行预设的函数。
						switch (_fn) {
							case "opencef":
								opencef(msg.pram);
								break;
							case "webopenie":
								webopenie(msg.pram);
								break;
						}
					} else {
						_pram ? eval(_fn)[ptype !== 'array' ? 'call' : 'apply'](this, _pram) : eval(_fn).call(this);
					}
				}
			} else if (msg.type === 'data') {
				//这里可以放数据的处理方法，比如只是简单的回调数据
			}
		} else if (typeof msg === "string") {
			if (msg.indexOf("{") >= 0) {
				return JSON.parse(msg)
			} else {
				console.log(msg)
				switch (msg) {
					case "hiback":
						window.history.back();
						break;
					case "hiforward":
						window.history.forward();
						break;
					case "reload":
						window.location.reload()
						break;
					case "notfiy": //测试消息推送是否可用；
						startPlay();
						break;
				}
			};
		}
	}
```

