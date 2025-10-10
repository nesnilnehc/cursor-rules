# 关于 Cursor 规则

Cursor 规则是控制 AI 代理模型行为的可重用、有作用域的指令。规则允许您为项目或个人提供系统级指导，是编码上下文、偏好或工作流程的持久化方式。

## 快速开始

### 作为 Git Submodule 使用

将本规则库作为 git submodule 添加到您的项目中：

```bash
git submodule add git@github-nesnilnehc:nesnilnehc/cursor-rules.git .cursor
```

添加后，您的项目结构将变为：

```text
your-project/
  .cursor/              # Cursor 规则目录
    rules/              # 规则文件
    README.md           # 规则说明文档
  src/
  package.json
  ...
```

### 克隆包含 Submodule 的项目

如果您克隆的项目已经包含了这个 submodule，请执行以下命令来初始化：

```bash
git clone <your-project-url>
cd <your-project>
git submodule update --init --recursive
```

### 更新规则

当规则库有更新时，您可以通过以下命令更新：

```bash
git submodule update --remote .cursor
```

或者进入 `.cursor` 目录手动更新：

```bash
cd .cursor
git pull origin main
cd ..
git add .cursor
git commit -m "Update cursor rules"
```

## 规则工作原理

大语言模型在完成任务之间不会保留记忆。规则通过在提示级别提供持久、可重用的上下文来解决这个问题。当应用规则时，其内容会包含在模型上下文的开始部分，为 AI 提供一致的指导。

规则适用于聊天（Chat）和内联编辑（Cmd/Ctrl + K）功能。

## 规则类型

Cursor 支持以下几种规则类型：

### 1. 项目规则（Project Rules）

- 存储在 `.cursor/rules` 目录中
- 版本控制管理，作用域限定在代码库内
- 支持路径模式匹配和相关性判断

### 2. 用户规则（User Rules）  

- 全局应用于 Cursor 环境
- 在设置中定义，始终生效
- 适用于个人偏好和通用指导

### 3. 记忆（Memories）

- 基于聊天对话自动生成的规则
- 智能学习和适应开发模式

## 项目规则详解

### 规则文件格式

每个规则文件使用 **MDC** (`.mdc`) 格式编写，这是一种支持元数据和内容的轻量级格式。

#### 规则触发类型

| 类型 | 说明 |
|------|------|
| **Always** | 始终包含在模型上下文中 |
| **Auto Attached** | 当引用匹配 glob 模式的文件时自动包含 |
| **Agent Requested** | AI 决定是否包含，必须提供描述 |
| **Manual** | 仅在使用 @ruleName 显式提及时包含 |

#### MDC 规则示例

```markdown
---
description: RPC 服务样板
globs: ["**/*.service.ts", "**/*.rpc.ts"]
alwaysApply: false
---

- 定义服务时使用我们的内部 RPC 模式
- 服务名称始终使用 snake_case 命名

@service-template.ts
```

### 嵌套规则

可以在项目结构中的多个 `.cursor/rules` 目录中组织规则：

```
project/
  .cursor/rules/        # 项目级规则
  backend/
    server/
      .cursor/rules/    # 后端特定规则
  frontend/
    .cursor/rules/      # 前端特定规则
```

嵌套规则特点：

- 当引用其目录中的文件时自动附加
- 在上下文选择器和 AI 可访问规则列表中仍然可用
- 适合在单体仓库或具有不同组件的项目中使用

## 目录结构

### workflow/ - 开发工作流程规范

包含开发过程中需要遵循的工作流程和协议：

- **import-management.mdc** - 代码重构时的引用管理协议
  - 规范重构过程中 import/using 语句的同步更新流程
  - 确保代码修改后引用关系的正确性

### documentation/ - 文档编写规则

包含所有与文档编写相关的规范和格式要求：

- **chinese-copywriting.mdc** - 中文文案排版规则
  - 中英文混排的空格使用规范
  - 标点符号的正确使用
  - 用户界面文案规范

- **documentation-management.mdc** - 文档管理策略
  - 文档创建决策树
  - 临时文档生命周期
  - 文档清理策略

