# IChatComponent

### `src/main/java/net/minecraft/util/IChatComponent.java`

### 聊天组件接口

```java
public interface IChatComponent extends Iterable<IChatComponent>
```

- [ChatComponentStyle](ChatComponentStyle.md)

---

- ```java
  String getUnformattedTextForChat();
  ```
  
  **为聊天信息获取未格式化的文本**
  
  > // Gets the text of this component, without any special formatting codes added, for chat.  
  > 
  > // TODO: why is this two different methods?

- ```java
  String getUnformattedText();
  ```
  
  **获取未格式化的文本**
  
  > // Get the text of this component, <em>and all child components</em>, with all special formatting codes removed.

&nbsp; 

- 

---

&nbsp; 
