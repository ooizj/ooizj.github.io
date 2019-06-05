

```js

let sf = new cc.SpriteFrame(this.texture, new cc.Rect(0, 0, 300, 300)) ; 

let node = new cc.Node() ; 
let sprite = node.addComponent(cc.Sprite) ; 
sprite.spriteFrame = sf ; 
cc.director.getScene().addChild(node) ; 
```
