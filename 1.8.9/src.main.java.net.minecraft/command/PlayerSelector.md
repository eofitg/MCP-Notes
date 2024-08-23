# PlayerSelector

### `src/main/java/net/minecraft/command/PlayerSelector.java`

### 玩家选择器

```java
public class PlayerSelector
```

- `public static IChatComponent`
  
  `matchEntitiesToChatComponent(ICommandSender sender, String token)`
  
  **匹配实体到聊天成分**
  
  - X
  
  ```java
  List<Entity> list = matchEntities(sender, token, Entity.class);
  if (list.isEmpty()) {
      return null;
  }
  else {
      List<IChatComponent> list1 = Lists.<IChatComponent>newArrayList();
      for (Entity entity : list) {
          list1.add(entity.getDisplayName());
      }
      return CommandBase.join(list1);
  }
  ```

- `public static <T extends Entity> List<T>`
  
  `matchEntities(ICommandSender sender, String token, Class <? extends T > targetClass)`
  
  **匹配实体**
  
  - X
  
  ```java
  Matcher matcher = tokenPattern.matcher(token);
  if (matcher.matches() && sender.canCommandSenderUseCommand(1, "@")) {
      Map<String, String> map = getArgumentMap(matcher.group(2));
      if (!isEntityTypeValid(sender, map)) {
          return Collections.<T>emptyList();
      } 
      else {
          String s = matcher.group(1);
          BlockPos blockpos = func_179664_b(map, sender.getPosition());
          List<World> list = getWorlds(sender, map);
          List<T> list1 = Lists.<T>newArrayList();
          for (World world : list) {
              if (world != null) {
                  List<Predicate<Entity>> list2 = Lists.<Predicate<Entity>>newArrayList();
                  list2.addAll(func_179663_a(map, s));
                  list2.addAll(getXpLevelPredicates(map));
                  list2.addAll(getGamemodePredicates(map));
                  list2.addAll(getTeamPredicates(map));
                  list2.addAll(getScorePredicates(map));
                  list2.addAll(getNamePredicates(map));
                  list2.addAll(func_180698_a(map, blockpos));
                  list2.addAll(getRotationsPredicates(map));
                  list1.addAll(filterResults(map, targetClass, list2, s, world, blockpos));
              }
          }
          return func_179658_a(list1, map, sender, targetClass, s, blockpos);
      }
  }
  else {
      return Collections.<T>emptyList();
  }
  ```

- 
