# 欢迎回来 &#x1f3e0;

## 快速入口

- [[Linux学习/00-日记/{{date}}|今日日记]]
- [[/linux学习/收件箱]]
- [[项目仪表盘]]
## 笔记结构
Vault/
├── 00-日记/              # Daily Notes 自动存放
├── 10-[[linux学习]]          
├── 20-[[Linux学习_2]]         
├── 30-资源/             
├── 40-归档/             
├── Templates/           
├── attachments
└── 主页.md             
## 当前项目

```dataview
TABLE status, deadline
FROM "10-项目"
WHERE status = "进行中"
```

## 未完成任务

```dataview
TASK
FROM "00-日记"
WHERE !completed
LIMIT 10
```

## 最近更新的笔记

```dataview
TABLE file.mtime as "最后修改"
FROM ""
SORT file.mtime DESC
LIMIT 5
```