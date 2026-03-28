# 股票智能分析系统 - GitHub Actions 部署指南（A股/基金版）

## 📋 目录

- [项目简介](#项目简介)
- [快速开始](#快速开始)
  - [方式一：GitHub Actions 部署（推荐）](#方式一github-actions-部署推荐)
  - [方式二：本地 Web 界面部署（可选）](#方式二本地-web-界面部署可选)
- [配置说明](#配置说明)
- [常见问题](#常见问题)

---

## 项目简介

基于 AI 大模型的 A股/基金智能分析系统，每日自动分析并推送「决策仪表盘」到企业微信/飞书/邮箱等渠道。

**核心功能**：
- 🤖 AI 决策仪表盘（一句话核心结论 + 精确买卖点位）
- 📊 多维度分析（技术面 + 筹码分布 + 舆情情报）
- �🇳 A股全市场支持（主板、创业板、科创板、北交所）
- 📈 基金支持（ETF、LOF等场内基金）
- 📱 多渠道推送（企业微信、飞书、邮箱、PushPlus等）
- ⏰ GitHub Actions 定时执行，零成本无需服务器

---

## 快速开始

### 两种部署方式对比

| 特性 | 方式一：GitHub Actions | 方式二：本地 Web 界面 |
|------|----------------------|---------------------|
| **适用场景** | 每日自动推送报告 | 手动分析、查看历史 |
| **部署成本** | 免费 | 需要本地电脑/服务器 |
| **定时推送** | ✅ 自动 | ❌ 手动 |
| **维护成本** | 低 | 高 |
| **需要 GitHub** | ✅ 是 | ❌ 否 |

**推荐**：日常使用选择 **方式一**，需要临时分析或查看历史记录时选择 **方式二**。

---

## 方式一：GitHub Actions 部署（推荐）

### 部署前准备

| 准备项 | 说明 | 获取方式 |
|--------|------|---------|
| GitHub 账号 | 托管代码、运行 Actions | [github.com](https://github.com) 注册 |
| AI 模型 API Key | 提供分析能力 | 至少配置一个（见步骤 3） |
| 推送渠道 | 接收分析结果 | 至少配置一个（见步骤 3） |

### 步骤 1：Fork 仓库

1. 打开项目地址：[https://github.com/ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis)

2. 点击右上角 **Fork** 按钮

3. 在弹出的页面中，保持默认设置，点击 **Create fork**

4. （可选）顺便点个 **Star⭐** 支持项目

> 💡 **关于自动更新**：Fork 的仓库不会自动同步原始项目的更新。如需更新，可参考以下两种方法：

#### 方法一：GitHub 网页同步（推荐，保留配置）

**适用场景**：已配置 Secrets，想保留配置的同时更新代码

**操作步骤**：

1. **进入你的 Fork 仓库页面**
   - 打开 `https://github.com/你的用户名/daily_stock_analysis`

2. **查看同步状态**
   - 如果原始项目有更新，你会在页面上方看到黄色提示框：
   - `This branch is out-of-date with ZhuLinsen/daily_stock_analysis:main`

3. **点击 Sync fork 按钮**
   - 点击页面上的 **"Sync fork"** 下拉按钮
   - 选择 **"Update branch"**
   - 等待同步完成

4. **验证更新**
   - 刷新页面，黄色提示框消失
   - 查看代码文件，确认已更新到最新版本

**示意图**：

```
┌─────────────────────────────────────────────────────────┐
│  This branch is out-of-date with ZhuLinsen/...          │
│                                                         │
│  [ Sync fork ▼ ]  [ Discard 1 commit ]                  │
│       │                                                 │
│       ▼                                                 │
│  ┌─────────────────────────┐                            │
│  │  Update branch          │ ← 点击这个                 │
│  │  Open pull request      │                            │
│  └─────────────────────────┘                            │
└─────────────────────────────────────────────────────────┘
```

**注意事项**：
- ✅ Secrets 配置会保留，不会丢失
- ⚠️ 如果提示 "Can't update because of conflicts"，说明你有自定义修改与上游冲突，此时请使用方法二

---

#### 方法二：删除重新 Fork（简单快捷）

**适用场景**：刚 Fork 还没配置，或配置较少，或同步出现冲突

**操作步骤**：

1. **删除现有 Fork**
   - 进入你的 Fork 仓库 → Settings → 拉到最下方 Danger Zone
   - 点击 **"Delete this repository"**
   - 输入仓库名确认删除

2. **重新 Fork**
   - 回到原始项目页面
   - 点击 Fork 按钮重新 Fork

3. **重新配置 Secrets**
   - 按本文档步骤重新配置 STOCK_LIST、AI Key、推送渠道等

> ⚠️ **警告**：删除仓库会丢失所有 Secrets 配置，请确保你记得所有配置值！

---

### 步骤 2：配置 Secrets

1. 进入你 Fork 的仓库页面

2. 点击 **Settings**（设置）

3. 在左侧菜单找到 **Secrets and variables** → **Actions**

4. 点击 **New repository secret** 按钮

---

### 步骤 3：配置必需的 Secrets

#### 3.1 配置自选股列表（必填）

| Secret 名称 | 值示例 | 说明 |
|-------------|--------|------|
| `STOCK_LIST` | `600519,300750,510300,159915` | A股股票/基金代码，逗号分隔 |

**A股代码格式说明**：

| 类型 | 格式 | 示例 | 说明 |
|------|------|------|------|
| 沪市股票 | 60xxxx | `600519`（贵州茅台） | 上海证券交易所主板 |
| 深市股票 | 00xxxx | `000001`（平安银行） | 深圳证券交易所主板 |
| 创业板 | 30xxxx | `300750`（宁德时代） | 深圳证券交易所创业板 |
| 科创板 | 688xxx | `688981`（中芯国际） | 上海证券交易所科创板 |
| 沪市ETF | 51xxxx | `510300`（沪深300ETF） | 上海证券交易所基金 |
| 深市ETF | 15xxxx | `159915`（创业板ETF） | 深圳证券交易所基金 |
| LOF基金 | 16xxxx | `161725`（招商白酒A） | 上市型开放式基金 |

**操作步骤**：
1. Name 输入：`STOCK_LIST`
2. Secret 输入：你的自选股代码（如 `600519,000001,510300`）
3. 点击 **Add secret**

---

#### 3.2 配置 AI 模型（至少一个）

**方案 A：使用 AIHubMix（推荐，国内可直接访问）**

| Secret 名称 | 值 |
|-------------|-----|
| `AIHUBMIX_KEY` | 你的 AIHubMix API Key |

> 💡 AIHubMix 一个 Key 即可使用 Gemini、GPT、Claude、DeepSeek 等全球主流模型，无需科学上网，含免费模型。

**方案 B：使用 DeepSeek（推荐，国内可直接访问）**

| Secret 名称 | 值 |
|-------------|-----|
| `OPENAI_API_KEY` | 你的 DeepSeek API Key（如 `sk-xxx`） |
| `OPENAI_BASE_URL` | `https://api.deepseek.com/v1` |
| `OPENAI_MODEL` | `deepseek-chat` |

**方案 C：使用通义千问（国内可直接访问）**

| Secret 名称 | 值 |
|-------------|-----|
| `OPENAI_API_KEY` | 你的通义千问 API Key |
| `OPENAI_BASE_URL` | `https://dashscope.aliyuncs.com/compatible-mode/v1` |
| `OPENAI_MODEL` | `qwen-turbo` 或 `qwen-plus` |

**方案 D：使用 Gemini（需科学上网）**

| Secret 名称 | 值 |
|-------------|-----|
| `GEMINI_API_KEY` | 你的 Google AI Studio API Key |

**方案 E：使用 OpenAI（需科学上网）**

| Secret 名称 | 值 |
|-------------|-----|
| `OPENAI_API_KEY` | 你的 OpenAI API Key |
| `OPENAI_MODEL` | `gpt-4o` 或 `gpt-3.5-turbo` |

---

#### 3.3 配置推送渠道（至少一个）

**方案 A：企业微信（推荐）**

1. 在企业微信群中添加机器人
   - 打开企业微信群 → 群设置 → 群机器人 → 添加机器人
2. 获取 Webhook URL（格式：`https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx`）

| Secret 名称 | 值 |
|-------------|-----|
| `WECHAT_WEBHOOK_URL` | 你的 Webhook URL |

**方案 B：飞书（推荐）**

1. 在飞书群中添加机器人
   - 打开飞书群 → 设置 → 群机器人 → 添加机器人 → 自定义机器人
2. 获取 Webhook URL

| Secret 名称 | 值 |
|-------------|-----|
| `FEISHU_WEBHOOK_URL` | 你的 Webhook URL |

**方案 C：邮箱**

| Secret 名称 | 值 |
|-------------|-----|
| `EMAIL_SENDER` | 发件人邮箱（如 `xxx@qq.com`） |
| `EMAIL_PASSWORD` | 邮箱授权码（非登录密码） |
| `EMAIL_RECEIVERS` | 收件人邮箱（多个用逗号分隔） |

> 💡 QQ邮箱授权码获取：设置 → 账户 → POP3/SMTP服务 → 生成授权码

**方案 D：PushPlus（微信推送）**

1. 关注 PushPlus 公众号
2. 获取 Token

| Secret 名称 | 值 |
|-------------|-----|
| `PUSHPLUS_TOKEN` | 你的 PushPlus Token |

**方案 E：Server酱（微信推送）**

1. 注册 Server酱 账号
2. 获取 SendKey

| Secret 名称 | 值 |
|-------------|-----|
| `SERVERCHAN3_SENDKEY` | 你的 SendKey |

**方案 F：Telegram（需科学上网）**

1. 在 Telegram 中搜索 @BotFather
2. 发送 `/newbot` 创建机器人，获取 Token
3. 获取 Chat ID（可使用 @userinfobot）

| Secret 名称 | 值 |
|-------------|-----|
| `TELEGRAM_BOT_TOKEN` | 你的 Bot Token（如 `123456:ABC-DEF`） |
| `TELEGRAM_CHAT_ID` | 你的 Chat ID（如 `123456789`） |

---

### 步骤 4：启用 GitHub Actions

1. 进入你 Fork 的仓库

2. 点击 **Actions** 标签

3. 如果看到提示 "Workflows aren't being run on this fork"，点击 **I understand my workflows, go ahead and enable them**

---

### 步骤 5：手动测试运行

1. 点击 **Actions** 标签

2. 在左侧选择 **每日股票分析** workflow

3. 点击右侧 **Run workflow** 按钮

4. 保持默认设置，点击绿色的 **Run workflow** 按钮

5. 等待运行完成（约 2-5 分钟）

6. 点击运行记录查看日志

7. 检查你配置的推送渠道是否收到消息

---

### 步骤 6：完成部署

✅ 部署完成！

**默认执行时间**：每个**工作日 18:00（北京时间）**自动执行

**非交易日**：自动跳过（含 A/H/US 节假日）

---

## 方式二：本地 Web 界面部署（可选）

如果你希望在本地通过 Web 界面手动分析股票、查看历史记录，可以使用以下方式部署。

> 💡 **注意**：Web 界面的配置与 GitHub Actions **完全独立**，需要分别配置。

### 部署前准备

| 准备项 | 说明 | 获取方式 |
|--------|------|---------|
| Python 3.8+ | 运行环境 | [python.org](https://python.org) 下载 |
| AI 模型 API Key | 提供分析能力 | 至少配置一个（见下方配置说明） |
| （可选）推送渠道 | 接收分析结果 | 企业微信、飞书、邮箱等 |

### 方式 2.1：本地运行

```bash
# 1. 克隆项目
git clone https://github.com/ZhuLinsen/daily_stock_analysis.git
cd daily_stock_analysis

# 2. 安装依赖
pip install -r requirements.txt

# 3. 配置环境变量
cp .env.example .env
# 编辑 .env 文件，配置 API Key（见下方配置说明）

# 4. 启动 Web 服务
python web_app.py
# 或
python main.py --web
```

**访问地址**：`http://localhost:5000`

### 方式 2.2：Docker 部署

```bash
# 1. 构建镜像
docker build -t stock-analysis .

# 2. 运行容器
docker run -d -p 5000:5000 --env-file .env stock-analysis
```

**访问地址**：`http://localhost:5000`

### Web 界面配置说明

Web 界面使用 `.env` 文件进行配置（与 GitHub Actions 的 Secrets 独立）：

```bash
# 复制示例配置文件
cp .env.example .env

# 编辑 .env 文件
vim .env
```

**常用配置项**：

| 配置项 | 说明 | 示例 |
|--------|------|------|
| `STOCK_LIST` | 自选股代码 | `600519,300750,510300` |
| `AIHUBMIX_KEY` | AIHubMix API Key | `sk-xxx` |
| `OPENAI_API_KEY` | OpenAI/DeepSeek/通义千问 Key | `sk-xxx` |
| `OPENAI_BASE_URL` | API 地址 | `https://api.deepseek.com/v1` |
| `OPENAI_MODEL` | 模型名称 | `deepseek-chat` |
| `TUSHARE_TOKEN` | Tushare Token（可选） | `2f4f8a9b...` |
| `BOCHA_API_KEYS` | 博查搜索 Key（可选） | `sk-xxx` |

> ⚠️ **注意**：Web 界面的 `.env` 配置与 GitHub Actions 的 Secrets **完全独立**，需要分别配置。

### Web 界面功能

| 功能 | 说明 |
|------|------|
| **手动分析** | 输入股票代码，即时分析 |
| **历史记录** | 查看过往分析记录 |
| **自选股管理** | 添加/删除自选股 |
| **配置管理** | 图形化界面修改配置 |
| **实时行情** | 查看实时价格和涨跌幅 |

### GitHub Actions vs Web 界面对比

| 功能 | GitHub Actions | Web 界面 |
|------|---------------|---------|
| **部署成本** | 免费 | 需要本地电脑/服务器 |
| **定时推送** | ✅ 自动每日推送 | ❌ 需手动点击 |
| **手动分析** | ❌ 只能分析配置列表 | ✅ 随时分析任意股票 |
| **历史记录** | ❌ 无 | ✅ 可视化查看 |
| **配置方式** | GitHub Secrets | 本地 `.env` 文件 |
| **维护成本** | 低 | 高 |

**建议**：日常使用推荐 **GitHub Actions**，需要临时分析或查看历史记录时使用 **Web 界面**。

---

## 配置说明

### 完整 Secrets 列表

#### 必填配置

| Secret 名称 | 说明 | 必填 |
|-------------|------|------|
| `STOCK_LIST` | 自选股代码，逗号分隔 | ✅ |

#### AI 模型配置（至少一个）

| Secret 名称 | 说明 | 国内可用 |
|-------------|------|---------|
| `AIHUBMIX_KEY` | AIHubMix API Key | ✅ |
| `OPENAI_API_KEY` | OpenAI 兼容 API Key（DeepSeek/通义千问） | ✅ |
| `OPENAI_BASE_URL` | OpenAI 兼容 API 地址 | - |
| `OPENAI_MODEL` | 模型名称 | - |
| `GEMINI_API_KEY` | Google Gemini API Key | ❌ 需科学上网 |
| `ANTHROPIC_API_KEY` | Claude API Key | ❌ 需科学上网 |

#### 数据源配置（可选，增强数据质量）

| Secret 名称 | 说明 | 国内可用 | 推荐度 |
|-------------|------|---------|--------|
| `TUSHARE_TOKEN` | Tushare Pro Token，获取更详细的财务数据、龙虎榜、机构持仓等 | ✅ | ⭐⭐⭐⭐ |

> 💡 **Tushare 的作用**：
> - 更详细的财务数据（资产负债表、利润表、现金流量表）
> - 龙虎榜数据（游资动向、机构买卖）
> - 机构持仓数据（基金持仓、北向资金）
> - 融资融券数据
> - 更准确的板块排名数据
> 
> **不配置也能正常使用**，系统会自动使用 AkShare 等免费数据源

#### 推送渠道配置（至少一个）

| Secret 名称 | 说明 | 国内可用 |
|-------------|------|---------|
| `WECHAT_WEBHOOK_URL` | 企业微信 Webhook | ✅ |
| `FEISHU_WEBHOOK_URL` | 飞书 Webhook | ✅ |
| `EMAIL_SENDER` | 发件人邮箱 | ✅ |
| `EMAIL_PASSWORD` | 邮箱授权码 | ✅ |
| `EMAIL_RECEIVERS` | 收件人邮箱 | ✅ |
| `PUSHPLUS_TOKEN` | PushPlus Token | ✅ |
| `SERVERCHAN3_SENDKEY` | Server酱 SendKey | ✅ |
| `TELEGRAM_BOT_TOKEN` | Telegram Bot Token | ❌ 需科学上网 |
| `TELEGRAM_CHAT_ID` | Telegram Chat ID | ❌ 需科学上网 |
| `DISCORD_WEBHOOK_URL` | Discord Webhook | ❌ 需科学上网 |
| `SLACK_BOT_TOKEN` | Slack Bot Token | ❌ 需科学上网 |

#### 新闻搜索配置

| Secret 名称 | 说明 | 国内可用 | 推荐度 |
|-------------|------|---------|--------|
| `BOCHA_API_KEYS` | 博查搜索（中文优化） | ✅ | ⭐⭐⭐⭐ |
| `MINIMAX_API_KEYS` | MiniMax 搜索 | ✅ | ⭐⭐⭐⭐ |
| `TAVILY_API_KEYS` | Tavily 搜索 API | ❌ 需科学上网 | ⭐⭐⭐ |
| `SERPAPI_API_KEYS` | SerpAPI 搜索 | ❌ 需科学上网 | ⭐⭐⭐ |
| `BRAVE_API_KEYS` | Brave Search API | ❌ 需科学上网 | ⭐⭐⭐ |

#### 可选配置

| Secret 名称 | 说明 | 默认值 |
|-------------|------|--------|
| `REPORT_TYPE` | 报告类型：simple/full/brief | simple |
| `REPORT_LANGUAGE` | 报告语言：zh/en | zh |
| `BIAS_THRESHOLD` | 乖离率阈值（%） | 5.0 |
| `NEWS_MAX_AGE_DAYS` | 新闻最大时效（天） | 3 |
| `TRADING_DAY_CHECK_ENABLED` | 交易日检查 | true |
| `ENABLE_CHIP_DISTRIBUTION` | 启用筹码分布 | false |

---

### 自定义执行时间

默认执行时间为**工作日 18:00（北京时间）**，如需修改：

1. 进入仓库，找到 `.github/workflows/` 目录
2. 编辑对应的 workflow 文件
3. 修改 `cron` 表达式

```yaml
on:
  schedule:
    - cron: '0 10 * * 1-5'  # UTC 时间，北京时间需减 8 小时
```

**常用时间对照表**：

| 北京时间 | cron 表达式（UTC） |
|---------|-------------------|
| 09:00 | `0 1 * * 1-5` |
| 12:00 | `0 4 * * 1-5` |
| 15:00 | `0 7 * * 1-5` |
| 18:00 | `0 10 * * 1-5` |
| 21:00 | `0 13 * * 1-5` |

---

## 常见问题

### Q1：Actions 运行失败怎么办？

**检查步骤**：
1. 进入 Actions 页面，点击失败的运行记录
2. 查看具体错误日志
3. 常见原因：
   - API Key 配置错误或过期
   - 网络问题（API 请求超时）
   - 自选股代码格式错误

### Q2：没有收到推送消息？

**排查步骤**：
1. 检查推送渠道的 Secret 是否正确配置
2. 检查 Webhook URL 是否有效
3. 检查邮箱授权码是否正确（非登录密码）
4. 查看 Actions 日志中是否有推送错误

### Q3：如何在非交易日测试？

**方法一**：手动触发时勾选 `force_run` 选项

**方法二**：设置 `TRADING_DAY_CHECK_ENABLED=false`

### Q4：如何添加/修改自选股？

1. 进入 Settings → Secrets and variables → Actions
2. 找到 `STOCK_LIST`，点击 Update
3. 修改股票代码列表
4. 点击 Update secret 保存

### Q5：支持哪些 A股代码格式？

| 类型 | 格式 | 示例 | 说明 |
|------|------|------|------|
| 沪市主板 | 60xxxx | `600519`（贵州茅台） | 上交所主板 |
| 深市主板 | 00xxxx | `000001`（平安银行） | 深交所主板 |
| 创业板 | 30xxxx | `300750`（宁德时代） | 深交所创业板 |
| 科创板 | 688xxx | `688981`（中芯国际） | 上交所科创板 |
| 北交所 | 8xxxxx / 4xxxxx | `832566`（梓撞科技） | 北交所 |
| 沪市ETF | 51xxxx | `510300`（沪深300ETF） | 上交所基金 |
| 深市ETF | 15xxxx | `159915`（创业板ETF） | 深交所基金 |
| LOF基金 | 16xxxx | `161725`（招商白酒A） | 上市型开放式基金 |

> 💡 **提示**：A股代码统一使用 6 位数字格式，无需添加前缀

### Q6：如何使用多个 AI 模型？

配置 `LLM_CHANNELS` 实现多模型负载均衡：

```
LLM_CHANNELS=primary,backup
LLM_PRIMARY_PROTOCOL=openai
LLM_PRIMARY_BASE_URL=https://api.deepseek.com/v1
LLM_PRIMARY_API_KEY=sk-xxx
LLM_PRIMARY_MODELS=deepseek-chat
LLM_BACKUP_PROTOCOL=openai
LLM_BACKUP_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
LLM_BACKUP_API_KEY=sk-xxx
LLM_BACKUP_MODELS=qwen-turbo
```

### Q7：Tushare Token 如何获取？

1. 访问 [https://tushare.pro](https://tushare.pro) 注册账号
2. 登录后进入「个人中心」→「接口TOKEN」
3. 复制你的 Token（格式如：`2f4f8a9b3c5d7e1f2a4b6c8d0e2f4a6b`）
4. 配置到 GitHub Secrets：`TUSHARE_TOKEN`

> 💡 **提示**：
> - 新用户注册送 100 积分，足够日常使用
> - 完善个人信息可获得更多积分
> - 积分用于兑换数据接口调用次数

### Q8：配置 Tushare 和不配置有什么区别？

| 数据维度 | 不配置 Tushare | 配置 Tushare |
|---------|---------------|-------------|
| 行情数据 | ✅ AkShare 提供 | ✅ 更稳定，多源备份 |
| 财务数据 | ⚠️ 基础数据 | ✅ 完整财务报表 |
| 龙虎榜 | ❌ 无 | ✅ 每日龙虎榜数据 |
| 机构持仓 | ❌ 无 | ✅ 基金持仓、北向资金 |
| 融资融券 | ❌ 无 | ✅ 两融余额、买卖数据 |
| 板块排名 | ⚠️ 基础数据 | ✅ 更准确的板块数据 |

**结论**：不配置也能正常使用，配置了数据分析更全面。

### Q9：中国大陆用户推荐配置？

**AI 模型**：
- 首选：AIHubMix（一个 Key 用所有模型）
- 备选：DeepSeek 或 通义千问

**推送渠道**：
- 首选：企业微信 或 飞书
- 备选：邮箱 或 PushPlus

**新闻搜索**：
- 首选：博查搜索 或 MiniMax

**数据源增强**（可选）：
- Tushare：获取更详细的财务数据、龙虎榜、机构持仓
  - 注册地址：[https://tushare.pro](https://tushare.pro)
  - 免费版已足够使用，积分可兑换更多数据

---

## 架构图

```
┌─────────────────────────────────────────────────────────────┐
│                    GitHub Actions                            │
│                   (工作日 18:00 执行)                         │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      数据采集层                               │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐        │
│  │ AkShare │  │Tushare  │  │ YFinance│  │  新闻API │        │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘        │
│       └────────────┴────────────┴────────────┘              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      AI 分析层                               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              LiteLLM 统一接口                         │   │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐       │   │
│  │  │AIHubMix│ │DeepSeek│ │ 通义千问 │ │ Gemini │       │   │
│  │  └────────┘ └────────┘ └────────┘ └────────┘       │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      推送层                                  │
│  ┌──────┐ ┌──────┐ ┌────────┐ ┌──────────┐ ┌──────┐       │
│  │企业微信│ │ 飞书 │ │PushPlus│ │ Server酱 │ │ 邮箱 │       │
│  └──────┘ └──────┘ └────────┘ └──────────┘ └──────┘       │
└─────────────────────────────────────────────────────────────┘
```

---

## 快速配置清单（中国大陆用户 - A股/基金）

### 最小配置（5 分钟完成）

| 步骤 | Secret 名称 | 值 |
|------|-------------|-----|
| 1 | `STOCK_LIST` | `600519,300750,510300` |
| 2 | `AIHUBMIX_KEY` | 你的 AIHubMix Key |
| 3 | `WECHAT_WEBHOOK_URL` | 你的企业微信 Webhook |

### 推荐配置

| 步骤 | Secret 名称 | 值 |
|------|-------------|-----|
| 1 | `STOCK_LIST` | 你的A股/基金代码列表 |
| 2 | `AIHUBMIX_KEY` | 你的 AIHubMix Key |
| 3 | `WECHAT_WEBHOOK_URL` | 你的企业微信 Webhook |
| 4 | `FEISHU_WEBHOOK_URL` | 你的飞书 Webhook（可选） |
| 5 | `BOCHA_API_KEYS` | 你的博查搜索 Key（可选） |

### A股/基金配置示例

```
# 股票 + ETF 组合
STOCK_LIST=600519,300750,510300,159915

# 纯股票组合
STOCK_LIST=600519,000001,300750,688981

# 纯基金组合
STOCK_LIST=510300,159915,161725,513500
```

---

## 总结

| 步骤 | 操作 | 时间 |
|------|------|------|
| 1 | Fork 仓库 | 10秒 |
| 2 | 配置 STOCK_LIST | 30秒 |
| 3 | 配置 AI 模型 API Key | 1分钟 |
| 4 | 配置推送渠道 | 1分钟 |
| 5 | 启用 Actions | 10秒 |
| 6 | 手动测试 | 2-5分钟 |

**总计**：约 **5 分钟**完成部署，零成本，无需服务器！

---

## 相关链接

- 项目地址：[https://github.com/ZhuLinsen/daily_stock_analysis](https://github.com/ZhuLinsen/daily_stock_analysis)
- AIHubMix：[https://aihubmix.com](https://aihubmix.com)
- DeepSeek：[https://platform.deepseek.com](https://platform.deepseek.com)
- 通义千问：[https://dashscope.console.aliyun.com](https://dashscope.console.aliyun.com)
- PushPlus：[https://www.pushplus.plus](https://www.pushplus.plus)
- Server酱：[https://sct.ftqq.com](https://sct.ftqq.com)
