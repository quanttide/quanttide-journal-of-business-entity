# CONTRIBUTING

## 日志分类标准

日志按业务领域分类，每个领域一个目录，按日期记录。

**目录结构**：
- 第一级：活跃业务领域（qtclass、qtcloud、qtdata）
- 第二级：日期日志（YYYY-MM-DD.md）

**目录选择原则**：
- 目录结构是真实业务的镜像：只有正在产生业务数据的业务线才有活跃目录
- 工具、机制、支撑性业务（管理后台、招聘、众包、黄页等）不单独立目录
- 实训基地群聊记录归 `qtclass`（实训基地是教学用途，归入培养体系）
- 已归档业务线的日志统一放 `data/archive/journal/` 对应目录，内容不删除、只迁移

**命名规范**：
- 目录名以 `qt` 开头，对应业务线标识
- 文件名统一为 `YYYY-MM-DD.md`

**日志内容规范**：
- 记录事实：什么时间、什么人、什么事
- 记录决策：为什么这么做、有什么依据
- 记录结果：做完了还是没做完、后续安排
- 避免空泛评价，用具体证据支撑观点

## 提交规范

使用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：`<type>: <description>`

### 提交类型

- feat：新功能
- fix：修复 bug
- docs：文档更新
- test：测试相关
- refactor：代码重构
- chore：构建/工具