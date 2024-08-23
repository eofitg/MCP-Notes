# ChatComponentStyle

### `src/main/java/net/minecraft/util/ChatComponentStyle.java`

### 聊天组件样式

[IChatComponent](IChatComponent.md)

```java
public abstract class ChatComponentStyle implements IChatComponent
```

- [ChatComponentScore](ChatComponentScore.md)

- [ChatComponentSelector](ChatComponentSelector.md)

- [ChatComponentText](ChatComponentText.md)

- [ChatComponentTranslation](ChatComponentTranslation.md)

---

- ```java
  public final String getUnformattedText()
  ```
  
  **获取未格式化的文本**
  
  - 采用 `StringBuilder` 类进行字符串操作
  
  ```java
  StringBuilder stringbuilder = new StringBuilder();
  for (IChatComponent ichatcomponent : this) {
    stringbuilder.append(ichatcomponent.getUnformattedTextForChat());
  }
  return stringbuilder.toString();
  ```

---

&nbsp;
