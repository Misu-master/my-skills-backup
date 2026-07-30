---
name: git-commit-message
description: "生成符合规范的 Git 提交备注。当用户说写git备注、git commit message、提交备注、commit message时使用。支持单个文件或批量多个文件生成备注。格式为：类型(作用域): 备注内容，正文详细说明变更点。"
---

# Git Commit Message 生成器

根据用户提供的文件路径和代码变更，生成符合 Conventional Commits 规范的中文提交备注。

## 触发场景

- 用户说"写git备注"、"git commit message"、"提交备注"、"commit message"
- 用户提供文件路径或代码片段，需要生成提交说明
- 用户完成代码修改后需要规范的提交信息

## 输出格式

### 单个文件
```
<类型>(<作用域>): <简短描述>

<详细说明>
```

### 多个文件
为每个文件单独生成备注，并标注文件名：

**filename.py**
```
<类型>(<作用域>): <简短描述>

<详细说明>
```

**another_file.py**
```
<类型>(<作用域>): <简短描述>

<详细说明>
```

## 提交类型

| 类型 | 说明 | 示例 |
|------|------|------|
| `feat` | 新功能 | feat(api): 新增用户认证接口 |
| `fix` | 修复 bug | fix(auth): 修复 JWT token 过期问题 |
| `refactor` | 重构（非功能/非修复） | refactor(db): 优化数据库连接池管理 |
| `docs` | 文档变更 | docs(readme): 补充安装说明 |
| `style` | 代码格式（不影响功能） | style(lint): 统一缩进和引号风格 |
| `test` | 新增或修改测试 | test(auth): 补充登录失败用例 |
| `chore` | 构建/工具/依赖变更 | chore(deps): 升级 pydantic 到 2.0 |
| `perf` | 性能优化 | perf(query): 使用索引优化查询速度 |
| `ci` | CI/CD 配置变更 | ci(github): 添加自动化测试 workflow |

## 作用域规则

**一般规则：使用具体文件名（不含扩展名）作为作用域**

| ✅ 正确 | ❌ 错误 |
|---------|---------|
| feat(base_model) | feat(core) |
| fix(config) | fix(settings) |
| refactor(database) | refactor(db) |
| feat(resources) | feat(manager) |

**例外：`__init__.py` 文件使用模块名**

对于包初始化文件 `__init__.py`，使用其所在目录的模块名作为作用域：

| 文件路径 | ✅ 作用域 | ❌ 作用域 |
|----------|----------|----------|
| `src/core/__init__.py` | core | __init__ |
| `src/integrations/__init__.py` | integrations | __init__ |
| `src/middlewares/__init__.py` | middlewares | __init__ |

**示例**：
```
feat(core): 新增核心模块包初始化文件

定义核心基础设施模块的包标识，包含配置、数据库、异常处理、资源管理等全局基础组件。
```

## 备注内容规范

### 首行（必填）
- 格式：`<类型>(<作用域>): <描述>`
- 长度：不超过 50 个字符
- 语言：中文
- 时态：使用现在时，如"新增"、"修复"、"优化"

### 正文（可选但推荐）
- 与首行空一行
- 详细说明变更内容、原因、影响范围
- 每行不超过 72 个字符
- 使用列表形式更清晰

## 示例

### 示例 1：新增功能
**输入**：`base_model.py` 新增了 Base 类和 DateTimeMixin

**输出**：
```
feat(base_model): 新增 SQLite 数据库基础模型和时间戳混入类

定义全局统一的数据库命名约定、DeclarativeBase 基类和 DateTimeMixin，
为所有 ORM 模型提供 created_at/updated_at 自动时间戳功能。
```

### 示例 2：修复问题
**输入**：`middlewares.py` 修复了信号量泄漏和计数器竞态条件

**输出**：
```
fix(middlewares): 修复并发计数器的竞态条件和信号量泄漏

- 使用 asyncio.Lock 保护 active_requests 计数器
- 改用 async with 自动管理信号量获取和释放
- 修复 finally 块中日志显示不准确的问题
```

### 示例 3：批量文件
**输入**：同时提供 `config.py` 和 `database.py`

**输出**：

**config.py**
```
feat(config): 新增应用配置模块，支持多数据库和飞书配置

使用 pydantic-settings 从 .env 读取配置，支持 MySQL/MSSQL/Redis/飞书
的配置解析，包含空值自动转换为 None 的验证逻辑。
```

**database.py**
```
feat(database): 新增 SQLite 异步引擎和会话管理

创建异步数据库引擎、SessionFactory、依赖注入函数 get_db()，
以及自动创建数据库目录和表的工具函数。
```

## 工作流程

1. **接收输入**：用户提供的文件路径和/或代码变更
2. **分析上下文**：读取文件内容，理解变更意图
3. **判断类型**：根据变更性质选择正确的提交类型
4. **确定作用域**：使用文件名（不含扩展名）作为作用域
5. **生成备注**：首行简洁，正文详细
6. **输出结果**：按格式输出，多文件时分别标注

## 注意事项

- 始终使用中文
- 作用域必须是具体文件名
- 首行不超过 50 字符
- 正文每行不超过 72 字符
- 多文件时每个文件单独生成，不要合并成一个备注
- 如果用户没有提供足够信息，可以询问更多上下文