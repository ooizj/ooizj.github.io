# cocos通过script进行绘画

```javascript
//绘制一个矩形
const BOUND = 10 ; 
let n1 = new cc.Node() ; 
//添加绘图组件
var ctx = n1.addComponent(cc.Graphics) ; 
ctx.strokeColor = new cc.Color(255, 0, 0, 120) ; 
ctx.moveTo(BOUND, BOUND);
ctx.lineTo(this.chunkWidth-BOUND, BOUND);
ctx.lineTo(this.chunkWidth-BOUND, this.chunkHeight-2*BOUND);
ctx.lineTo(BOUND, this.chunkHeight-2*BOUND);
ctx.lineTo(BOUND, BOUND);
ctx.stroke();
this.addChild(n1) ;
```

