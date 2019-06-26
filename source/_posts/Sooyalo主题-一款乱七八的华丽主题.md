---
title: Sooyalo主题-一款乱七八的华丽主题~
date: 2018-06-24 17:32:06
tags:
- Hexo
- Sooyaro
categories: 
- Web前端
thumbnailImage: https://images.alphacoders.com/165/165083.jpg

metaAlignment: center

coverImage: https://images7.alphacoders.com/567/567688.jpg

coverSize: full

coverMeta: out

---

选择好搭建空间后，进而选择一个合适的主题便成为了首要任务，一开始本想直接应用[NEXT](https://github.com/iissnan/hexo-theme-next)主题，但考虑到它让大部分使用者失去个人特色的恐怖应用量以及我所厌恶的极简主义，所以选择以[themia](https://github.com/kaedea/hexo-theme-themia)主题为基础进行一定程度的个人化修改，最后脸滚键盘取了一个谁也不知道什么意思的名字。这就是Sooyalo的由来。
<!-- more -->
<br>

# 如何搭建好Hexo框架以及Github Page服务器？

相比于坑死人的Hexo超（la）级（ji）官方文档，我更倾向于知乎大神给的最简单直白的新手教程，每一步都介绍得不多不少，很详细，直接上链接就好

[GitHub+Hexo 搭建个人网站详细教程](https://zhuanlan.zhihu.com/p/26625249)

<br>

# 什么是themia主题？
<code>themia</code>是由[kaedea](http://kaedea.com)以[tranquilpeak](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak)为基础进行一定额度的个人化以及中国本土化修改而形成的华丽的 Hexo 响应式主题，以ejs语言编写。对于<code>themia</code>主题各项功能，kaedea在其个人博客中已经有详细的介绍（[HEXO 响应式主题・ THEMIA](http://kaedea.com/2015/10/05/themia-demo-using-thmia/)）。之所以选择这个主题是因为看中了它丰富的图片处理能力以及简单易懂的结构，方便对其进行个人化加工。另外，它对于手机端的特殊加工也是该主题的一大特色~~，虽然我已经放弃了手机端的优化~~。

<br>

# 相比于themia，乱七八糟的Sooyalo有何不同？
之所以叫它乱七八糟，主要是因为我所添加的多半都是小部件，对于本体结构其实并没有太大的改变。而且目测...大概不会有人会选择直接使用本主题（反正这博客也是自己写着玩的），所以干脆直接把这些部件的代码全部直接写出来或许会比较合适一些..
### fireworks的鼠标交互特效
本着和极简主义说不的精神，我使用了anime.js连同canvas画布铺盖整个网页，再用css将它至于顶层便可直接使之生效。
首先是js语言进行脚本的架构。
fireworks.js：
```
window.human = false;

var canvasEl = document.querySelector('.fireworks');
var ctx = canvasEl.getContext('2d');
var numberOfParticules = 30;
var pointerX = 0;
var pointerY = 0;
var tap = ('ontouchstart' in window || navigator.msMaxTouchPoints) ? 'touchstart' : 'mousedown';
var colors = ['#FF1461', '#18FF92', '#5A87FF', '#FBF38C'];

function setCanvasSize() {
  canvasEl.width = window.innerWidth * 2;
  canvasEl.height = window.innerHeight * 2;
  canvasEl.style.width = window.innerWidth + 'px';
  canvasEl.style.height = window.innerHeight + 'px';
  canvasEl.getContext('2d').scale(2, 2);
}

function updateCoords(e) {
  pointerX = e.clientX || e.touches[0].clientX;
  pointerY = e.clientY || e.touches[0].clientY;
}

function setParticuleDirection(p) {
  var angle = anime.random(0, 360) * Math.PI / 180;
  var value = anime.random(50, 180);
  var radius = [-1, 1][anime.random(0, 1)] * value;
  return {
    x: p.x + radius * Math.cos(angle),
    y: p.y + radius * Math.sin(angle)
  }
}

function createParticule(x,y) {
  var p = {};
  p.x = x;
  p.y = y;
  p.color = colors[anime.random(0, colors.length - 1)];
  p.radius = anime.random(16, 32);
  p.endPos = setParticuleDirection(p);
  p.draw = function() {
    ctx.beginPath();
    ctx.arc(p.x, p.y, p.radius, 0, 2 * Math.PI, true);
    ctx.fillStyle = p.color;
    ctx.fill();
  }
  return p;
}

function createCircle(x,y) {
  var p = {};
  p.x = x;
  p.y = y;
  p.color = '#FFF';
  p.radius = 0.1;
  p.alpha = .5;
  p.lineWidth = 6;
  p.draw = function() {
    ctx.globalAlpha = p.alpha;
    ctx.beginPath();
    ctx.arc(p.x, p.y, p.radius, 0, 2 * Math.PI, true);
    ctx.lineWidth = p.lineWidth;
    ctx.strokeStyle = p.color;
    ctx.stroke();
    ctx.globalAlpha = 1;
  }
  return p;
}

function renderParticule(anim) {
  for (var i = 0; i < anim.animatables.length; i++) {
    anim.animatables[i].target.draw();
  }
}

function animateParticules(x, y) {
  var circle = createCircle(x, y);
  var particules = [];
  for (var i = 0; i < numberOfParticules; i++) {
    particules.push(createParticule(x, y));
  }
  anime.timeline().add({
    targets: particules,
    x: function(p) { return p.endPos.x; },
    y: function(p) { return p.endPos.y; },
    radius: 0.1,
    duration: anime.random(1200, 1800),
    easing: 'easeOutExpo',
    update: renderParticule
  })
    .add({
    targets: circle,
    radius: anime.random(80, 160),
    lineWidth: 0,
    alpha: {
      value: 0,
      easing: 'linear',
      duration: anime.random(600, 800),  
    },
    duration: anime.random(1200, 1800),
    easing: 'easeOutExpo',
    update: renderParticule,
    offset: 0
  });
}

var render = anime({
  duration: Infinity,
  update: function() {
    ctx.clearRect(0, 0, canvasEl.width, canvasEl.height);
  }
});

document.addEventListener(tap, function(e) {
  window.human = true;
  render.play();
  updateCoords(e);
  animateParticules(pointerX, pointerY);
}, false);

var centerX = window.innerWidth / 2;
var centerY = window.innerHeight / 2;

function autoClick() {
  if (window.human) return;
  animateParticules(
    anime.random(centerX-50, centerX+50), 
    anime.random(centerY-50, centerY+50)
  );
  anime({duration: 200}).finished.then(autoClick);
}

setCanvasSize();
window.addEventListener('resize', setCanvasSize, false);
```
而后利用css对其位置进行定义
fireworks.css：
```
.fireworks {
    position: fixed;
    z-index: 99;
    pointer-events: none;
    top: 0;
    left: 0;
    widows: 100%;
    height: 100%;
    display: inline-block;
    box-sizing: border-box;
}
```
另外也需要引用anime.js的官方配置anime.min.js
```
<script src="https://cdn.bootcss.com/animejs/2.0.2/anime.min.js"></script>
```
这样子差不多就万事OK，把两个js脚本以及css导入后，在layout之类的布局脚本中直接利用canvas标签来引用就可以做到全站使用了~
```
<canvas class="fireworks"></canvas>
```

### live2D桌面人物
页面右下角的随鼠标摇摆的二次元人偶，看似很复杂，实际上应用起来非常简单（因为人家已经开过源了233【[hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d)】）。
先回到hexo框架的博客文件夹，shift右键进入命令窗，输入：
```
npm install --save hexo-helper-live2d
```
该插件就算是安装好了~
而后进入主题的_config.yml文件中
```
live2d:
  model: koharu
  bottom: -60
```
人偶就算布置完成了~
其实对于live2d还可以进行很多方面的修改，具体如下
```
live2d:
  enable: true
  scriptFrom: local
  pluginRootPath: live2dw/
  pluginJsPath: lib/
  pluginModelPath: assets/
  model:
    use: live2d-widget-model-wanko
  display:
    position: right
    width: 150
    height: 300
  mobile:
    show: true
```
另外live2D拥有多个model以供选择，我把所有model的名字都列出来，有兴趣的话可以一个一个常识一下
```
live2d-widget-model-chitose
live2d-widget-model-epsilon2_1
live2d-widget-model-gf
live2d-widget-model-haru/01 (use npm install --save live2d-widget-model-haru)
live2d-widget-model-haru/02 (use npm install --save live2d-widget-model-haru)
live2d-widget-model-haruto
live2d-widget-model-hibiki
live2d-widget-model-hijiki
live2d-widget-model-izumi
live2d-widget-model-koharu
live2d-widget-model-miku
live2d-widget-model-ni-j
live2d-widget-model-nico
live2d-widget-model-nietzsche
live2d-widget-model-nipsilon
live2d-widget-model-nito
live2d-widget-model-shizuku
live2d-widget-model-tororo
live2d-widget-model-tsumiki
live2d-widget-model-unitychan
live2d-widget-model-wanko
live2d-widget-model-z16
```
为你的hexo添加色气满满的live2d吧！
我先对着我的koharu傻笑30分钟再接着写（吸口水）

### 文章目录
我一直很困惑为什么themia这个已经基本成熟的主题的框架为什么没有一个文章的目录部件。所以还是自己动手丰衣足食好了.按照官方文档，直接引用toc函数就可以自动生成一篇文章的目录了。可以选择单独创立一个toc.ejs组件
```
<% if (post.toc != false) { %>
<div id="toc">
  <%- toc(post.content, {list_number: false}) %>
</div>
<% } %>
```
然后相对应的css进行布局：
```
#toc {
    position: fixed;
    right: 0%;
    width: 320px;
    height: 90%;
    top: 15px;
}

for i in (1..6) {
    .toc-level-{i} .toc-text {margin-left: i em;}
}

.toc-text {white-space: nowrap;}

.toc-link {
    color: #999;
    display: inline-block; // stretch to parent height
    border-left: 1px solid transparent; // avoid "jumping" on hover
    &:hover {text-decoration: none;}
}

.toc {
    padding: 0;
    margin: 0;
    line-height: 1.8em;
    overflow-y: auto;
    overflow-x: hidden;
    width: 100%;
    height: 100%;
}

.toc-child {
    margin-left: 0;
    padding-left: 0;
}

.toc-item {
    list-style-type: none;
    &:hover > .toc-link {border-left: 1px solid $color-theme;}
    &.active {
        > .toc-link {
            color: $color-theme;
            border-left: 2px solid $color-theme;
        }
        if(hexo-config('toc.collapse')) { .toc-child {display: block;} }
    }
}

.toc-item.active .toc-child { display: block; }
```
最后利用 
```
<%- partial() %>
```
函数将toc.ejs放置在合适的位置就好。位置一定要选对，否则很可能会出现逻辑bug（跪了两天），一个超级简单的toc组件就算完成了~
之后我可能会进一步完善这一部分，比如说加上动画效果，或者随画面而动之类的。以后的事嘛.最近略忙。

### 百分比样式回到顶部按钮
就是左下角那个圆形的随着页面改变数字会动，按一下就会回到顶部的家伙，主要参考了[unnamed42](https://unnamed42.github.io/2016-09-10-Hexo%E6%8A%98%E8%85%BE%E7%AC%94%E8%AE%B0.html#%E7%99%BE%E5%88%86%E6%AF%94%E6%A0%B7%E5%BC%8F)的使用方法，并且针对我的博客进行一些逻辑上和位置上的调整，首先是ejs文件和css文件
totop.ejs(还是画布.....)
```
<div id="totop" title="<%= __('to_top') %>">
    <canvas id="totop-canvas" width="48" height="48"></canvas>
    <div id="totop-percent"></div>
</div>
```
totop.css
```
#totop {
    background-color: #eee;
    border-radius: 100%;
    bottom: 5%;
    height: 48px;
    width: 48px;
    top: 78%;
    left: 320px;
    position: fixed;
    right: -100px;
    z-index: 99;
    -webkit-transition: 0.5s;
       -moz-transition: 0.5s;
            transition: 0.5s;
}

#totop.display {right: 10px;}

#totop-percent {
    font-size: 16px;
    height: 48px;
    line-height: 48px;
    position: absolute;
    text-align: center;
    top: 0;
    width: 48px;
    color: #555;
    cursor: pointer;
}

#totop-percent:before {content:attr(data-percent);}

#totop-percent:hover:before {
    display: inline-block;
    font: 14px/1em FontAwesome;
    font-size: inherit;
    text-rendering: auto;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    content: "\f176";
}
```

再js加入脚本，实现画圆圈，箭头等功能
```
$(function() {
    var totop = $("#totop"),
        canvas = $("#totop-canvas"),
        percent = $("#totop-percent"),
        width = canvas.width(),
        height = canvas.height(),
        center = width / 2,
        radius = parseInt((width - 3) / 2),
        ctx = canvas[0].getContext("2d");
    function drawCircle(color, percent) {
        ctx.beginPath();
        ctx.arc(center, center, radius, - Math.PI / 2, Math.PI * 1.5 * percent, false);
        ctx.strokeStyle = color;
        ctx.lineCap = "round"; // butt, round or square
        ctx.lineWidth = 3;
        ctx.stroke();
    }

    totop.click(function() {
        $("body, html").animate({
            scrollTop: 0
        }, 800);
    });

    $(window).scroll(function() {
        var docHeight = $(document).height() - $(window).height(),
            scrollTop = $(window).scrollTop(),
            per = parseInt(scrollTop / docHeight * 100);
        if (scrollTop >= 200) {
            totop.addClass("display");
            ctx.clearRect(0, 0, width, height);
            drawCircle("#efefef", 1);
            drawCircle("#555555", per/100);
        } else
            totop.removeClass("display");
        percent.attr("data-percent", per);
    });
});
```
在post一类的布局文件中引入ejs文件就可以直接使用，但是虽然功能更强，但代码量更大，性能损耗更多。具体如何取舍还看个人。比如我这网页有的手机跑起来就会比较卡，但是pc来讲一般都是没有问题的。

### 不蒜子流量统计
真如官网所说，只用两行代码，一是引入js脚本，再就是使用
```
<script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
 <span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
```
为静态网站提供免费的流量统计服务，great~

### css布局调整
主要是为了给那些小布局腾出空间，另外适当对一些布局（背景A，日志宽度之类的）进行个人化调整。都是一些繁琐地F12中css的基础小操作，没必要详细写出来，之后完全可以按照个人喜好再做调整

### 其他
其实还是有很多细节的修改，多余文件的删除，布局的调整，etc...

# 终语
折腾了两三个礼拜的博客现在暂时算是可以使用了，一开始的预估时间只是一个礼拜随便弄弄，后来自己的小需求一点一点变多，外加没有前端基础，以及有别的功课需要学习，（手里的碧蓝航线也不能停，认真脸.jpg）所以一直有一搭没一搭地乱写着。之后要是用比较充裕的时间的话，会尝试将这个博客进一步优化，比如手机端的大头以及再添加一些小部件什么的。毕竟我讨厌极简主义XD。
OK，可以全心去忙别的了oh yeah~