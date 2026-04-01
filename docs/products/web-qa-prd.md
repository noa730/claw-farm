# 循环农业问答助手 — 产品需求文档 (PRD)

## 1. 产品概述

### 1.1 一句话描述
一个面向非技术用户的 Web 问答界面，基于循环农业知识库提供专家级农业咨询。

### 1.2 目标用户
- 电脑使用经验少的农业从业者
- 无需安装任何软件，打开浏览器即可使用

### 1.3 核心场景
用户打开网页 → 用自然语言提问 → 获得基于知识库的专业回答

## 2. 功能需求

### 2.1 基础问答（MVP）
- 用户输入文字问题，系统返回文字回答
- 回答基于知识库内容，标注数据来源
- 支持多轮对话（上下文保持）
- 支持手机浏览器访问

### 2.2 知识库自动同步
- 后端定时从 GitHub 拉取最新知识库文件（建议间隔：1 小时）
- 用户端无需任何操作即可获取最新知识
- 同步失败时使用本地缓存，不影响服务可用性

### 2.3 访问控制
- 支持邀请码机制（可选开启）
- 无需注册账号

## 3. 技术架构

### 3.1 整体架构
```
┌──────────────┐     ┌──────────────────────┐     ┌─────────────┐
│   浏览器      │────→│   后端 API 服务       │────→│  LLM API    │
│  （用户端）    │←────│                      │←────│ (DeepSeek)  │
└──────────────┘     │  ┌────────────────┐  │     └─────────────┘
                     │  │ 知识库缓存      │  │
                     │  │ (定时从GitHub拉) │  │
                     │  └────────────────┘  │
                     └──────────┬───────────┘
                                │ 定时拉取
                     ┌──────────▼───────────┐
                     │   GitHub Repo         │
                     │   claw-farm           │
                     └──────────────────────┘
```

### 3.2 技术选型

| 组件 | 推荐方案 | 备选 |
|------|---------|------|
| 前端 | 纯 HTML/CSS/JS（单文件） | React |
| 后端 | Python FastAPI | Node.js Express |
| LLM | DeepSeek API | 通义千问 / Kimi / Claude（需代理） |
| 部署 | 阿里云函数计算 / 腾讯云 | 自有服务器 |

### 3.3 模型配置
系统应支持通过环境变量切换模型，不锁死单一供应商：

```env
LLM_PROVIDER=deepseek          # deepseek / openai / claude / qwen
LLM_API_KEY=sk-xxx
LLM_MODEL=deepseek-chat        # 具体模型名
LLM_BASE_URL=https://api.deepseek.com  # API 地址
```

## 4. 知识库对接

### 4.1 数据源仓库
- **仓库地址**：`https://github.com/noa730/claw-farm`
- **分支**：`main`

### 4.2 需要拉取的文件

采用 **动态遍历** 方式，不写死文件路径。知识库新增文件后自动纳入，无需改代码。

| 规则 | 说明 |
|------|------|
| `CLAUDE.md`（根目录） | 固定拉取，作为 System Prompt 核心 |
| `docs/distilled/**/*.md` | **递归遍历所有子目录**，拉取全部 `.md` 文件 |

> 当知识库新增目录（如 `docs/distilled/compost/`）或新增文件时，后端下次同步会自动发现并加载，无需任何配置变更。

### 4.3 GitHub API 调用方式

#### 递归获取整个目录树（推荐，一次请求拿到所有文件路径）
```
GET https://api.github.com/repos/noa730/claw-farm/git/trees/main?recursive=1
Accept: application/vnd.github.v3+json

# 响应中 tree 数组包含所有文件路径，过滤逻辑：
# 1. path == "CLAUDE.md" → 拉取
# 2. path.startsWith("docs/distilled/") && path.endsWith(".md") → 拉取
# 3. 其他文件忽略
```

#### 获取单个文件内容
```
GET https://api.github.com/repos/noa730/claw-farm/contents/{path}
Accept: application/vnd.github.v3+json

# 响应中 content 字段为 Base64 编码，需解码
# sha 字段可用于判断文件是否变更（缓存优化）
```

> 注：公开仓库无需 Token，但有速率限制（60次/小时）。建议配置 GitHub Token 提升至 5000次/小时。

### 4.4 同步流程（伪代码）
```python
def sync_knowledge_base():
    # 1. 一次请求拿到完整文件树
    tree = github_api.get_tree("noa730/claw-farm", branch="main", recursive=True)

    # 2. 过滤出需要的 .md 文件
    targets = [f for f in tree if
        f.path == "CLAUDE.md" or
        (f.path.startswith("docs/distilled/") and f.path.endswith(".md"))]

    # 3. 逐个拉取内容（跳过 sha 未变的文件）
    for f in targets:
        if cache.get(f.path)?.sha == f.sha:
            continue  # 未变更，跳过
        content = github_api.get_file_content(f.path)
        cache.set(f.path, sha=f.sha, content=content)

    return cache
```

