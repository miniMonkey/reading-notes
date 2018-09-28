# canvas相关接口

## 创建画布

```
// HTML
<canvas id="canvas"></canvas>

// Javascript
var canvas = document.getElementById('canvas');
var context = canvas.getContext('2d')
```

## Line

### Draw one line

```
// 状态设置
context.moveTo(100, 100); // 移动
context.lineTo(700, 700); // 画线
context.lineWidth = 10; // 线条宽度
context.strokeStyle = "#058"; // 颜色

// 绘制
context.stroke();
```

### Draw more lines

`beginPath` 后面可以直接使用 `lineTo`，作用与 `moveTo` 一致

```
// 状态设置
context.lineWidth = 10; // 线条宽度

context.beginPath(); // 之后配置的新的状态可以覆盖旧的状态，旧状态依然保留
context.moveTo(100, 200); // 移动
context.lineTo(300, 400); // 画线
context.lineTo(100, 600); // 画线
context.strokeStyle = "red"; // 颜色
context.stroke();

context.beginPath();
context.moveTo(300, 200); // 移动
context.lineTo(500, 400); // 画线
context.lineTo(300, 600); // 画线
context.strokeStyle = "green"; // 颜色
context.stroke();

context.beginPath();
context.moveTo(500, 200); // 移动
context.lineTo(700, 400); // 画线
context.lineTo(500, 600); // 画线
context.strokeStyle = "blue"; // 颜色
context.stroke();
```

`beginPath()` 与 `closePath()` 组合使用，绘制封闭图形，`fill`  可以为封闭图形进行填充

```
// 状态设置
context.lineWidth = 10; // 线条宽度
context.strokeStyle = "#058"; // 线条色
context.fillStyle = "yellow"; // 填充色

context.beginPath(); // 之后配置的新的状态可以覆盖旧的状态，旧状态依然保留
context.moveTo(100, 350); // 移动
context.lineTo(500, 350); // 画线
context.lineTo(500, 200); // 画线
context.lineTo(700, 400); // 画线
context.lineTo(500, 600); // 画线
context.lineTo(500, 450); // 画线
context.lineTo(100, 450); // 画线
context.closePath();

// 填充
context.fill();

// 绘制
context.stroke();

// fill 如果在 stroke 后面，则边框会被填充 fill 覆盖一半的宽度，所以如果想展示完整边框，需要把 fill 过程放在 stroke 前面
// context.fill();

```

###  Draw a Rectangle

```
// 绘制矩形
context.rect(x, y, width, height);

// 绘制矩形，并使用当前 fillStyle 填充矩形
context.fillRect(x, y, width, height);

// 绘制矩形，并使用当前 strokeStyle 进行绘制边框
context.strokeRect(x, y, width, height);
```

###  线条属性 `lineCap`

只有开始和结尾处会受到影响，连接处不会受到影响，有三个属性如下：
* butt(default)
* round 两头各多一个圆形头
* square 两头各多一个方形头

### Draw a Star

```
context.beginPath();
for (var i = 0; i < 5; i++) {
    context.lineTo(
        Math.cos((18 + i * 72) / 180 * Math.PI) * 300 + 400,
        -Math.sin((18 + i * 72) / 180 * Math.PI) * 300 + 400
    );
    context.lineTo(
        Math.cos((54 + i * 72) / 180 * Math.PI) * 150 + 400,
        -Math.sin((54 + i * 72) / 180 * Math.PI) * 150 + 400
    );
}
context.closePath();

context.lineWidth = 10;
context.stroke();

```

## 线条属性 `lineJoin`

连接处的展示形式，有三个属性如下：
* miter(default) 尖角
    - miterLimit 默认10像素 两个线条需要补充的尖角距离如果超过10，就会使用 bevel 的方式显示
* bevel 斜接
* round 圆角

## 图形变换

* 位移 translate(x, y)
* 旋转 rotate(deg)
* 缩放 scale(sx, sy) 对于其他属性，例如坐标、边框宽度也会同样进行缩放

## 状态保存和恢复

* context.save(); 保存当前状态
* context.restore(); 恢复保存时的状态

## 变换矩阵

`transform(a, b, c, d, e, f);`
`setTransform(a, b, c, d, e, f);` 忽略之前的transform

> a c e 
> b d f
> 0 0 1
> a 水平缩放(1) c 垂直倾斜(0) e 水平位移(0)
> b 水平倾斜(0) d 垂直缩放(1) f 垂直位移(0)

## fillStyle，实现渐变色

### 线性渐变

