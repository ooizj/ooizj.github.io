
### 通过script添加sprite、label等

添加各种组件的方式步骤如下  
1，创建一个Node  
2，Node添加组件  
3，添加Node  

```js
//添加一个sprite、一个label和绘图组件示例  
cc.Class({
    extends: cc.Node,

    properties: {
        texture: {
            default: null,
            type: cc.Texture2D
        }
    },
    
    init () {        

        //添加sprite
        let sf = new cc.SpriteFrame(this.texture, new cc.Rect(0, 0, 200, 200)) ; 
        let n2 = new cc.Node() ; 
        let sprite = n2.addComponent(cc.Sprite) ; 
        sprite.spriteFrame = sf ; 
        n2.anchorX = 0 ; 
        n2.anchorY = 0 ; 
        this.addChild(n2) ; 

        //添加label
        let n1 = new cc.Node() ; 
        let l = n1.addComponent(cc.Label) ; 
        l.string = 'hhh' ; 
        n1.x = 70 ; 
        n1.y = 100 ; 
        this.addChild(n1) ; 
        
        //添加绘图组件
        const BOUND = 10 ; 
        let n3 = new cc.Node() ; 
        var ctx = n3.addComponent(cc.Graphics) ; 
        ctx.strokeColor = new cc.Color(255, 0, 0, 120) ; 
        ctx.moveTo(BOUND, BOUND);
        ctx.lineTo(this.chunkWidth-BOUND, BOUND);
        ctx.lineTo(this.chunkWidth-BOUND, this.chunkHeight-2*BOUND);
        ctx.lineTo(BOUND, this.chunkHeight-2*BOUND);
        ctx.lineTo(BOUND, BOUND);
        ctx.stroke();
        this.addChild(n3) ; 

    }
});

```
