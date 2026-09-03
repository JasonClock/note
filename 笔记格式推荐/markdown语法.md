上标  ^上标^

~~outline~~

*斜体*

**粗体**

==highlight==

![photo](.\地址)

[名称](网址)

- [ ] task

# 
## 
### 
#### 

> dsaf
>


分割线

-----------------

[MarkDown支持Emoji表情 - OWIeruy - 博客园](https://www.cnblogs.com/wutongxue132/p/16684085.html)

[Markdown 语法速查表 | Markdown 教程](https://markdown.com.cn/cheat-sheet.html#总览)

Mermaid 的语法和特性

Mermaid 的语法简洁直观，支持定义多种类型的图表结构，如流程图、时序图、类图、状态图、实体关系图等。用户可以通过简单的文本命令来声明图表的元素和它们之间的关系，Mermaid 会根据这些描述自动生成图表。

例如，要创建一个流程图，用户可以使用如下 Mermaid 代码：
``` mermaid
graph LR

A[开始] --> B[过程]

B --> C[判断]

C -->|是| D[结束]

C -->|否| B
```

这段代码定义了一个从“开始”到“过程”，再到“判断”的流程，根据“判断”的结果，流程可能会结束，也可能会返回到“过程”继续执行。

Mermaid 还支持为图表元素添加样式、定义子图表、使用 fontawesome 图标、添加注释和标签、定义循环和条件语句等高级功能。这些特性使得 Mermaid 成为一个功能强大且灵活的图表工具。