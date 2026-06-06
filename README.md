# 📓 open-notebook-wrap

> 基于 [open-notebook](https://github.com/lfnovo/open-notebook) 的开源播客 AI 工具封装 — 把播客/音频转成 AI 摘要知识库，**支持私有部署**，完全可控。

## 🎯 是什么

open-notebook 是开源的 Notebook LM 实现，支持：
- 📝 **播客/音频转文字**（Whisper ASR）
- 🧠 **AI 摘要生成**（基于 LLM）
- 📚 **知识库构建**（向量嵌入 + 检索）
- 🔊 **音频对话**（语音交互）

open-notebook-wrap 在原版基础上增加：
- ✅ **Docker 一键部署**
- ✅ **中文优化配置**
- ✅ **API 接口文档**
- ✅ **生产级部署指南**

## 🚀 快速开始

### Docker 部署（推荐，5分钟启动）

```bash
# 克隆项目
git clone https://github.com/q15004040209-creator/open-notebook-wrap.git
cd open-notebook-wrap

# 启动所有服务
docker-compose up -d

# 访问 http://localhost:3000
```

### 本地开发

```bash
# 安装依赖
pip install -r requirements.txt

# 启动后端 API
python run_api.py

# 启动前端
cd frontend && npm install && npm run dev
```

## 📁 项目结构

```
open-notebook-wrap/
├── README.md
├── demo/                     # 完整 Demo
│   ├── app/                  # 前端 (Next.js)
│   ├── api/                  # 后端 API
│   │   ├── upload.py         # 音频上传
│   │   ├── transcribe.py     # 语音转文字 (Whisper)
│   │   ├── summarize.py      # AI 摘要生成
│   │   └── search.py         # 知识检索
│   ├── models/              # 数据模型
│   ├── docker-compose.yml    # Docker 配置
│   └── requirements.txt
└── docs/
    ├── API.md               # API 接口文档
    └── DEPLOY.md            # 部署指南
```

## 🔌 API 接口

### 1. 上传音频

```
POST /api/upload
Content-Type: multipart/form-data

file: <audio_file>  # 支持 mp3/wav/m4a/ogg

Response:
{
  "id": "ep_001",
  "filename": "podcast_ep1.mp3",
  "duration_seconds": 1847,
  "status": "uploaded"
}
```

### 2. 转写

```
POST /api/transcribe
{"id": "ep_001"}

Response:
{
  "id": "ep_001",
  "transcript": "今天我们来聊聊 AI Agent 的发展...",
  "language": "zh",
  "segments": [
    {"start": 0.0, "end": 12.5, "text": "今天我们来聊聊 AI Agent 的发展..."},
    ...
  ]
}
```

### 3. 生成摘要

```
POST /api/summarize
{
  "id": "ep_001",
  "style": "detailed"  // "brief" | "detailed" | "bullet_points"
}

Response:
{
  "id": "ep_001",
  "summary": "本文讨论了 AI Agent 的三个发展阶段...",
  "key_points": [
    "2024年是AI Agent元年",
    "多模态是核心能力",
    "垂直领域落地最快"
  ],
  "timestamps": [
    {"time": "00:05", "topic": "AI Agent 定义"},
    {"time": "00:15", "topic": "技术演进历史"}
  ]
}
```

### 4. 检索知识库

```
POST /api/search
{
  "query": "AI Agent 如何提升生产力？",
  "top_k": 5
}

Response:
{
  "results": [
    {
      "source": "ep_001",
      "text": "AI Agent 通过自动化重复任务来提升生产力...",
      "timestamp": "00:23:15",
      "score": 0.92
    }
  ]
}
```

## 📊 系统架构

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│  前端 UI    │───▶│  FastAPI     │───▶│  Whisper    │
│ (Next.js)   │    │  Backend     │    │  (ASR)      │
└─────────────┘    └──────────────┘    └─────────────┘
                         │
                         ▼
                   ┌──────────────┐    ┌─────────────┐
                   │  PostgreSQL  │───▶│  LLM API    │
                   │  (向量存储)   │    │ (GPT-4o等)  │
                   └──────────────┘    └─────────────┘
```

## 🐳 Docker 配置

```yaml
# docker-compose.yml 核心配置
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - WHISPER_MODEL=base
    volumes:
      - ./data:/app/data

  frontend:
    image: node:20-alpine
    ports:
      - "3000:3000"
    depends_on:
      - api
```

## ⚙️ 环境变量

| 变量 | 说明 | 必需 |
|------|------|------|
| `OPENAI_API_KEY` | OpenAI API Key | 是 |
| `WHISPER_MODEL` | Whisper 模型大小 | 否（默认 base） |
| `DATABASE_URL` | PostgreSQL 连接字符串 | 否 |
| `JWT_SECRET` | JWT 签名密钥 | 是（生产环境） |

## 📄 许可证

基于 Apache 2.0，商用友好。

## 🔗 链接

- 原始项目：https://github.com/lfnovo/open-notebook
- 官方文档：https://github.com/lfnovo/open-notebook#readme
