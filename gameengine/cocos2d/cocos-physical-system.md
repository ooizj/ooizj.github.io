# cocos物理系统

下面以让角色站在地面上为例

## 1，启用物理系统

```javascript
var manager = cc.director.getPhysicsManager();

//开启物理系统
manager.enabled = true;

//绘制物理调试信息
manager.debugDrawFlags = cc.PhysicsManager.DrawBits.e_aabbBit |
    cc.PhysicsManager.DrawBits.e_pairBit |
    cc.PhysicsManager.DrawBits.e_centerOfMassBit |
    cc.PhysicsManager.DrawBits.e_jointBit |
    cc.PhysicsManager.DrawBits.e_shapeBit
    ;
```

## 2，添加分组

在项目设置中添加“player”和“floor”两个分组

## 3，为角色Node和地面Node新增RigidBody\(刚体\)和PhysicsBoxCollider\(形状\)

角色刚体类型为Dynamic  
角色Node分组为“player”

地面刚体类型为Static  
地面Node分组为“floor”

## 4，若发现角色移动超出地面边界没有掉落，可以将角色刚体的“Allow Sleep”禁用

