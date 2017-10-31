### 移动端1px变粗的原因
由于对移动屏幕的物理像素和css像素的不一致，以至于在html中设置一下代码后

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```
使得css的1像素在设备中根据devicePixelRatio映射到物理像素的2像素or3像素（devicePixelRatio为物理像素和css像素的比例）

### 解决方案
#### 1、用小数来写px值
```scss
.border{border: 1px solid #ccc;}
@media screen and (-webkit-min-device-pixel-ratio: 2){
	.border{border: 0.5px solid #ccc;}
}
@media screen and (-webkit-min-device-pixel-ratio: 3){
	.border{border: 0.3333px solid #ccc;}
}
```
**缺点：**安卓与低版本ios不适用

#### 2、border-image
```scss
@media screen and (-webkit-min-device-pixel-ratio: 2){
	.border{
		border: 1px solid transparent;
		border-image: url(border.gif) 2 repeat;
	}
}
```
**缺点:** 对于圆角样式, 将图片放大修改成圆角也能满足需求, 但这样无形中增加了border的宽度，存在多种边框颜色或者更改的时候麻烦。

#### 3、background渐变
```scss
@media screen and (-webkit-min-device-pixel-ratio: 2), only screen and (min-device-pixel-ratio:2)	{
	.good-content {
     border: none;
     background-image: -webkit-linear-gradient(90deg,#e000,#00 50%,transparent 50%);
     background-image: -moz-linear-gradient(90deg,#000,#e000 50%,transparent 50%);
     background-image: -o-linear-gradient(90deg,#000,#000 50%,transparent 50%);
     background-image: linear-gradient(0,#000,#000 50%,transparent 50%);
     background-size: 100% 1px;
     background-repeat: no-repeat;
     background-position: bottom
   }}
```
**缺点:**代码量大

#### rem(flexible.js)
```javascript
////根据屏幕大小及dpi调整缩放和大小  
(function () {
        var scale = 1.0;
        var ratio = 1;
        if (window.devicePixelRatio >= 2) {
            scale *= 0.5;
            ratio *= 2;
        }
        var text = '<meta name="viewport" content="initial-scale=' + scale + ', maximum-scale=' + scale + ',' + ' minimum-scale=' + scale + ', width=device-width,' + ' user-scalable=no" />';
        document.write(text);
        document.documentElement.style.fontSize = 50 * ratio + "px";
    })();
    
/* if Android */
var link = document.createElement('link');
link.setAttribute("rel","stylesheet");
link.setAttribute("type","text/css");
link.setAttribute("href",".......Android.css");
document.querySelector('head').appendChild(link);
```
**缺点：**不适用安卓, 原因在于安卓手机不一定有devicePixelRatio属性, 就算有也不一定能响应scale小于1的viewport缩放设置

#### :after，:before与transform
```scss
.border{position: relative}
@media screen and (-webkit-min-device-pixel-ratio: 2){
	.border:before{
		content: 0;
		pinter-events: none;/*防止点击触发*/
		box-sizing: border-box;
		position: absolute;
		width: 200%;
		height: 200%;
		left: 0;
		top: 0;
		border: 1px solid #ccc;
		transform: scale(0.5);
		transform-origin: 0 0;
	}
}
```
**缺点:** 代码量也很大, 占据了伪元素, 容易引起冲突

***以上内容参考自***[移动端1px解决方案](http://www.cnblogs.com/fang51/p/5681528.html),[移动端1px细线解决方案总结](http://www.jianshu.com/p/d62d22b44ce4),[返回主页 lunarorbitx 移动端1px细线解决方案总结](http://www.cnblogs.com/lunarorbitx/p/5287309.html)