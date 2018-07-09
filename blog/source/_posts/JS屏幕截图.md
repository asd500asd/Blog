---
title: JS屏幕截图
date: 2018-07-09 10:56:58
tags:
- 前端
categories:
- 前端
---

> 最近比较忙，很久没写博客了，今天抽空将最近项目中遇到的一些问题及一些解决方案记录一下。

最近接触的项目主要是移动端的，关于移动端方面适配之类的文章太多了，对这方面的技术不做过多的阐述。主要记录下这次中遇到的一些吧。

1. 截图分享功能
2. 按钮防重复点击(节流函数应用)
3. 下拉刷新，上拉加载更多(这个地方有坑)
4. 性能优化方面


### 截图分享功能
事因：移动端分享功能，产品经理给的原型是 点击分享后将需要分享的内容放在一个固定的容器中，生成UI指定的样式后，通过图片的方式分享到微信、微博等第三方应用中。

技术选型：最初想到的是用  html2canvas,嗯，说完就去写了一个demo,具体效果也还可以，就是这个不能引用跨域的图片，解决的办法是直接先请求下来图片，再直接给准成Base64放上去了，简单粗暴。。。

不过最后还是放弃了使用html2canvas,原因是在github的issues里说道这个插件可能还是会有些问题，比如导致生成的图片模糊(这个github也有专门针对解决的方案),页面空白之类的。又查了下发现一个stars数较高的，就是今天想说的了 [rasterizeHTML.js](https://github.com/cburgmer/rasterizeHTML.js)，经过在项目检验，这个确实挺好用的，用法在github上挺详细的，点击前面的链接就OK啦~~~

**需要注意的地方：**

1. 生成图片是需要先生成canvas的，而canvas是需要指定宽高的。所以在生成图片也是需要宽高的，移动端宽度采用 750比较合适，高度的话可以写上最小高度 1350，这样保证截的图片长宽比比较合适，具体高度的话，除去UI给图的头尾部分固定高度，内容方面根据每行的高度来判断，写点计算代码算下 所需内容能生成多少行文字，再想加一下就好了。

2. 比较关键的地方是此项目采用的是安卓套webview的方式完成，所以当点击分享的时候，需要传给安卓的是一张图片地址，而 第一步生成的是canvas，我们需要将其转换成blob，再通过formdat的方式将图片上传到后端，拿到图片链接，最后才是将图片传给安卓并分享出去。贴下关键代码：


截图模板.js
```
import {codeHtml} from "../../common/share-html";
export default function (data = {}) {
    const {
        shareImage,
        shareTitle,
        shareTime,
        shareContent,
        contentHeight,
        shareCode,
        shareMsg
    } = data;
    const w = document.getElementsByTagName('html')[0].style.fontSize;
    return `
<div style="width: 750px;text-align: center;background-color: #ffffff;">
<style type="text/css">
html {
font-size: 100px;
height: 100%;
}
body{
padding: 0;
margin: 0;
background-color: #ffffff;
height: 100%;
}

p {
    margin: 0;
    padding: 0;
}

.modal-img {
    width: 100%;
}
.modal-content {
    font-size: .28rem;
    background: #fff;
    height: 100%;
    /*padding-bottom: 60px;*/
}
.model-title {
  font-size: .36rem;
  font-weight: bold;
  padding-top: .15rem;
}

.modal-time {
   color: #9B9B9B;
   font-size: .24rem;
   padding-top: .15rem;
   text-align: left;
   padding-left: .5rem;
}
.model-main-content {
      /*text-indent: 2em;*/
      margin-top: .12rem;
      padding: 0 .5rem;
      font-size: .3rem;
      line-height: 1.5;
      min-height: ${contentHeight}px;
      text-align: justify;
      color: #191919;
    }

${codeHtml.style}

</style>
     <div class="modal-content">
                <img class="modal-img" src="/static/img/header.png"/>
                <div class="model-title">${shareTitle}</div>

                <div class="modal-time">${shareTime}</div>
                <div class="model-main-content">
                    ${shareContent}

                </div>
                ${codeHtml.html}

    </div>

</div>

    `;
}
```


分享页面.js


```
****
import marketShareHtml from "../../../components/share-template/share-img";
****

 async handleShare(news) {let shareTemContent = this.htmlFilter(news.content);
            let shareTemTime = dayjs(news.publishTime * 1000).format('YYYY-MM-DD HH:mm');
            const tempImg = new Image();
            const vm = this;
            // console.log(shareTemContent.length / 20, 'length');// 自动计算内容高度，自适应canvas高度。
            const templateHeight = shareTemContent.length / 20 * 40;
            const tempH = templateHeight > 580 ? templateHeight : 580;
            tempImg.src = '/static/img/header.png';
            // 图片加载完成后生成 html模板
            tempImg.onload = async function () {
                const html = marketShareHtml({
                    shareTitle: '',
                    shareTime: shareTemTime,
                    shareContent: shareTemContent,
                    contentHeight: tempH,
                    shareCode: '',
                    shareMsg: ''
                });
                // console.log(html, 22222);
                const canvas = document.createElement('canvas');
                let tempHeight = 720;
                const finalHeight = tempHeight + tempH;
                // console.log(finalHeight, 'finalHeight');
                canvas.width = 750;
                canvas.height = finalHeight > 1280 ? finalHeight : 1280;
                // console.log(canvas.height, 'canvas.height');
                const res = await
                // 这个地方是关键代码---绘制canvas
                vm.$rasterizeHTML.drawHTML(html, canvas);
                // 这个地方是生成blob，
                vm.shareImage = canvas.toBlob((blob) => {
                    // var newImg = document.createElement("img"),
                    // console.log('blob', blob);
                    const url = window.URL.createObjectURL(blob);


                    if (vm.first) {
                        vm.handleShare(news);
                        vm.first = false;
                    } else {
                        vm.$store.dispatch('dynamic/newsFlash/changeBlob', blob);

                        // 下面是将blob变成图片地址的实现方法。
                        // const fd = new FormData();
                        // fd.append('img', blob, 'image.png');
                        // axios({
                        //     url: '/b/a/u/info/img',
                        //     method: 'post',
                        //     data: fd,
                        //     headers: {'Content-Type': 'multipart/form-data'}
                        // }).then(function (data) {
                        //     console.log(data);
                        // }).catch(function (err) {
                        //     console.log(err);
                        // });
                        vm.$store.dispatch('dynamic/common/toggleSharePopupModal', {title: '分享后需返回APP才能获得奖励', showModal: true, screenShot: null, image: vm.shareImage});
                        // vm.$ModalHelper.afterOpen('flash-news-wrap');
                    }
                });
            };
        },


```



---
屏幕截图这块差不多就完结了，主要就是几种格式之间的转化，多注意下就好了。