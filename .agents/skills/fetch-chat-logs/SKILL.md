---
name: fetch-chat-logs
description: 更新工作日志时使用。用 lark-cli 按群名检索飞书群并拉取聊天消息，按业务线分流写入 journal 日志文件，包含脱敏规则、日志写法与提交流程。
---

# 拉取飞书群消息更新工作日志

当用户要求「更新日志」「拉取群消息记录到日志」时，按本流程执行：按群名解析群 ID → 拉取指定时段消息 → 按业务线分流写入日志 → 提交。

## 红线：敏感信息不入仓库

本仓库是公开仓库。以下内容**禁止写入任何文件、禁止出现在提交中**：

- 真实 ID：`oc_` 开头的 chat_id、`ou_` 开头的 user_id、open_id、union_id 等
- 真实姓名：一律替换为职务代称（如「创始人」「CEO 助理」「秘书」）；代称映射关系向维护者确认或从既有日志推断，映射表本身也不入仓库
- 经营敏感信息：请假/病假、工作交接、项目进度、回款、薪资、内部数据
- 内部链接：飞书文档详情、内部系统地址
- 成员个人数据：引用任何人的个人数据须当事人同意

**允许出现**：群名（如「量潮实训基地」「闲聊水库」）。

身份不确定时按高保密处理——宁可不写职务，也不猜测归属。

## 第 1 步：按群名解析 chat_id

不在仓库中存储或写死任何 chat_id，每次运行时用群名检索：

```bash
lark-cli im +chat-search --query "实训基地" --jq '[.data.chats[] | {name: .name, chat_id: .chat_id}]'
```

- 返回结构：`.data.chats[]`，字段 `name` 和 `chat_id`
- 结果多于一个时按群名精确匹配；结果为空时换更短的关键词重试
- 解析出的 chat_id 只用于本次命令执行，不写入任何文件

## 第 2 步：拉取消息

```bash
lark-cli im +chat-messages-list --chat-id <第1步解析的chat_id> \
  --start 2026-09-09T00:00:00+08:00 --end 2026-09-10T00:00:00+08:00 \
  --order asc --page-size 50 --no-reactions \
  --jq '[.data.messages[] | {t: .create_time, s: (.sender.name // "system"), ty: .msg_type, c: .content}]'
```

要点：

- 所有 flag 都属于 `+chat-messages-list` 子命令，放在它后面
- `--start` / `--end` 用 ISO 8601 格式且必须带时区（`+08:00`）
- JSON 路径是 `.data.messages[]`（不是 `.data.items[]`）
- 系统消息的 sender 为 null，jq 中用 `(.sender.name // "system")`
- 默认排序 `desc`（新的在前）；补记历史日志用 `--order asc` 按时间正序读
- 超过 50 条时用返回中的 `page_token` 加 `--page-token` 翻页，直到 `has_more` 为 false

## 第 3 步：按业务线分流

| 来源群 | 归属目录 | 说明 |
|--------|---------|------|
| 量潮实训基地 | `qtclass/` | 培养体系（默认） |
| 闲聊水库 | 按内容判断 | 培养相关→qtclass，产品研发→qtcloud |
| 无法归类到具体业务 | `default/` | 跨群、公司级、无明确业务线的内容 |

同一业务线同一天只保留一个日志文件；若已存在 `YYYY-MM-DD.md`，把新内容合并进去（标注来源群与时间），不新建 `-2` 文件。

## 第 4 步：写日志

- 文件路径：`<业务线>/YYYY-MM-DD.md`；文件顶部写「来源：…群」
- 每个章节标题标注来源群与时间，如 `## 标题（闲聊水库群 18:44 ~ 18:45）`
- 写法：**事实 + 原话引述**——保留说话人的真实语气（用引号），不做没有出处的推断
- 复盘、背景注释用引用块（`>`）与正文区分
- 提炼共识、跨日志的主题归纳**不写入 journal**——journal 只做实录；洞察写入 insight 仓库，演化叙事写入 history 仓库

## 第 5 步：提交

journal 是独立仓库，也可能作为主仓库（quanttide-tech）的子模块挂载：

```bash
# 1. journal 子仓库内
git add -A
git commit -m "docs: <日期> <业务线>——<一句话内容摘要>"
git push

# 2. 回主仓库同步指针（若作为子模块挂载）
git add data/journal
git commit -m "chore: update journal submodule"
git push
```

遵循 Conventional Commits；子模块指针同步是常规操作。

## 常见坑

- jq 报错或输出为空：先去掉 `--jq` 看原始 JSON 结构，再写过滤表达式——不要猜路径
- `--start` 写成 `--start-time` 会报错；时间漏掉时区会报错或静默错位
- 网络/TLS 偶发超时：`sleep 10` 后重试即可，无需改命令
