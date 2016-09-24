关于TWX的分享
===

>### 分享内容概览
>
>1. [TWX整体目录结构](#p1)
>1. [单个页面组成部分](#p2)（lua模板+js）
>1. [flux在twx里的应用](https://worried-k.github.io/qunit/code/dataStore.html)（数据驱动模型与dataStore）
>1. [关于js的doc文档](https://worried-k.github.io/twxDoc/)
>1. [setTimeout和setInterval与js的事件驱动机制](https://worried-k.github.io/qunit/code/delay.html)（js与浏览器进程间的交互）
>1. [ajax的异常处理，与递归调用](#p6)
>1. [对js的函数和对象的理解](#p1)（函数是js的精华）
>1. [如何用js函数去创建一个对象，并为此函数扩展属性](#p1)
>1. [js闭包的应用](#p1)
>1. [慎用js中的关键字 this](#p1)
>1. [js将匿名函数作为参数传递](#p1)
>1. [js的引用传递，避免与利用](#p1)
>1. [script标签在html中的位置与原因](#p1)
>1. [js的局部函数化、模块化](#p1)
>1. [关于js的跨域（服务端添加response的http首部来避免跨域）](#p1)
>1. [关于js中的假值，所引起的异常判断](#p1)

### 1. <span id="p1">TWX的目录结构</span>
<pre><code>
twx
└── src
    ├── etc
    │   ├── agreement ---首次安装的状态位配置文件
    │   ├── init.d ---子启动脚本文件夹
    │   │   ├── build_i18n ---生成js的i18n文件
    │   │   └── build_loginpage ---生成静态登陆页
    ├── usr
    │   └── lib
    │       └── lua
    │           └── luci
    │               ├── dispatcher.lua ---luci的调度函数文件
    │               ├── util.lua
    │               ├── version.lua ---版本配置文件，页面引用静态文件时用到了它里面的值。如：？v=<%=ver.svnRevNum%>
    │               ├── controller ---twx页面与twx自身api的调度与实现
    │               │   ├── admin_mobile
    │               │   │   └── index.lua
    │               │   ├── admin_web
    │               │   │   ├── guide.lua
    │               │   │   ├── index.lua
    │               │   │   └── login.lua
    │               │   ├── admin_wp
    │               │   │   └── index.lua
    │               │   ├── api
    │               │   │   ├── index.lua
    │               │   │   └── ...
    │               │   └── guide_v2
    │               │       ├── guide.lua
    │               │       └── index.lua
    │               ├── ...
    │               ├── i18n ---i18n语言包
    │               │   ├── eng.lua
    │               │   ├── oem.lua
    │               │   └── zh_cn.lua
    │               ├── i18n.lua ---i18n对外函数功能实现
    │               ├── local_ssh ---本地ssh的功能实现
    │               │   ├── local_ssh.lua
    │               │   └── local_ssh_util.lua
    │               └── view
    │                   ├── admin_mobile ---移动端页面的html模板文件
    │                   │   ├── footer.htm ---html公共尾文件
    │                   │   ├── header.htm ---html公共头文件
    │                   │   ├── home.htm
    │                   │   ├── sysauth.htm
    │                   │   ├── network
    │                   │   │   └── ...
    │                   │   ├── system
    │                   │   │   └── ...
    │                   │   └── wifi
    │                   │       ├── index.htm
    │                   │       └── wifi_setup.htm
    │                   ├── admin_web ---pc端页面的html模板文件
    │                   │   ├── footer.htm ---html公共尾文件
    │                   │   ├── header.htm ---html公共头文件
    │                   │   ├── home.htm
    │                   │   ├── ap ---路由器为ap时的网络诊断页面
    │                   │   │   ├── diagnose.htm
    │                   │   │   └── error.htm
    │                   │   ├── menu
    │                   │   │   └── menu_left.htm ---pc端页面的左侧菜单
    │                   │   ├── network
    │                   │   │   ├── index.htm ---文件夹中的其他页面被include到此页面
    │                   │   │   └── ...
    │                   │   ├── passport
    │                   │   │   ├── apps.htm
    │                   │   │   └── guarantee.htm
    │                   │   ├── plugin
    │                   │   │   ├── hua_san.htm
    │                   │   │   ├── index.htm
    │                   │   │   ├── logs.htm
    │                   │   │   └── rui_jie.htm
    │                   │   ├── safe
    │                   │   │   ├── black_list.htm
    │                   │   │   ├── index.htm ---文件夹中的其他页面被include到此页面
    │                   │   │   └── white_list.htm
    │                   │   ├── storage
    │                   │   │   ├── index.htm
    │                   │   │   └── samba.htm
    │                   │   ├── system
    │                   │   │   ├── index.htm ---文件夹中的其他页面被include到此页面
    │                   │   │   └── ...
    │                   │   └── wifi
    │                   │       ├── index.htm ---文件夹中的其他页面被include到此页面
    │                   │       └── ...
    │                   └── guide_v2 ---首次安装页面的html模板文件
    │                       ├── agreement.htm ---系统中唯一的路由器使用协议
    │                       ├── footer.htm ---html公共尾文件
    │                       ├── header.htm ---html公共头文件
    │                       ├── index.htm ---首次安装入口
    │                       ├── first.htm ---首次安装入口
    │                       └── ...
    └── www
        ├── index.html ---twx入口文件
        ├── favicon.ico
        ├── 404.html
        ├── 500.html
        ├── local_ssh.html ---本地ssh入口文件
        ├── login_mobile_backup.html
        ├── login_mobile.html --静态登陆文件
        ├── login_web_backup.html
        ├── login_web.html --静态登陆文件
        ├── net_detect.html --网络诊断入口文件
        ├── robots.txt ---组织twx页面被爬虫抓取
        ├── cgi-bin
        │   ├── proxy-cgi ---twx调用openapi时的代理cgi
        │   └── turbo
        ├── demo
        │   └── ...
        └── turbo-static
            └── turbo
                └── v2
                    ├── img
                    │   ├── 2.4g@2x.png ---pc端图片
                    │   ├── ...
                    │   ├── admin_mobile ---移动端图片
                    │   │   ├── ad-close.png
                    │   │   ├── ...
                    │   ├── guide ---首次安装图片
                    │   │   ├── an.png
                    │   │   ├── ...
                    │   ├── net_detect_mobile
                    │   │   ├── arrow-back.png
                    │   │   ├── ...
                    │   ├── net_detect_web
                    │   │   ├── arrow.png
                    │   │   ├── ...
                    ├── js
                    │   ├── g.js ---提供全局对象HiWiFi
                    │   ├── onerror.js ---js错误捕获，置前加载
                    │   ├── admin_mobile
                    │   │   ├── general_module.js ---通用页面的逻辑处理
                    │   │   ├── g.js ---独属于移动端模块的公共函数，HiWiFi对象的扩展
                    │   │   ├── home.js
                    │   │   └── ...
                    │   ├── admin_web
                    │   │   ├── general_module.js ---通用页面的逻辑处理
                    │   │   ├── g.js ---独属于web端模块的公共函数，HiWiFi对象的扩展
                    │   │   ├── home.js
                    │   │   └── ...
                    │   ├── boot_loader ---基础js类库，全局依赖的js库
                    │   │   ├── jquery.min.js
                    │   │   ├── json3.min.js
                    │   │   └── require.min.js ---一个JavaScript文件或者模块的加载器，暂未使用
                    │   ├── guide
                    │   │   └── ....
                    │   ├── i18n ---国际化js文件占位文件夹，里面的文件在路由器启动时生成
                    │   ├── lib ---公共的js类库，非全局依赖
                    │   │   ├── dialog.js
                    │   │   ├── jquery.datetimepicker.js
                    │   │   ├── jquery.validate.config.js
                    │   │   ├── jquery.validate.js
                    │   │   └── pushstream.js
                    │   ├── openapi ---调用openapi接口的基于ajax的基础类库（twx中几乎所有的请求，都使用它）
                    │   │   └── openapi.js
                    │   └── readme.txt
                    └── style
                        ├── admin_mobile ---移动端样式表
                        │   ├── g.css
                        │   └── net_detect.css
                        ├── guide ---首次安装样式表
                        │   ├── 360.css
                        │   ├── ie8.css
                        │   ├── iphone4.css
                        │   └── ...
                        ├── home.css ---pc端样式表
                        ├── login.css
                        └── ...
</code></pre>

### 2. <span id="p2">单个页面组成部分</span>

##### html模板的构成
<pre><code>
1.页面中需要执行的lua，写在文件头部
2.include('header')
3.引入样式表 href="../style/demo.css?v=<%=ver.svnRevNum%>"
4.引入其他子页面
    ...（本页html）
    include('admin_web/menu/menu_left') ---引入pc端的左侧菜单（仅pc端模块主页面需引入）
    ...（本页html）
    include('demo/demo1') ---引入其他子页面
    ...（本页html）
5.include('footer')
...
6.引入通用模块的专属js文件： src=".../general_module.js?v=<%=ver.svnRevNum%>"
7.引入本页的专属js文件： src=".../demo.js?v=<%=ver.svnRevNum%>"
</code></pre>

##### 页面专属js文件的构成
<pre><code>
1.初始化数据仓库store
    HiWiFi.dataStore({
        demo_data: {
            demo_name: ""
        },
        ...
    });
2.Interfaces列表（本页用到的所有的openapi接口）
    //修改设备名称
    function setDeviceName(request_data, successCallback, errorCallback) {
       //...(写入数据类型 接口写前面)
    }
    ...
    //检查系统是否需要升级
    function checkRomUpdate(muti_call) {
        //...(读取数据类型 接口写后面)
        //ajax回调函数中执行：HiWiFi.dataStore.updateData("demo_data",{...})
    }
3.页面展现逻辑，依据dataStore中的数据模型，展现页面内容
    var homeView = function () {
        var controller_view = {
            showViewRightInfo: function () {
                var demo_data = HiWiFi.dataStore.getData("demo_data");
                //...
                if (demo_data.demo_name) {
                    //操作dom
                }
            },
            ...
        };
        //默认向controller_view中的函数添加dataStore事件监听
        HiWiFi.dataStore.addChangeListenerFromCaller(controller_view);
        return controller_view;
    };
4.Actions
    4.1初始化页面
    var controller_view = homeView();
    4.2定义各种事件
    4.3配置各个表单的，验证规则和提示
</code></pre>

### 6. <span id="p6">ajax的异常处理，与递归调用</span>
##### 在通过xmlHttpRequest对象时（同过ajax的非jsonp访问），处理异常是非常重要的，尤其是递归里调用ajax时。
<pre><code>
var xhr = $.ajax({
    url: “”,
    cache: false,
    type: "POST",
    async: true, //同步会影响页面的渲染--此处选择异步
    dataType: "json",
    data: JSON.stringify(ajaxData),//对象转字符串，千万不要拼字符串
    timeout: 20000,//超时是一定要定义的
    context: callbacks_context//各个回调函数的父对象,防止上下文丢失
});
</code></pre>
<pre><code>
xhr.done(function (rsp, status, xhr) {
    var callbacks = $(this)[0];
    if (rsp.code === 0 || rsp.code === "0") {
        if (typeof callbacks.success === "function") {
            //执行成功回调
            callbacks.success(rsp, status, xhr);
        }
    } else {
        if (rsp.code === 99999 || rsp.code === "99999") {
            if (typeof callbacks.noAuthError === "function") {
                //执行没有登陆权限的回调函数
                callbacks.noAuthError(rsp);
                return false;
            }
        }
        
        if (typeof callbacks.responseError === "function") {
            //执行接口报错的回调函数
            callbacks.responseError(rsp);
        }
    }
});
</code></pre>
<pre><code>
xhr.fail(function (e) {
    var callbacks = $(this)[0];
    if (e.status === 0) {
        if (e.statusText === "abort") {
            if (typeof callbacks.canceledError === "function") {
                //http被取消时，所执行的回调函数（可能是浏览器取消的，或自己手动取消的）
                callbacks.canceledError(e);
                return false;
            }
        } else if (e.statusText === "timeout") {
            if (typeof callbacks.timeoutError === "function") {
                //接口超时 时执行的回调函数
                callbacks.timeoutError(e);
                return false;
            }
        }
    }
    if (typeof callbacks.requestError === "function") {
            //在http请求失败，但没有失败原因时执行的回调
            callbacks.requestError(rsp);
        }
});
</code></pre>
<pre><code>
xhr.always(function (rsp) {
    var callbacks = $(this)[0];
    if (typeof callbacks.always === "function") {
        //不论http请求成功或失败都应执行的回调
        callbacks.always(rsp);
    }
});
</code></pre>

##### 递归里调用，需要注意异常时是否需要再次调用
<pre><code>
function demo(i) {
    var xhr = $.ajax({
    url: “”,
    cache: false,
    type: "POST",
    ...
    });
    xhr.done(function (rsp, status, xhr) {
        //正常此处需要处理
        demo(i++);
    });
    xhr.fail(function (e) {
        //异常此处需要处理,确定是否需要重试
        setTimeout(function() {
            demo(i);
        }, 500);
    });
    xhr.always(function (rsp) {
        //不论成功失败，都继续递归
        demo(i++);
    });
}
</code></pre>