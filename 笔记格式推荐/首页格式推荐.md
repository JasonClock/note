# 欢迎回来 &#x1f3e0;

## 快速入口

- [[00-日记/{{date}}|今日日记]]
- [[收件箱]]
- [[项目仪表盘]]

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