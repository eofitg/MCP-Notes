# EntityPlayer

### `src/main/java/net/minecraft/entity/player/EntityPlayer.java`

### 玩家实体事件

[EntityLivingBase](../EntityLivingBase.md)

```java
public abstract class EntityPlayer extends EntityLivingBase
```

---

- ```java
  public void onDeath(DamageSource cause)
  ```
  
  生物生命值变为 `0` 时触发，即**死亡检测**
  
  > - `cause` 造成死亡的原因
  
  ```java
  super.onDeath(cause);
  this.setSize(0.2F, 0.2F);
  this.setPosition(this.posX, this.posY, this.posZ);
  this.motionY = 0.10000000149011612D;
  ```
  
  - 特判实体名为 `Notch` 时，额外掉落一个苹果
  
  ```java
  if (this.getName().equals("Notch")) {
      this.dropItem(new ItemStack(Items.apple, 1), true, false);
  }
  ```
  
  - 判断是否**保留物品栏**
  
  ```java
  if (!this.worldObj.getGameRules().getBoolean("keepInventory")) {
      this.inventory.dropAllItems();
  }
  ```
  
  - X
  
  ```java
  if (cause != null) {
      this.motionX = (double)(-MathHelper.cos((this.attackedAtYaw + this.rotationYaw) * (float)Math.PI / 180.0F) * 0.1F);
      this.motionZ = (double)(-MathHelper.sin((this.attackedAtYaw + this.rotationYaw) * (float)Math.PI / 180.0F) * 0.1F);
  } else {
      this.motionX = this.motionZ = 0.0D;
  }
  ```
  
  - X
  
  ```java
  this.triggerAchievement(StatList.deathsStat);
  this.func_175145_a(StatList.timeSinceDeathStat);
  ```

&nbsp;

- ```java
  public float getArmorVisibility()
  ```
  
  **盔甲可见度**，即该实体隐身时被其他实体发现的概率
  
  - 可见度不受盔甲类型影响
  
  - 可见度是**盔甲数量**与**可穿戴盔甲数目**的比值，仅有 $\frac{1}{4},\frac{2}{4},\frac{3}{4},\frac{4}{4}$ 四种可能
  
  ```java
  int i = 0;
  for (ItemStack itemstack : this.inventory.armorInventory) {
      if (itemstack != null) {
          ++i;
      }
  }
  return (float)i / (float)this.inventory.armorInventory.length;
  ```

&nbsp;

- ```java
  public boolean attackEntityFrom(DamageSource source, float amount)
  ```
  
  **实体受击时触发**
  
  > - `source` 伤害来源
  > 
  > - `amount` 伤害值
  
  - 判断是否能造成伤害
    
    - 是否免疫该伤害类型 `例如防火效果`
    
    - 是否可受到伤害 (免伤状态 / 创造模式)
  
  ```java
  if (this.isEntityInvulnerable(source)) {
      return false;
  } else if (this.capabilities.disableDamage && !source.canHarmInCreative()) {
      return false;
  } else { // can cause damage
  ```
  
  - 能造成伤害则判断玩家是否已经死亡或在睡觉
    
    - 生命值小于等于 `0.0F` 视为死亡，**取消本次伤害**
    
    - 在睡觉则强制叫醒**再继续伤害运算**
    
    ```java
    this.entityAge = 0;
    if (this.getHealth() <= 0.0F) { // target has dead
        return false;
    } else { // target alive
        if (this.isPlayerSleeping() && !this.worldObj.isRemote) {
            this.wakeUpPlayer(true, true, false);
        }
    ```
  
  - 根据**难度设定**计算应受到的伤害，**和平模式则取消本次伤害**
    
    - **伤害计算公式** 
      
      $\ dmg =$
      
      - `NORMAL` 或未设定难度 : $\ dmg$
      
      - `PEACEFUL` : $\ 0$
      
      - `EASY` : $\ dmg/2+1$
        
        > 保底 `1.00F` 伤害
        > 
        > - 避免了游戏精度不足造成的伤害折损，使该有效的伤害必然有效
        > 
        > - 保证了执行完判断后 `0.0F` 必然代表和平模式
      
      - `HARD` : $\ dmg * 1.5$
      
      ```java
      if (source.isDifficultyScaled()) {
          if (this.worldObj.getDifficulty() == EnumDifficulty.PEACEFUL) {
              amount = 0.0F;
          }
          if (this.worldObj.getDifficulty() == EnumDifficulty.EASY) {
              amount = amount / 2.0F + 1.0F;
          }
          if (this.worldObj.getDifficulty() == EnumDifficulty.HARD) {
              amount = amount * 3.0F / 2.0F;
          }
      }
      if (amount == 0.0F) { // peaceful mode
          return false;
      }
      ```
  
  - 最终造成伤害
    
    - 特判伤害来源是否是箭矢
      
      > 这个特判实际无意义，或许是更新时遗留下来忘记删除的部分
      
      [EntityLivingBase.attackEntityFrom](../EntityLivingBase.md)
      
      ```java
      else {
          Entity entity = source.getEntity();
          if (entity instanceof EntityArrow && ((EntityArrow)entity).shootingEntity != null) {
              entity = ((EntityArrow)entity).shootingEntity;
          }
          return super.attackEntityFrom(source, amount);
      }
      ```

```java
    }
}
```

&nbsp;

- 

---

&nbsp;