- **markdownlint/** - Markdown 格式规范
  - **md009-trailing-spaces.mdc** - 行末空格规则
  - **md031-blanks-around-fences.mdc** - 围栏代码块前后空行规则  
  - **md032-lists-blank-lines.mdc** - 列表前后空行规则

### standards/ - 编码标准规范

包含通用的编码标准和原则（可跨项目复用）：

- **coding-principles.mdc** - 通用编码原则（跨语言）
  - 代码组织原则
  - 注释原则
  - 命名规范
  - 错误处理
  - 日志输出
  - 代码简洁性
  - 重构原则

- **shell-coding.mdc** - Shell 脚本编码标准
  - 脚本基本结构
  - 日志规范（统一使用日志函数）
  - 函数注释格式
  - 变量命名和引用
  - 代码风格
  - 常见模式

### tools/ - 工具使用说明

包含开发工具的使用注意事项和限制说明：

- **list-dir-dotfiles-limitation.mdc** - list_dir 工具的点文件限制
  - 工具行为说明
  - 替代方案
  - 最佳实践

## 使用方式

### 基本用法

1. **自动应用** - 根据文件类型和路径自动触发相关规则
2. **手动引用** - 使用 `@ruleName` 在对话中显式引用特定规则
3. **智能建议** - AI 根据上下文智能推荐相关规则

### 规则管理

- **创建规则**: 使用 `Cmd + Shift + P` > "New Cursor Rule" 命令
- **查看规则**: 在 `Cursor Settings > Rules` 中查看所有规则状态
- **生成规则**: 使用 `/Generate Cursor Rules` 命令从对话生成规则

这些规则文件会被 Cursor AI 自动读取和应用，帮助在开发过程中：

1. **代码审查** - 提供统一的代码质量标准
2. **文档编写** - 确保文档的专业性和可读性
3. **团队协作** - 统一团队的开发实践
4. **工作流程自动化** - 将重复的开发工作流程标准化

## 最佳实践

### 编写高质量规则

1. **保持简洁** - 规则应保持在 500 行以内
2. **聚焦明确** - 将大概念拆分为多个可组合的规则
3. **提供示例** - 包含具体示例或引用文件
4. **避免模糊** - 编写清晰明确的指导，就像编写内部文档一样
5. **重复利用** - 当发现在聊天中重复使用相同提示时，考虑创建规则

### 规则组织

- **按功能分类** - 将相关规则组织在相应的子目录中
- **使用描述性名称** - 文件名应清楚表达规则的用途
- **版本控制** - 将规则文件纳入版本控制系统
- **团队协作** - 通过代码审查确保规则质量

### 规则示例类型

1. **领域特定指导** - 针对特定技术栈或业务域的规范
2. **样板和模板** - 代码生成模板和结构规范
3. **工作流程自动化** - 开发流程和任务自动化
4. **代码风格** - 编码风格和架构决策标准

## 扩展规则

### 目录组织原则

按功能领域组织，通过文件名区分具体类型：

- **workflow/** - 开发流程、工作协议类规则
- **documentation/** - 文档编写、格式规范类规则
- **standards/** - 通用编码标准（可跨项目复用）
  - 命名格式: `{language}-{aspect}.mdc` 或 `{aspect}.mdc`
  - 示例: `coding-principles.mdc`, `shell-coding.mdc`
- **architecture/** - 项目特定的架构原则和约束
- **tools/** - 开发工具使用说明和注意事项

### 规则层次

1. **通用原则** (`standards/coding-principles.mdc`)
   - 跨语言适用
   - alwaysApply: true
   - 所有代码都遵循

2. **语言标准** (`standards/{language}-coding.mdc`)
   - 特定语言的编码规范
   - globs: 匹配文件类型
   - 编辑对应文件时自动应用

3. **项目架构** (`architecture/*.mdc`)
   - 项目特定的设计约束
   - globs: 匹配特定文件
   - 维护架构一致性

### 文件组织

- **平铺式**: `standards/` 和 `architecture/` 下平铺文件，不创建子目录
- **命名清晰**: 通过文件名自说明（如 `shell-coding` 而不是 `coding`）
- **单一职责**: 每个规则文件聚焦一个主题

### 规则质量标准

每个规则文件应：

- **简洁**: < 500 行，聚焦核心原则
- **清晰**: 提供明确的 ✅/❌ 对比
- **可操作**: 包含检查清单
- **有价值**: 聚焦原则和标准，不是代码示例集

## 常见问题

**Q: 为什么我的规则没有生效？**
A: 检查规则类型设置。对于 `Agent Requested` 类型，确保定义了描述。对于 `Auto Attached` 类型，确保文件模式匹配引用的文件。

**Q: 规则可以引用其他规则或文件吗？**
A: 可以。使用 `@filename.ts` 可以在规则上下文中包含文件。

**Q: 可以从聊天中创建规则吗？**
A: 可以。使用 `/Generate Cursor Rules` 命令从聊天生成项目规则。如果启用了记忆功能，记忆将自动生成。

**Q: 规则是否影响 Cursor Tab 或其他 AI 功能？**
A: 不会。规则仅提供给 Agent 和 Cmd-K AI 模型使用。

## 参考

- [Cursor 规则官方文档](https://docs.cursor.com/context/rules#user-rules)
