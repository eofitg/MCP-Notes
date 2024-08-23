# CommandBase

### `src/main/java/net/minecraft/command/CommandBase.java`

### 指令系统底层

[ICommand](ICommand.md)

```java
public abstract class CommandBase implements ICommand
```

- `public static IChatComponent`
  
  `getChatComponentFromNthArg(ICommandSender sender, String[] args, int p_147178_2_)` 
  
  `throws CommandException, PlayerNotFoundException`
  
  最后一个参数是 `false` 的 `getChatComponentFromNthArg` 
  
  ```java
  return getChatComponentFromNthArg(sender, args, p_147178_2_, false);
  ```

- `public static IChatComponent`
  
  `getChatComponentFromNthArg(ICommandSender sender, String[] args, int index, boolean p_147176_3_)`
  
  `throws PlayerNotFoundException`
  
  **获取第N项参数及以后的聊天信息, 视为一个整体成分返回**
  
  - 遍历自指定下标往后 `args` 包含的每一个参数 `[index, args.length)`
  
  ```java
  IChatComponent ichatcomponent = new ChatComponentText("");
  for (int i = index; i < args.length; ++i) {
  ```
  
  - 在 `index` 后的每一个参数前添加一个空格
    
    ```java
    if (i > index) {
        ichatcomponent.appendText(" ");
    }
    IChatComponent ichatcomponent1 = new ChatComponentText(args[i]);
    ```
  
  - 猜测 `p_147176_3` 用于判断该指令末尾是否会出现玩家名
    
    - 如果需要读取玩家名则检验玩家名是否有效，无效直接 `throw`
      
      [PlayerSelector.matchEntitiesToChatComponent](PlayerSelector.md)
    
    ```java
    if (p_147176_3_) {
        IChatComponent ichatcomponent2 = PlayerSelector.matchEntitiesToChatComponent(sender, args[i]);
        if (ichatcomponent2 == null) {
            if (PlayerSelector.hasArguments(args[i])) {
                throw new PlayerNotFoundException();
            }
        }
        else {
            ichatcomponent1 = ichatcomponent2;
        }
    }
    ichatcomponent.appendSibling(ichatcomponent1);
    ```
  
  ```java
  }
  return ichatcomponent;
  ```

- 
