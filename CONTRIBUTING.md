# CONTRIBUTING

## 日志分类标准

遵循业务活动与知识发现的分离原则：

**两个主线**：
- 业务活动记录：记录"发生了什么"（course、qtclass等）
- 知识发现记录：从业务活动中提炼"学到了什么知识"（knowl）

**目录结构**：
- 第一级：业务领域（course、knowl、qtclass）
- 第二级：具体项目/客户（qtclass下的vip/zstu，knowl下的qtclass/qtdata）
- 第三级：日期日志（YYYY-MM-DD.md）

**命名规范**：
- 短标识命名：qtclass、qtdata、vip、zstu
- 每个目录必有README.md说明用途

**示例**：
同一日期可能有两个日志：
- course/2026-04-07.md：记录与CEO讨论课程设计（业务活动）
- knowl/qtclass/2026-04-07.md：提炼课程设计的理论框架（知识发现）

## 提交规范

使用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：`<type>: <description>`

### 提交类型

- feat：新功能
- fix：修复 bug
- docs：文档更新
- test：测试相关
- refactor：代码重构
- chore：构建/工具