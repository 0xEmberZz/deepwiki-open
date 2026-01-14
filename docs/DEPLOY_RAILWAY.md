# Railway 部署指南

本指南介绍如何将 DeepWiki 部署到 [Railway](https://railway.app)。

## 前置要求

- Railway 账号（可使用 GitHub 登录）
- 至少一个 API 密钥：`GOOGLE_API_KEY` 或 `OPENAI_API_KEY`

## 部署步骤

### 方式一：一键部署（推荐）

1. Fork 本仓库到你的 GitHub 账号

2. 登录 [Railway](https://railway.app)

3. 点击 **New Project** → **Deploy from GitHub repo**

4. 选择你 fork 的 `deepwiki-open` 仓库

5. Railway 会自动检测 `railway.toml` 并开始构建

6. 在 **Variables** 标签页添加环境变量：
   ```
   GOOGLE_API_KEY=your_google_api_key
   OPENAI_API_KEY=your_openai_api_key
   ```

7. 等待部署完成（首次构建约 5-10 分钟）

8. 点击 **Settings** → **Networking** → **Generate Domain** 获取访问 URL

### 方式二：Railway CLI

```bash
# 安装 Railway CLI
npm install -g @railway/cli

# 登录
railway login

# 初始化项目
railway init

# 添加环境变量
railway variables set GOOGLE_API_KEY=your_key
railway variables set OPENAI_API_KEY=your_key

# 部署
railway up
```

## 环境变量配置

### 必需变量

| 变量 | 说明 |
|------|------|
| `GOOGLE_API_KEY` | Google Gemini API 密钥 |
| `OPENAI_API_KEY` | OpenAI API 密钥（用于 embeddings） |

### 可选变量

| 变量 | 说明 |
|------|------|
| `OPENROUTER_API_KEY` | OpenRouter API 密钥 |
| `DEEPWIKI_AUTH_MODE` | 设为 `true` 启用认证模式 |
| `DEEPWIKI_AUTH_CODE` | 认证模式下的访问密码 |
| `LOG_LEVEL` | 日志级别 (DEBUG/INFO/WARNING/ERROR) |

## 添加持久化存储（推荐）

默认情况下，Railway 容器是无状态的。添加 Volume 可以持久化：
- 克隆的仓库
- 向量数据库
- Wiki 缓存

1. 在 Railway 项目中，点击 **New** → **Volume**

2. 挂载路径设为：`/root/.adalflow`

3. 重新部署服务

## 资源配置

DeepWiki 建议配置：

- **内存**: 至少 2GB，推荐 4GB
- **CPU**: 至少 1 vCPU

在 Railway 的 **Settings** → **Resources** 中调整。

## 常见问题

### 构建失败：内存不足

Railway 免费层内存有限。解决方案：
1. 升级到付费计划
2. 或使用更小的基础镜像

### 访问显示 502 错误

服务可能还在启动中，等待 30 秒后重试。检查 **Deployments** 标签页的日志。

### WebSocket 连接失败

确保使用 `https://` 而不是 `http://` 访问。Railway 自动提供 SSL。

## 费用估算

Railway 按使用量计费：
- **Hobby Plan**: $5/月，包含 $5 额度
- **Pro Plan**: $20/月起，更多资源

DeepWiki 月均费用约 $5-15，取决于使用频率。

## 相关文件

- `railway.toml` - Railway 部署配置
- `Dockerfile.railway` - Railway 专用 Dockerfile
