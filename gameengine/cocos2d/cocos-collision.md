# cocos碰撞检测

## 1，启用全局碰撞检测

```javascript
// 获取碰撞检测系统
var manager = cc.director.getCollisionManager();

// 默认碰撞检测系统是禁用的，如果需要使用则需要以下方法开启碰撞检测系统
manager.enabled = true;

// 默认碰撞检测系统的 debug 绘制是禁用的，如果需要使用则需要以下方法开启 debug 绘制：x
manager.enabledDebugDraw = true;

// 如果还希望显示碰撞组件的包围盒，那么可以通过以下接口来进行设置：
manager.enabledDrawBoundingBox = true;
```

## 2，添加碰撞分组

![image](https://raw.githubusercontent.com/ooizj/ooizj.github.io/master/gameengine/cocos2d/img/%E6%B7%BB%E5%8A%A0%E5%88%86%E7%BB%84.PNG)

## 3，对Node设置刚才添加的碰撞分组并新增包围盒组件

![image](https://raw.githubusercontent.com/ooizj/ooizj.github.io/master/gameengine/cocos2d/img/%E8%AE%BE%E7%BD%AE%E5%88%86%E7%BB%84.PNG)  
使用脚本动态设置分组并新增包围盒

```javascript
let bulletNode = new cc.Node() ; 
bulletLayer.addChild(bulletNode) ; 
//设置分组
bulletNode.group = 'player-bullet' ; 
bulletNode.x = startX ; 
bulletNode.y = startY ; 

//绘制精灵
let bulletSprite = bulletNode.addComponent(cc.Sprite) ; 
bulletSprite.spriteFrame = new cc.SpriteFrame(this.bulletTexture) ;         

//添加包围盒
let bulletBox = bulletNode.addComponent(cc.BoxCollider) ; 
bulletBox.offset = new cc.v2(0, 0) ; 
bulletBox.size = new cc.size(16, 16) ; 

//发射
bulletNode.runAction(
    cc.bezierTo(2, [cc.v2(startX, startY), cc.v2(500, 800), cc.v2(800, 100)])
) ;
```

## 4，对Node添加脚本用来添加碰撞事件处理方法

```javascript
/**
 * 当碰撞产生的时候调用
 * @param  {Collider} other 产生碰撞的另一个碰撞组件
 * @param  {Collider} self  产生碰撞的自身的碰撞组件
 */
onCollisionEnter: function (other, self) {
    cc.log('enemy enter');

    // 碰撞系统会计算出碰撞组件在世界坐标系下的相关的值，并放到 world 这个属性里面
    var world = self.world;

    // 碰撞组件的 aabb 碰撞框
    var aabb = world.aabb;

    // 节点碰撞前上一帧 aabb 碰撞框的位置
    var preAabb = world.preAabb;

    // 碰撞框的世界矩阵
    var t = world.transform;

    // 以下属性为圆形碰撞组件特有属性
    var r = world.radius;
    var p = world.position;

    // 以下属性为 矩形 和 多边形 碰撞组件特有属性
    var ps = world.points;

    cc.log(other.node, self.node);
},
// /**
//  * 当碰撞产生后，碰撞结束前的情况下，每次计算碰撞结果后调用
//  * @param  {Collider} other 产生碰撞的另一个碰撞组件
//  * @param  {Collider} self  产生碰撞的自身的碰撞组件
//  */
// onCollisionStay: function (other, self) {
//     // console.log('on collision stay');
// },
/**
 * 当碰撞结束后调用
 * @param  {Collider} other 产生碰撞的另一个碰撞组件
 * @param  {Collider} self  产生碰撞的自身的碰撞组件
 */
onCollisionExit: function (other, self) {
    // cc.log('enemy exit');
}
```

