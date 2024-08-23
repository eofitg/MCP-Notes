# CommandSummon

### `src/main/java/net/minecraft/command/server/CommandSummon.java`

### /summon 指令

![summon uasge](/Users/erou/Documents/MCP-Notes/1.8.9/src.main.java.net.minecraft/command/server/summonable-entities.png)

[CommandBase](../CommandBase.md)

```java
public class CommandSummon extends CommandBase
```

- ```java
  public void processCommand(ICommandSender sender, String[] args)
  throws CommandException
  ```
  
   **指令运行过程**
  
  - 滤除无效指令 `/summon` 
  
  ```java
  if (args.length < 1) {
      throw new WrongUsageException("commands.summon.usage", new Object[0]);
  }
  ```
  
  - 确认基本信息
    
    - 目标实体ID `s = args[0]`
    
    - 默认生成坐标 `d0, d1, d2` ，即**指令发送者的坐标**
  
  ```java
  else {
      String s = args[0]; // entity id
      BlockPos blockpos = sender.getPosition();
      Vec3 vec3 = sender.getPositionVector();
      double d0 = vec3.xCoord;
      double d1 = vec3.yCoord;
      double d2 = vec3.zCoord;
  ```
  
  - 如果指令指定了生成坐标则替换坐标
    
    ```java
    if (args.length >= 4) { // summon [entity] [x] [y] [z] ...
        d0 = parseDouble(d0, args[1], true);
        d1 = parseDouble(d1, args[2], false);
        d2 = parseDouble(d2, args[3], true);
        blockpos = new BlockPos(d0, d1, d2);
    }
    ```
  
  - 检验该坐标所在区块在该世界是否加载
    
    ```java
    World world = sender.getEntityWorld();
    if (!world.isBlockLoaded(blockpos)) {
        throw new CommandException("commands.summon.outOfWorld", new Object[0]);
    }
    ```
  
  - **特判闪电束**
    
    - 是因为**闪电束和其他实体的生成方式不同**，还是说只是**刚加入召唤闪电束的指令时所采用的临时方案**？
    
    - 虽然后者很可疑，但就算是前者似乎也反映了 `Mojang` 当时对实体类型还没有一个明确的规范?
      
      > **LightningBolt** 的特点
      > 
      > - 相较于其他所有实体，无需检测交互
      > 
      > - 相较于大多数实体，闪电可以自然消失，无需追踪
      > 
      > - 相较于 PrimedTNT ，无需检测生效条件
    
    ```java
    else if ("LightningBolt".equals(s)) {
        world.addWeatherEffect(new EntityLightningBolt(world, d0, d1, d2));
        notifyOperators(sender, this, "commands.summon.success", new Object[0]);
    } 
    // ingore
    else {
        NBTTagCompound nbttagcompound = new NBTTagCompound();
        boolean flag = false;
    ```
  
  - 如果指定了NBT信息
    
    - 解析且仅解析第四位参数
      
      > [CommandBase.
      > 
      > getChatComponentFromNthArg](../CommandBase.md): 获取第N项参数及以后的聊天信息, 视为一个整体成分返回
      > 
      > [getUnformattedText](../../util/ChatComponentStyle.md): 获取未格式化的文本
      > 
      > [JsonToNBT.getTagFromJson]()
      
      ```java
      if (args.length >= 5) {
          // summon [entity] [x] [y] [z] [nbt] ...
          IChatComponent ichatcomponent = getChatComponentFromNthArg(sender, args, 4);
          try {
              nbttagcompound = JsonToNBT.getTagFromJson(ichatcomponent.getUnformattedText());
              flag = true;
          } catch (NBTException nbtexception) {
              throw new CommandException("commands.summon.tagError", new Object[] {nbtexception.getMessage()});
          }
      }
      ```