```
// Step1:
var grd = context.createLinearGrandient(xstart, ystart, xend, yend);

// Step2:
grd.addColorStop(stop, color);

// Step3:
context.fillStyle = grd;
```

### 镜像渐变

```
// Step1:
var grd = context.createRadialGradient(x0, y0, r0, x1, y1, r1);

// Step2:
grd.addColorStop(stop, color);

// Step3:
context.fillStyle = grd;
```

## 使用图片、画布或者video，`createPattern(source, repeat-style)`

source 参数如下：
    * img
    * canvas
    * video

repeat-style 参数如下：
    * no-repeat 不重复
    * repeat-x x轴重复
    * repeat-y y轴重复
    * repeat x,y轴都重复

### 图片

```
var backgroundImage = new Image();
backgroundImage.src = "xx.jpg";
backgroundImage.onload = function() {
    var pattern = context.createPattern(backgroundImage, "no-repeat");
    context.fillStyle = pattern;
    context.fillRect(0, 0, 800, 800);
}
```

### 画布

```
// 创建 长100 宽100 的画布，并在上面绘制了五角星
function createBackgroundCanvas() {
    var backCanvas = document.createElement("canvas");
    backCanvas.width = 100;
    backCanvas.height = 100;
    var backCanvasContext = backCanvas.getContext("2d");
    drawStar(backCanvasContext, 50, 50, 50, 0);
    return backCanvas;
}
```

## arc 与 arcTo

### Draw an Arc

```
context.arc(
    centerx,centery,radius, // 圆心x,y坐标，半径r
    startingAngle, endingAngle, // 开始角度，结束角度
    anticlockwise = false // 是否是逆时针，默认顺时针
)
```

### arcTo

```
context.arcTo(x1, y1, x2, y2, radius);
```

### quadraticCurveTo

```
context.quadraticCurveTo(
    x1, y1, // 起始点
    x2, y2  // 结束点
);
```

### bezeirCurveTo

```
context.bezeirCurveTo(
    x1, y1, x2, y2, // 起始点
    x3, y3          // 结束点
);
```

## 文字渲染

```
context.font = "bold 40px Arial";
context.fillText(string, x, y, /* optional */ width);   // 实体字
context.strokeText(string, x, y, /* optional */ width); // 只有外边框
```

### font

默认值: "20px sans-serif"
    * font-style:
        - normal  // default
        - italic  // 斜体
        - oblique //倾斜字体
    * font-variant:
        - normal     // default
        - small-caps // 小型大写字母形式来显示所有大写字母
    * font-weight:
        - lighter // 更细
        - normal  // default
        - bold    // 粗体
        - bolder  // 更粗
    * font-size:
        - 20px // default
        - 2em
        - 150%
        - xx-small
        - x-small
        - medium
        - large
        - x-large
        - xx-large
    * font-family

### 文本对齐

textAlign:
    * left
    * center
    * right

textBaseline:
    * top
    * middle
    * bottom
    * alphabetic // default 基于拉丁语
    * ideographic // 基于汉字
    * hanging // 基于印度语

```
// 水平
context.textAlign = left;
// 垂直
context.textBaseline = top;
```

### 文本度量

```
context.measureText(string).width // 获取文本宽度
```

## 阴影

```
context.shadowColor   // 阴影颜色
context.shadowOffsetX // 阴影X轴位移值
context.shadowOffsety // 阴影Y轴位移值
context.shadowBlur    // 阴影模糊c程度
```

## global 全局值

globalCompositeOperation:
    * source-over
    * source-atop
    * source-in
    * soucce-out
    * destination-over
    * destination-atop
    * destination-in
    * destination-out
    * lighter
    * copy
    * xor

```
globalAlpha = 1 // default
globalCompositeOperation = source-over // default
```

## 剪辑区域

```
context.clip();
```

## isPointInPath

```
context.isPointInPath(x, y); // x, y 这个坐标是否在路径内
```

## CanvasRenderingContext2D 添加自己的绘制函数

```
CanvasRenderingContext2D.prototype.fillStar = function(r, R, x, y, rot) {
    this.beginPath();
    for (var i = 0; i < 5; i++) {
        this.lineTo(Math.cos((18 + i * 72 - rot) / 180 * Math.PI) * R + x, -Math.sin((18 + i * 72 - rot) / 180 * Math.PI) * R + y);
        this.lineTo(Math.cos((54 + i * 72 - rot) / 180 * Math.PI) * r + x, -Math.sin((54 + i * 72 - rot) / 180 * Math.PI) * r + y);
    }

    this.closePath();
    this.fill();
}
```
