# Claude Code Agent Skill 编写指南

## 1. Skill 基本概念

### 什么是 Skill？

Skill 是 Claude Code agent 的专业化指令集，用于处理特定类型的任务。它们提供：

- **标准化流程**：为常见任务（如创建 MCP 服务器、自定义模式）定义明确的步骤
- **上下文知识**：包含特定领域的最佳实践和约束条件
- **可复用模板**：减少重复性工作，提高一致性

### Skill 的作用

1. **任务专业化**：针对特定场景提供专家级指导
2. **流程标准化**：确保相同类型任务的执行一致性
3. **知识封装**：将领域知识打包成可复用的指令集
4. **提高效率**：减少用户需要提供的上下文信息

---

## 2. Skill 文件结构

### 基本结构

```markdown
# Skill 标题

## 概述
简要说明这个 Skill 的用途和适用场景

## 前置条件
列出使用此 Skill 需要满足的条件

## 执行步骤

### 步骤 1：标题
详细说明...

### 步骤 2：标题
详细说明...

## 最佳实践
提供相关的最佳实践建议

## 注意事项
列出需要特别注意的事项

## 示例
提供具体的使用示例
```

### 关键组成部分

1. **标题和概述**：清晰说明 Skill 的目的
2. **前置条件**：明确使用要求
3. **执行步骤**：分步骤的详细指导
4. **最佳实践**：经验总结和建议
5. **注意事项**：常见陷阱和警告
6. **示例**：实际应用案例

---

## 3. Skill 语法规则

### Markdown 格式要求

- 使用标准 Markdown 语法
- 标题层级清晰（# ## ### ####）
- 代码块使用三个反引号包裹
- 列表使用 `-` 或 `1.` 格式

### 特殊标记

```markdown
**重要**：使用粗体标记关键信息
`代码片段`：使用反引号标记代码或命令
> 引用：使用引用块标记注意事项
```

### 文件引用

当需要引用其他文件时：

```markdown
参见：[`filename.ext`](path/to/file.ext)
```

### 条件逻辑

```markdown
**如果** 条件A：
- 执行操作 X

**否则**：
- 执行操作 Y
```

---

## 4. Skill 配置和加载机制

### 存储位置

Skill 文件通常存储在：

```
.codex/skills/           # 项目级 Skills
~/.codex/skills/         # 全局 Skills（用户级）
```

### 命名规范

- 使用小写字母和连字符：`create-mcp-server.md`
- 文件名应清晰表达 Skill 的用途
- 避免使用空格和特殊字符

### 加载机制

1. **自动检测**：Agent 根据用户请求自动匹配相关 Skill
2. **显式调用**：使用 `skill` 工具加载特定 Skill
3. **优先级**：项目级 Skills 优先于全局 Skills

### Skill 元数据

在 Skill 文件开头可以包含元数据：

```markdown
---
name: create-mcp-server
description: 创建 MCP 服务器的标准流程
version: 1.0.0
tags: [mcp, server, setup]
---
```

---

## 5. 创建自定义 Skill 示例

### 示例：Go + Vue CRUD 生成器

```markdown
# Go + Vue CRUD 模块生成器

## 概述
为供暖工单管理系统快速生成标准的 CRUD 功能模块，包括 Go 后端和 Vue 前端。

## 前置条件
- 项目结构遵循 [`AGENTS.md`](../../AGENTS.md) 规范
- Go 后端使用 Gin + GORM
- Vue 前端使用 Vue 3 + Vite

## 执行步骤

### 步骤 1：收集需求信息

询问用户以下信息：
- 模块名称（英文，如 `heat_meter`）
- 中文名称（如"热量表"）
- 主要字段列表
- 是否需要软删除
- 是否需要租户隔离

### 步骤 2：生成后端代码

#### 2.1 创建 Model

在 [`server/internal/app/model/`](../../server/internal/app/model/) 创建模型文件：

```go
// {module_name}.go
package model

type {ModelName} struct {
    Base
    TenantID uint   `gorm:"not null;index" json:"tenantId"`
    Name     string `gorm:"size:100;not null" json:"name"`
    // 其他字段...
}
```

#### 2.2 创建 Repository

在 [`server/internal/app/repository/`](../../server/internal/app/repository/) 创建仓储层。

#### 2.3 创建 Service

在 [`server/internal/app/service/{module_name}/`](../../server/internal/app/service/) 创建服务层。

#### 2.4 创建 Handler

在 [`server/internal/app/handler/{module_name}/`](../../server/internal/app/handler/) 创建处理器。

#### 2.5 注册路由

在 [`server/internal/app/router/router.go`](../../server/internal/app/router/router.go) 注册路由。

### 步骤 3：生成前端代码

#### 3.1 创建 API 接口

在 [`web/src/api/`](../../web/src/api/) 创建 API 文件：

```typescript
// {moduleName}s.ts
import http from './http'

export interface {ModelName} {
  id: number
  name: string
  // 其他字段...
}