### 4.5 System Prompt 拼装逻辑
```python
def build_system_prompt(cache):
    parts = []

    # 1. 全局指令（固定在最前）
    parts.append(cache.get("CLAUDE.md"))

    # 2. 按目录分组拼接知识库
    for path in sorted(cache.keys()):
        if path == "CLAUDE.md":
            continue
        # 用目录名作为章节标题
        # docs/distilled/shared/context.md → "# shared/context"
        section = path.replace("docs/distilled/", "").replace(".md", "")
        parts.append(f"\n\n# 知识库: {section}\n" + cache.get(path))

    return "\n".join(parts)
```

## 5. 前端设计

### 5.1 页面要素
```
┌─────────────────────────────────┐
│  🌾 循环农业智能问答             │  ← 标题栏
├─────────────────────────────────┤
│                                 │
│  用户: 松茸出菇需要什么温度？     │  ← 对话区域
│                                 │
│  助手: 根据知识库，松茸出菇的     │
│  最适温度为 16-20°C ...          │
│                                 │
├─────────────────────────────────┤
│  [请输入您的问题...]     [发送]  │  ← 输入区域
└─────────────────────────────────┘
```

### 5.2 交互要求
- 发送消息：点击按钮 或 按回车
- 等待回答时显示"正在思考..."
- 回答支持 Markdown 渲染（加粗、表格、列表）
- 流式输出（打字机效果），降低等待焦虑
- 移动端自适应

### 5.3 风格
- 简洁、清爽，绿色主色调（与农业主题一致）
- 字体大、间距宽（照顾非技术用户）
- 无多余按钮和功能入口

## 6. 后端 API 设计

### 6.1 接口定义

#### POST /api/chat
```json
// 请求
{
  "message": "松茸出菇需要什么温度？",
  "history": [
    {"role": "user", "content": "你好"},
    {"role": "assistant", "content": "你好！我是循环农业助手..."}
  ]
}

// 响应（流式 SSE）
data: {"content": "根据", "done": false}
data: {"content": "知识库", "done": false}
data: {"content": "...", "done": true}
```

#### GET /api/health
```json
// 响应
{
  "status": "ok",
  "knowledge_base_updated_at": "2026-04-01T15:00:00Z",
  "file_count": 12
}
```

## 7. 部署要求

### 7.1 环境变量
```env
# LLM 配置
LLM_PROVIDER=deepseek
LLM_API_KEY=sk-xxx
LLM_MODEL=deepseek-chat
LLM_BASE_URL=https://api.deepseek.com

# GitHub 知识库
GITHUB_REPO=noa730/claw-farm
GITHUB_BRANCH=main
GITHUB_TOKEN=ghp_xxx              # 可选，提升速率限制

# 访问控制
INVITE_CODE=                       # 留空则不启用邀请码

# 同步间隔
SYNC_INTERVAL_MINUTES=60
```

### 7.2 部署平台：腾讯云

推荐使用 **腾讯云云函数 SCF + API 网关** 组合：

| 组件 | 腾讯云产品 | 说明 |
|------|-----------|------|
| 后端函数 | 云函数 SCF | Python 3.9 运行时，按调用次数计费 |
| HTTP 入口 | API 网关 | 绑定自定义域名，HTTPS |
| 前端托管 | 静态网站托管 / COS | 存放 HTML/CSS/JS |
| 定时触发 | SCF 定时触发器 | 每小时触发知识库同步 |

#### 费用估算（月）
| 项目 | 免费额度 | 超出费用 |
|------|---------|---------|
| SCF 调用次数 | 100万次/月 | ¥0.0133/万次 |
| SCF 资源使用 | 40万 GBs/月 | ¥0.00011108/GBs |
| API 网关 | 100万次/月 | ¥0.06/万次 |
| COS 存储 | 50GB | ¥0.118/GB/月 |

> 初期使用量下，基本在免费额度内，月费用约 ¥0-5。

#### 部署步骤概要
```
1. 创建 SCF 云函数（Python 3.9，上传后端代码）
2. 配置环境变量（LLM_API_KEY 等）
3. 创建 API 网关触发器，绑定 /api/* 路径
4. 创建定时触发器（每小时执行 sync_knowledge_base）
5. 前端 HTML 上传至 COS，开启静态网站
6.（可选）绑定自定义域名 + SSL 证书
```

## 8. 非功能需求

| 项目 | 要求 |
|------|------|
| 响应时间 | 首字输出 < 2 秒 |
| 并发 | 支持 5 人同时使用（初期） |
| 可用性 | 知识库同步失败不影响问答（使用缓存） |
| 安全 | API Key 不暴露在前端；输入做基本过滤 |

## 9. 里程碑

| 阶段 | 交付物 | 预期 |
|------|--------|------|
| M1 | 单文件前端 + 后端 API + 本地可运行 | 1-2 天 |
| M2 | 部署上线 + 知识库自动同步 | 1 天 |
| M3 | 邀请码 + 移动端优化 | 1 天 |
