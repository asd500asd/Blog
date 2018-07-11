---
title: 字体文件走CDN跨域
date: 2018-07-11 10:45:00
tags:
- 防坑指南
categories:
- 防坑指南
---


> 事因：线上环境静态资源走CDN，但出现了字体CDN跨域的情况。百度查了下可以改七牛云的白名单解决防盗链导致的跨域，但是修改后七牛处理需要很长时间。于是想到去年一个项目出现同样的问题，搜了下有道云笔记，感谢自己有记笔记的习惯。。。1分钟解决问题，字体转成base64，完美解决~~~


以下为解决方案：

知识就像内-裤，看不见但很重要。

如上，文字是由base64字体编码生成的，是不是碉堡？！

CSS3给我们带来了@font-face,网页中可以展现的字体就不局限于电脑中已安装的几款字体。@font-face的作用是从网上下载并使用自定义字体，使页面显示字体不依赖用户的操作系统字体环境。


当然，需要注意点的是字体文件的体积一般都比较大，而且需要额外的http请求连接，而且需要兼容多种浏览器的话，往往需要三四个或者更多格式的字体文件，目前最主要的几种网络字体(web font)格式包括WOFF，SVG，EOT，OTF/TTF。这直接影响了网页的加载速度，所以你要清楚它的利与弊。国外的网站中用的比较多，国内嘛….在我看来，弊大于利。


慢慢的就有了WebFont (Web Open Font Format) 这个技术，极大滴有针对性的压缩了字体文件的体积，压缩了体积就解决了个大问题，很实用。


今天我要介绍的就是把WebFont的字体转换成base64编码，直接放在样式表里面，是不是感觉很神奇？


生成下载需要的字体

首先，先设置我们要使用的文字，使用WebFont，推荐阿里妈妈WebFont平台http://www.iconfont.cn/webfont/#!/webfont/index和有字库http://www.youziku.com/，使用简单粗暴，输入你要的文字，下载就完了，代码都给你生成好了。

```
@font-face {font-family: 'webfont';
    src: url('webfont.eot'); /* IE9*/
    src: url('webfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
    url('webfont.woff') format('woff'), /* chrome、firefox */
    url('webfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
    url('webfont.svg#webfont') format('svg'); /* iOS 4.1- */
}
```

ctrl+c ctrl+v 就解决了需求，兼容至BT的IE6，简直碉堡。
解码

然后，http://www.motobit.com/util/base64-decoder-encoder.asp,

**打开这个网址，上传字体就能直接生成base64编码，神器==！**==

注意：我们要转换的字体是.ttf格式的。

然后就得到了base64编码，但是这个网站生成的base64很奇葩，有很多空格换行，代码少可以认为删掉，但是生成的代码一般都不少，处理很麻烦，在这里教大家一个简单的方法，把代码复制丢进word，ctrl+h替换，【替换内容】输入^p，【替换为】什么都不要填>确定，换行空格就都没有了。好，Get了可以使用的base64字体编码。
怎么使用？
把处理好了的base64编码放在下面代码中：


```
@font-face {
    font-family: 'webfont';
    src: url(data:font/truetype;charset=utf-8;base64,XXXXXXXXXXXXXXXXX) format('truetype');
    font-weight: normal;
    font-style: normal
}
```

就这样，定义我们要使用的字体了。
调用
使用我们定义的字体：


```
.web-font {
  font-family:"webfont" !important;
  font-size:16px;
  font-style:normal;
  -webkit-font-smoothing: antialiased;        //字体抗锯齿
  -webkit-text-stroke-width: 0.2px;           //设置或检索对象中的文字的描边宽度。可取小数和medium（描边宽度中等）, thick（描边宽度粗）, thin（描边宽度细）关键字。默认值为0。
  -moz-osx-font-smoothing: grayscale;         //字体抗锯齿
}
```


好了，这样就大功告成，简单实用粗暴，赶紧试试吧。


下面来谈谈这个技术的优缺点吧！

优点：

1、首先不得不说的是它的体积：很小，有针对性的字体，文字越少，体积越小；

2、美化网页文字、增强界面效果，不需要图片来代替，也就减少了http请求；

3、base64编码直接放进了样式表，这样不需要额外的http请求；

4、字体的大小、颜色、粗细可控；
5、支持主流浏览器，即使不支持也可以优雅降级；
缺点：

1、维护麻烦，如果PM那天要改版，改下文字就要重新生成字体-转码；

2、字体比较少，正规授权的字体很少；

3、暂时发现只能使用一个编码，使用.ttf格式的字体转换的base64编码；

4、发现火狐支持并没有chrome那么完美，字体太小会出现缺失，所以字体必须足够大；

5、不支持IE，对IE不兼容。如果追求兼容性，还是推荐使用@font-face直接下载字体；
欧克，撒U拉拉。