export const {moduleName}Api = {
  list: (params: any) => http.get('/{module_name}s', { params }),
  create: (data: {ModelName}) => http.post('/{module_name}s', data),
  update: (id: number, data: {ModelName}) => http.put(`/{module_name}s/${id}`, data),
  delete: (id: number) => http.delete(`/{module_name}s/${id}`)
}
```

#### 3.2 创建页面组件

在 [`web/src/pages/admin/{module_category}/`](../../web/src/pages/admin/) 创建页面。

#### 3.3 注册路由

在 [`web/src/router/index.ts`](../../web/src/router/index.ts) 注册前端路由。

### 步骤 4：测试验证

1. 后端测试：`cd server && go test ./...`
2. 前端构建：`cd web && pnpm build`
3. 手动测试 CRUD 功能

## 最佳实践

1. **命名一致性**：保持前后端命名风格一致
2. **权限控制**：为新模块添加相应的权限码
3. **数据验证**：前后端都要进行数据验证
4. **错误处理**：统一的错误处理和提示
5. **代码复用**：参考现有模块的实现模式

## 注意事项

- 遵循项目的分层架构：`handler → service → repository → model`
- 使用事务处理涉及多表的操作
- 前端使用 `@/` 别名引用 `web/src` 目录
- 不要自动添加测试代码，除非用户明确要求
- 提交前运行 `gofmt` 格式化 Go 代码

## 示例

用户请求："创建一个热量表管理模块"

生成的文件：
- `server/internal/app/model/heat_meter.go`
- `server/internal/app/repository/heat_meter_repository.go`
- `server/internal/app/service/heatmeter/service.go`
- `server/internal/app/handler/heatmeter/handler.go`
- `web/src/api/heatMeters.ts`
- `web/src/pages/admin/base-settings/HeatMetersPage.vue`
```

---

## 6. 最佳实践总结

### 编写 Skill 的原则

1. **清晰性**：步骤明确，易于理解
2. **完整性**：覆盖所有必要的信息
3. **可操作性**：提供具体的执行指导
4. **可维护性**：易于更新和扩展
5. **一致性**：遵循统一的格式和风格

### 内容组织

- **从概述到细节**：先整体后局部
- **分步骤执行**：每个步骤独立且可验证
- **提供示例**：用实际案例说明
- **链接相关资源**：引用项目中的相关文件

### 语言风格

- 使用祈使句（"创建文件"而非"应该创建文件"）
- 保持简洁，避免冗余
- 使用项目术语，保持一致性
- 中英文混排时注意空格

### 文件引用

```markdown
正确：[`server/config/default.yaml`](../../server/config/default.yaml)
错误：server/config/default.yaml（无链接）
```

---

## 7. 常见陷阱和注意事项

### 避免的问题

1. **过于宽泛**：Skill 应该针对特定任务，不要试图覆盖所有情况
2. **缺少上下文**：假设用户了解项目结构，但要提供必要的背景信息
3. **步骤不清晰**：每个步骤应该有明确的输入和输出
4. **忽略错误处理**：说明可能出现的问题和解决方案
5. **缺少验证**：每个关键步骤后应该有验证方法

### 维护建议

- 定期更新 Skill 以反映项目变化
- 收集使用反馈，持续改进
- 版本控制 Skill 文件
- 文档化 Skill 的变更历史

---

## 8. Skill 与 Mode 的关系

### Mode 限制

不同的 Mode 有不同的文件编辑权限：

- **Architect Mode**：只能编辑 `.md` 文件
- **Code Mode**：可以编辑所有代码文件
- **Debug Mode**：专注于调试和问题诊断

### Skill 适配

Skill 应该考虑当前 Mode 的限制：

```markdown
**在 Architect Mode 中**：
- 创建计划文档
- 使用 `switch_mode` 切换到 Code Mode 执行实现

**在 Code Mode 中**：
- 直接创建和修改代码文件
- 执行测试和验证
```

---

## 9. 高级技巧

### 条件分支

```markdown
### 步骤 2：选择实现方式

**如果**用户选择"简单模式"：
1. 使用默认配置
2. 跳过高级选项

**如果**用户选择"高级模式"：
1. 询问详细配置
2. 自定义所有选项
```

### 循环和迭代

```markdown
### 步骤 3：为每个字段生成代码

**对于每个字段**：
1. 确定字段类型
2. 生成 Go struct tag
3. 生成 TypeScript 接口
4. 添加表单验证规则
```

### 错误恢复

```markdown
### 故障排除

**如果**遇到编译错误：
1. 检查导入语句
2. 验证类型定义
3. 运行 `go mod tidy`

**如果**前端构建失败：
1. 清除缓存：`rm -rf node_modules/.vite`
2. 重新安装依赖：`pnpm install`
3. 重新构建：`pnpm build`
```

---

## 10. 实战案例：为本项目创建 Skill

### 场景分析

供暖工单管理系统的常见任务：

1. **新增基础数据模块**（如热量表、阀门等）
2. **工单类型扩展**（新增工单分类）
3. **报表生成**（统计分析功能）
4. **第三方集成**（对接外部系统）

### 推荐创建的 Skills

1. **`create-base-data-module.md`**
   - 快速生成基础数据 CRUD
   - 包含导入导出功能
   - 集成字典管理

2. **`create-workorder-type.md`**
   - 扩展工单类型
   - 生成对应的表单和列表
   - 配置工作流

3. **`create-report-page.md`**
   - 创建统计报表页面
   - 集成 ECharts 图表
   - 实现数据导出

4. **`integrate-third-party-api.md`**
   - 第三方 API 对接流程
   - Outbox 模式实现
   - 错误处理和重试机制

---

## 总结

编写高质量的 Skill 需要：

1. **深入理解项目**：熟悉代码结构和约定
2. **清晰的文档**：步骤明确，易于跟随
3. **实用的示例**：提供真实的使用场景
4. **持续改进**：根据反馈不断优化

通过创建项目特定的 Skills，可以显著提高开发效率，确保代码质量的一致性。
