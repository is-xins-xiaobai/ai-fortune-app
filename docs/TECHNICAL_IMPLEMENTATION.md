# AI算命应用 - 技术实现方案

**项目名称**: 天机阁 (TianjiBot)  
**文档版本**: v1.0  
**创建时间**: 2026-03-19

---

## 🏗️ 系统架构

### 整体架构图

```
┌─────────────────────────────────────────────────────────────┐
│                         客户端层                              │
├─────────────────────────────────────────────────────────────┤
│  iOS App (Swift)  │  Android App (Kotlin)  │  Web (React)   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      API Gateway (Nginx/Kong)                │
│                    - 限流  - 鉴权  - 路由                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                         微服务层                              │
├──────────────┬──────────────┬──────────────┬────────────────┤
│ User Service │Fortune Service│ AI Service  │Payment Service │
│   (用户管理)  │  (命理计算)    │  (LLM对话)   │   (支付订阅)    │
├──────────────┼──────────────┼──────────────┼────────────────┤
│Notification  │  Analytics   │   Admin      │   CDN/Storage  │
│  (推送通知)   │  (数据分析)    │  (管理后台)   │  (文件存储)     │
└──────────────┴──────────────┴──────────────┴────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                         数据层                                │
├──────────────┬──────────────┬──────────────┬────────────────┤
│ PostgreSQL   │   MongoDB    │    Redis     │   ElasticSearch│
│  (关系数据)   │  (文档数据)    │   (缓存)      │   (搜索引擎)    │
└──────────────┴──────────────┴──────────────┴────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      AI/ML 基础设施                           │
├──────────────┬──────────────┬──────────────────────────────┤
│  LLM服务     │  命理计算引擎  │  CV模型 (手相面相)            │
│ (GPT/Llama)  │  (八字/紫微)   │  (YOLOv8)                   │
└──────────────┴──────────────┴──────────────────────────────┘
```

---

## 📱 前端技术栈

### 方案A: Flutter (推荐 - 快速跨平台)

**优势**:
- 一套代码同时支持 iOS + Android + Web
- 性能接近原生
- UI组件丰富，渲染流畅
- 开发效率高，节省成本

**技术选型**:
```yaml
框架: Flutter 3.x
语言: Dart
状态管理: Riverpod (推荐) 或 Bloc
网络请求: Dio
本地存储: Hive / SharedPreferences
路由: GoRouter
依赖注入: get_it
```

**核心依赖**:
```yaml
# pubspec.yaml
dependencies:
  flutter: sdk: flutter
  
  # 网络
  dio: ^5.0.0
  retrofit: ^4.0.0
  
  # 状态管理
  flutter_riverpod: ^2.3.0
  
  # UI组件
  flutter_screenutil: ^5.8.0  # 屏幕适配
  animations: ^2.0.0          # 动画
  lottie: ^2.3.0              # Lottie动画
  
  # 本地存储
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  
  # 路由
  go_router: ^7.0.0
  
  # 工具
  intl: ^0.18.0               # 国际化
  logger: ^2.0.0              # 日志
  image_picker: ^1.0.0        # 图片选择(手相面相)
  
  # 支付
  alipay_kit: ^6.0.0
  wechat_kit: ^3.0.0
  
  # 推送
  flutter_local_notifications: ^15.0.0
  firebase_messaging: ^14.6.0
```

**目录结构**:
```
lib/
├── main.dart
├── app/
│   ├── routes.dart           # 路由配置
│   ├── theme.dart            # 主题
│   └── constants.dart        # 常量
├── core/
│   ├── network/              # 网络层
│   │   ├── api_client.dart
│   │   └── api_endpoints.dart
│   ├── storage/              # 本地存储
│   └── utils/                # 工具类
├── features/                 # 功能模块
│   ├── auth/                 # 登录注册
│   ├── fortune/              # 命理功能
│   │   ├── bazi/             # 八字
│   │   ├── ziwei/            # 紫微
│   │   └── astro/            # 星座
│   ├── chat/                 # AI对话
│   ├── profile/              # 个人中心
│   └── payment/              # 支付订阅
├── shared/
│   ├── widgets/              # 公共组件
│   ├── models/               # 数据模型
│   └── providers/            # 状态管理
└── l10n/                     # 国际化
```

---

### 方案B: 原生开发 (高性能要求)

**iOS**:
```swift
框架: SwiftUI + Combine
架构: MVVM
网络: Alamofire
持久化: CoreData / Realm
```

**Android**:
```kotlin
框架: Jetpack Compose
架构: MVVM + Repository
网络: Retrofit + OkHttp
持久化: Room
```

---

## 🔧 后端技术栈

### 核心框架: FastAPI (Python)

**为什么选FastAPI**:
- 性能优秀 (媲美Node.js/Go)
- 异步支持 (async/await)
- 自动API文档 (Swagger)
- 类型检查 (Pydantic)
- AI/ML生态丰富 (与Python AI库无缝集成)

**技术选型**:
```python
框架: FastAPI 0.100+
异步: asyncio, uvicorn
ORM: SQLAlchemy 2.0 (异步)
验证: Pydantic v2
认证: JWT (python-jose)
任务队列: Celery + Redis
WebSocket: FastAPI原生支持
```

**核心依赖**:
```txt
# requirements.txt
fastapi==0.100.0
uvicorn[standard]==0.23.0
sqlalchemy[asyncio]==2.0.0
asyncpg==0.28.0              # PostgreSQL异步驱动
redis==4.6.0
celery==5.3.0
pydantic==2.0.0
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
alembic==1.11.0              # 数据库迁移
python-multipart==0.0.6      # 文件上传
aiofiles==23.1.0

# AI相关
openai==0.27.0               # GPT API
langchain==0.0.200           # LLM工具链
sentence-transformers==2.2.0 # 向量化
chromadb==0.4.0              # 向量数据库

# 工具
httpx==0.24.0
python-dotenv==1.0.0
loguru==0.7.0
sentry-sdk==1.26.0
```

**项目结构**:
```
backend/
├── app/
│   ├── main.py                    # 应用入口
│   ├── config.py                  # 配置
│   ├── dependencies.py            # 依赖注入
│   │
│   ├── api/                       # API路由
│   │   ├── v1/
│   │   │   ├── auth.py            # 认证
│   │   │   ├── fortune.py         # 命理
│   │   │   ├── chat.py            # AI对话
│   │   │   ├── payment.py         # 支付
│   │   │   └── user.py            # 用户
│   │   └── dependencies/
│   │
│   ├── models/                    # SQLAlchemy模型
│   │   ├── user.py
│   │   ├── fortune_record.py
│   │   ├── subscription.py
│   │   └── conversation.py
│   │
│   ├── schemas/                   # Pydantic Schema
│   │   ├── user.py
│   │   ├── fortune.py
│   │   └── chat.py
│   │
│   ├── services/                  # 业务逻辑
│   │   ├── auth_service.py
│   │   ├── fortune_service.py     # 命理计算
│   │   ├── ai_service.py          # LLM调用
│   │   ├── payment_service.py
│   │   └── notification_service.py
│   │
│   ├── core/                      # 核心功能
│   │   ├── fortune/               # 命理计算引擎
│   │   │   ├── bazi.py            # 八字
│   │   │   ├── ziwei.py           # 紫微
│   │   │   ├── astro.py           # 星座
│   │   │   └── lunar.py           # 农历算法
│   │   ├── ai/                    # AI相关
│   │   │   ├── llm_client.py      # LLM客户端
│   │   │   ├── prompts.py         # Prompt模板
│   │   │   └── rag.py             # RAG检索
│   │   └── security.py            # 安全
│   │
│   ├── db/                        # 数据库
│   │   ├── base.py
│   │   ├── session.py
│   │   └── migrations/            # Alembic迁移
│   │
│   └── utils/                     # 工具函数
│       ├── lunar_calendar.py      # 万年历
│       ├── solarterm.py           # 节气计算
│       └── helpers.py
│
├── tests/                         # 测试
│   ├── unit/
│   └── integration/
│
├── alembic.ini                    # 数据库迁移配置
├── Dockerfile
├── docker-compose.yml
└── requirements.txt
```

---

## 🧠 AI/ML 核心实现

### 1. LLM服务架构

#### 方案A: 微调开源模型 (推荐)

**选择基础模型**:
```python
# 推荐: Qwen2.5-72B-Instruct (阿里通义千问)
# 优势: 中文能力强, 指令遵循好, 免费商用

from transformers import AutoModelForCausalLM, AutoTokenizer

model_name = "Qwen/Qwen2.5-72B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    device_map="auto",
    torch_dtype=torch.bfloat16,
    load_in_4bit=True  # 量化节省显存
)
```

**微调数据准备**:
```json
// 训练数据格式 (JSONL)
{
  "messages": [
    {
      "role": "system",
      "content": "你是一位精通中国传统命理学的AI算命师,擅长八字、紫微斗数、周易等。你的回答应该专业、温和、有启发性。"
    },
    {
      "role": "user",
      "content": "我是1990年5月15日早上8点出生的,请帮我分析八字。"
    },
    {
      "role": "assistant",
      "content": "好的,让我为您排盘分析...\n\n**生辰信息**\n公历: 1990年5月15日 8:00\n农历: 庚午年四月廿一 辰时\n\n**八字排盘**\n年柱: 庚午 (路旁土)\n月柱: 辛巳 (白蜡金)\n日柱: 癸亥 (大海水)\n时柱: 丙辰 (沙中土)\n\n**五行分析**\n金: 2  水: 2  木: 0  火: 3  土: 1\n五行缺木,火旺,日主癸水在巳月处死地,身弱。\n\n**格局判断**\n您的命局属于从弱格,宜顺从火土金的力量...\n\n[详细解读]..."
    }
  ]
}
```

**微调方法**:
```python
# 使用 LoRA (Low-Rank Adaptation) 微调
# 优势: 节省显存, 训练快, 效果好

from peft import LoraConfig, get_peft_model, TaskType

lora_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=16,                    # LoRA秩
    lora_alpha=32,
    lora_dropout=0.1,
    target_modules=["q_proj", "v_proj"]  # 要微调的层
)

model = get_peft_model(model, lora_config)

# 训练参数
training_args = TrainingArguments(
    output_dir="./models/tianji-qwen-lora",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=8,
    learning_rate=2e-4,
    fp16=True,
    save_strategy="epoch",
    logging_steps=10
)
```

**部署推理服务**:
```python
# 使用 vLLM 加速推理 (比HuggingFace快10-20倍)
from vllm import LLM, SamplingParams

llm = LLM(
    model="./models/tianji-qwen-lora",
    tensor_parallel_size=2,  # 多GPU并行
    dtype="bfloat16"
)

sampling_params = SamplingParams(
    temperature=0.7,
    top_p=0.9,
    max_tokens=2048
)

def generate_fortune(prompt: str) -> str:
    outputs = llm.generate([prompt], sampling_params)
    return outputs[0].outputs[0].text
```

**成本估算**:
- GPU租赁: 2x A100 (80GB) ≈ ¥30/小时
- 训练时间: 约50-100小时
- 总成本: ¥1500-3000 (一次性)
- 推理成本: ¥0.01-0.02/次 (自建服务器长期更划算)

---

#### 方案B: GPT-4 API (快速验证)

```python
# app/core/ai/llm_client.py
from openai import AsyncOpenAI
import os

client = AsyncOpenAI(api_key=os.getenv("OPENAI_API_KEY"))

async def chat_completion(
    messages: list[dict],
    model: str = "gpt-4-turbo-preview",
    temperature: float = 0.7
) -> str:
    """
    调用GPT-4生成回复
    """
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=temperature,
        max_tokens=2048
    )
    return response.choices[0].message.content

# 使用示例
async def analyze_bazi(birth_info: dict) -> str:
    system_prompt = """
    你是一位精通八字命理的大师,请根据用户提供的生辰信息,
    进行详细的八字排盘和分析。回复应包括:
    1. 八字四柱
    2. 五行强弱
    3. 格局判断
    4. 运势解读
    5. 建议指引
    """
    
    user_prompt = f"""
    请分析以下生辰信息:
    出生时间: {birth_info['datetime']}
    性别: {birth_info['gender']}
    """
    
    messages = [
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": user_prompt}
    ]
    
    return await chat_completion(messages)
```

**成本对比**:
- GPT-4 Turbo: $0.01/1K tokens
- 平均单次咨询: ~2K tokens (输入+输出)
- 成本: ~¥0.15/次
- 月度1万次咨询 ≈ ¥1500

---

### 2. 命理计算引擎

#### 八字排盘算法

```python
# app/core/fortune/bazi.py
from datetime import datetime
from lunar_python import Lunar, Solar

class BaziCalculator:
    """八字排盘计算器"""
    
    # 天干
    TIANGAN = ["甲", "乙", "丙", "丁", "戊", "己", "庚", "辛", "壬", "癸"]
    
    # 地支
    DIZHI = ["子", "丑", "寅", "卯", "辰", "巳", "午", "未", "申", "酉", "戌", "亥"]
    
    # 五行
    WUXING = {
        "甲": "木", "乙": "木", "丙": "火", "丁": "火",
        "戊": "土", "己": "土", "庚": "金", "辛": "金",
        "壬": "水", "癸": "水"
    }
    
    def __init__(self, birth_datetime: datetime, gender: str):
        self.birth_datetime = birth_datetime
        self.gender = gender
        
        # 转换为农历
        solar = Solar.fromDate(birth_datetime)
        self.lunar = Lunar.fromSolar(solar)
    
    def get_bazi(self) -> dict:
        """
        获取八字四柱
        
        Returns:
            {
                "year": {"gan": "庚", "zhi": "午"},
                "month": {"gan": "辛", "zhi": "巳"},
                "day": {"gan": "癸", "zhi": "亥"},
                "hour": {"gan": "丙", "zhi": "辰"}
            }
        """
        year_gan = self.lunar.getYearGan()
        year_zhi = self.lunar.getYearZhi()
        
        month_gan = self.lunar.getMonthGan()
        month_zhi = self.lunar.getMonthZhi()
        
        day_gan = self.lunar.getDayGan()
        day_zhi = self.lunar.getDayZhi()
        
        hour_zhi = self.lunar.getTimeZhi()
        hour_gan = self._get_hour_gan(day_gan, hour_zhi)
        
        return {
            "year": {"gan": year_gan, "zhi": year_zhi},
            "month": {"gan": month_gan, "zhi": month_zhi},
            "day": {"gan": day_gan, "zhi": day_zhi},
            "hour": {"gan": hour_gan, "zhi": hour_zhi}
        }
    
    def analyze_wuxing(self) -> dict:
        """
        五行强弱分析
        
        Returns:
            {
                "金": 2, "木": 0, "水": 2, "火": 3, "土": 1,
                "missing": ["木"],
                "strong": "火",
                "weak": "木"
            }
        """
        bazi = self.get_bazi()
        wuxing_count = {"金": 0, "木": 0, "水": 0, "火": 0, "土": 0}
        
        # 统计天干五行
        for pillar in bazi.values():
            gan = pillar["gan"]
            wuxing_count[self.WUXING[gan]] += 1
        
        # 统计地支五行 (简化版,实际更复杂)
        # TODO: 考虑地支藏干
        
        # 找出缺失、旺盛、衰弱的五行
        missing = [wx for wx, count in wuxing_count.items() if count == 0]
        strong = max(wuxing_count, key=wuxing_count.get)
        weak = min(wuxing_count, key=wuxing_count.get)
        
        return {
            **wuxing_count,
            "missing": missing,
            "strong": strong,
            "weak": weak
        }
    
    def get_dayun(self) -> list[dict]:
        """
        计算大运
        
        Returns:
            [
                {"age": 8, "gan": "壬", "zhi": "午", "start_year": 1998},
                {"age": 18, "gan": "癸", "zhi": "未", "start_year": 2008},
                ...
            ]
        """
        # TODO: 实现大运计算逻辑
        pass
    
    def _get_hour_gan(self, day_gan: str, hour_zhi: str) -> str:
        """根据日干和时支推算时干 (日上起时法)"""
        # 五虎遁: 甲己起甲子, 乙庚起丙子...
        # 简化实现
        pass

# 使用示例
calculator = BaziCalculator(
    birth_datetime=datetime(1990, 5, 15, 8, 0),
    gender="男"
)

bazi = calculator.get_bazi()
wuxing = calculator.analyze_wuxing()

print(f"八字: {bazi}")
print(f"五行: {wuxing}")
```

**依赖库**:
```python
# 推荐使用: lunar-python (6tail开源的阴阳历转换库)
pip install lunar-python
```

---

#### 紫微斗数排盘

```python
# app/core/fortune/ziwei.py
class ZiweiCalculator:
    """紫微斗数排盘"""
    
    # 十二宫位
    PALACES = [
        "命宫", "兄弟", "夫妻", "子女", "财帛", "疾厄",
        "迁移", "奴仆", "官禄", "田宅", "福德", "父母"
    ]
    
    # 主星
    MAIN_STARS = {
        "紫微": {"五行": "土", "阴阳": "阳"},
        "天机": {"五行": "木", "阴阳": "阴"},
        "太阳": {"五行": "火", "阴阳": "阳"},
        # ... 共14颗主星
    }
    
    def __init__(self, birth_datetime: datetime, gender: str):
        self.birth_datetime = birth_datetime
        self.gender = gender
        self.lunar = Lunar.fromSolar(Solar.fromDate(birth_datetime))
    
    def arrange_palace(self) -> dict:
        """
        安排十二宫位和星曜
        
        Returns:
            {
                "命宫": {
                    "palace": "命宫",
                    "position": "寅",
                    "stars": ["紫微", "天府"],
                    "description": "..."
                },
                ...
            }
        """
        # TODO: 实现安星算法
        # 1. 定命宫 (根据生月和生时)
        # 2. 安紫微星系 (根据生日和命宫)
        # 3. 安天府星系
        # 4. 安其他辅星、煞星
        pass
```

---

### 3. RAG (检索增强生成)

为了提高AI回答的准确性,我们可以构建命理知识库,使用RAG技术:

```python
# app/core/ai/rag.py
from langchain.embeddings import HuggingFaceEmbeddings
from langchain.vectorstores import Chroma
from langchain.text_splitter import RecursiveCharacterTextSplitter

class FortuneKnowledgeBase:
    """命理知识库 (RAG)"""
    
    def __init__(self):
        # 使用中文嵌入模型
        self.embeddings = HuggingFaceEmbeddings(
            model_name="BAAI/bge-large-zh-v1.5"
        )
        
        # 向量数据库
        self.vectorstore = Chroma(
            persist_directory="./data/chroma_db",
            embedding_function=self.embeddings
        )
    
    def build_knowledge_base(self, documents_path: str):
        """
        构建知识库
        
        Args:
            documents_path: 命理典籍文本目录
                - 易经.txt
                - 滴天髓.txt
                - 紫微斗数全书.txt
                - 三命通会.txt
                ...
        """
        # 加载文档
        from langchain.document_loaders import DirectoryLoader
        
        loader = DirectoryLoader(
            documents_path,
            glob="**/*.txt"
        )
        documents = loader.load()
        
        # 分割文本
        text_splitter = RecursiveCharacterTextSplitter(
            chunk_size=500,
            chunk_overlap=50
        )
        splits = text_splitter.split_documents(documents)
        
        # 存入向量数据库
        self.vectorstore.add_documents(splits)
        self.vectorstore.persist()
    
    def retrieve(self, query: str, k: int = 3) -> list[str]:
        """
        检索相关知识
        
        Args:
            query: 查询问题
            k: 返回top-k结果
        
        Returns:
            ["相关知识片段1", "相关知识片段2", ...]
        """
        docs = self.vectorstore.similarity_search(query, k=k)
        return [doc.page_content for doc in docs]

# 使用示例
kb = FortuneKnowledgeBase()

# 首次构建知识库
# kb.build_knowledge_base("./data/fortune_books/")

# 检索
query = "八字中火旺缺木的人应该如何化解?"
knowledge = kb.retrieve(query, k=3)

# 结合检索结果生成回答
system_prompt = f"""
你是命理大师,请参考以下古籍知识回答问题:

{'\n\n'.join(knowledge)}

请基于上述知识,用现代语言解答用户问题。
"""
```

---

## 💾 数据库设计

### PostgreSQL 表结构

```sql
-- 用户表
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    phone VARCHAR(20) UNIQUE,
    email VARCHAR(100),
    nickname VARCHAR(50),
    avatar_url TEXT,
    
    -- 生辰信息
    birth_datetime TIMESTAMP,
    birth_location VARCHAR(100),
    gender VARCHAR(10),
    lunar_birth JSONB,  -- 农历生日 {"year": 1990, "month": 4, "day": 21}
    
    -- 会员信息
    subscription_tier VARCHAR(20) DEFAULT 'free',  -- free/premium/pro
    subscription_end_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 命盘记录
CREATE TABLE fortune_charts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    
    chart_type VARCHAR(20),  -- bazi/ziwei/astro
    chart_data JSONB,        -- 排盘结果 (JSON格式存储)
    
    created_at TIMESTAMP DEFAULT NOW()
);

-- 咨询记录
CREATE TABLE consultations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    
    consultation_type VARCHAR(50),  -- career/love/wealth/health
    question TEXT,
    answer TEXT,
    ai_model VARCHAR(50),           -- gpt-4/qwen-72b
    
    -- 评价
    rating INTEGER,                 -- 1-5星
    feedback TEXT,
    
    created_at TIMESTAMP DEFAULT NOW()
);

-- 订单表
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    
    order_type VARCHAR(20),         -- subscription/single
    product_id VARCHAR(50),
    amount DECIMAL(10, 2),
    
    payment_method VARCHAR(20),     -- alipay/wechat
    payment_status VARCHAR(20),     -- pending/paid/failed/refunded
    payment_at TIMESTAMP,
    
    created_at TIMESTAMP DEFAULT NOW()
);

-- 每日运势缓存
CREATE TABLE daily_fortune_cache (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    
    date DATE,
    fortune_content JSONB,  -- {"lucky_color": "红色", "lucky_number": 7, ...}
    
    created_at TIMESTAMP DEFAULT NOW(),
    
    UNIQUE(user_id, date)
);

-- 索引
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_consultations_user_id ON consultations(user_id);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_daily_fortune_user_date ON daily_fortune_cache(user_id, date);
```

### MongoDB 集合 (文档型数据)

```javascript
// 对话历史 (conversations集合)
{
  _id: ObjectId("..."),
  user_id: "uuid",
  session_id: "uuid",
  messages: [
    {
      role: "user",
      content: "我最近事业不顺,请指点一下",
      timestamp: ISODate("2026-03-19T10:00:00Z")
    },
    {
      role: "assistant",
      content: "根据您的命盘...",
      timestamp: ISODate("2026-03-19T10:00:05Z"),
      tokens_used: 500
    }
  ],
  created_at: ISODate("2026-03-19T10:00:00Z"),
  updated_at: ISODate("2026-03-19T10:05:00Z")
}

// 用户行为日志 (user_actions集合)
{
  _id: ObjectId("..."),
  user_id: "uuid",
  action: "view_fortune",  // view_fortune/ask_question/share/subscribe
  metadata: {
    fortune_type: "daily",
    screen: "home"
  },
  timestamp: ISODate("2026-03-19T10:00:00Z")
}
```

---

## 🔐 安全与鉴权

### JWT认证

```python
# app/core/security.py
from datetime import datetime, timedelta
from jose import JWTError, jwt
from passlib.context import CryptContext

SECRET_KEY = os.getenv("JWT_SECRET_KEY")
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 60 * 24 * 7  # 7天

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def create_access_token(data: dict) -> str:
    """生成JWT token"""
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

def verify_token(token: str) -> dict:
    """验证JWT token"""
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return payload
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid token")

def hash_password(password: str) -> str:
    """密码哈希"""
    return pwd_context.hash(password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    """验证密码"""
    return pwd_context.verify(plain_password, hashed_password)
```

### API限流

```python
# 使用 slowapi 实现限流
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

@app.post("/api/v1/chat")
@limiter.limit("10/minute")  # 每分钟最多10次
async def chat(request: Request, ...):
    ...
```

---

## 📊 监控与日志

### 日志系统

```python
# app/utils/logger.py
from loguru import logger
import sys

# 配置日志
logger.remove()  # 移除默认handler

# 控制台输出
logger.add(
    sys.stdout,
    level="INFO",
    format="<green>{time:YYYY-MM-DD HH:mm:ss}</green> | <level>{level: <8}</level> | <cyan>{name}</cyan>:<cyan>{function}</cyan> - <level>{message}</level>"
)

# 文件输出
logger.add(
    "logs/app_{time:YYYY-MM-DD}.log",
    rotation="00:00",  # 每天轮转
    retention="30 days",
    level="DEBUG"
)

# 错误日志单独记录
logger.add(
    "logs/error_{time:YYYY-MM-DD}.log",
    rotation="00:00",
    retention="90 days",
    level="ERROR"
)
```

### Sentry错误追踪

```python
import sentry_sdk
from sentry_sdk.integrations.asgi import SentryAsgiMiddleware

sentry_sdk.init(
    dsn=os.getenv("SENTRY_DSN"),
    traces_sample_rate=0.1,  # 性能监控采样率
    environment="production"
)

app.add_middleware(SentryAsgiMiddleware)
```

---

## 🚀 部署方案

### Docker容器化

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制代码
COPY ./app /app/app

# 运行
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  # FastAPI后端
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@postgres:5432/tianji
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
    restart: always
  
  # PostgreSQL
  postgres:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: tianji
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: always
  
  # Redis
  redis:
    image: redis:7-alpine
    restart: always
  
  # Nginx (反向代理)
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - api
    restart: always

volumes:
  postgres_data:
```

### Kubernetes (生产环境)

```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tianji-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: tianji-api
  template:
    metadata:
      labels:
        app: tianji-api
    spec:
      containers:
      - name: api
        image: tianji/api:latest
        ports:
        - containerPort: 8000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: tianji-secrets
              key: database-url
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "2000m"
---
apiVersion: v1
kind: Service
metadata:
  name: tianji-api-service
spec:
  selector:
    app: tianji-api
  ports:
  - port: 80
    targetPort: 8000
  type: LoadBalancer
```

---

## 📈 性能优化

### 1. 缓存策略

```python
# 每日运势缓存 (Redis)
import redis.asyncio as redis

redis_client = redis.from_url("redis://localhost")

async def get_daily_fortune(user_id: str, date: str):
    cache_key = f"fortune:daily:{user_id}:{date}"
    
    # 先查缓存
    cached = await redis_client.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # 缓存未命中,计算运势
    fortune = await calculate_daily_fortune(user_id, date)
    
    # 存入缓存 (24小时过期)
    await redis_client.setex(
        cache_key,
        86400,
        json.dumps(fortune)
    )
    
    return fortune
```

### 2. 数据库连接池

```python
# app/db/session.py
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

engine = create_async_engine(
    DATABASE_URL,
    echo=False,
    pool_size=20,        # 连接池大小
    max_overflow=10,     # 最大溢出连接
    pool_pre_ping=True   # 连接前检查
)

async_session = sessionmaker(
    engine,
    class_=AsyncSession,
    expire_on_commit=False
)
```

### 3. 异步任务队列

```python
# 使用Celery处理耗时任务
from celery import Celery

celery_app = Celery(
    "tianji",
    broker="redis://localhost:6379",
    backend="redis://localhost:6379"
)

@celery_app.task
def generate_annual_report(user_id: str):
    """生成年度报告 (异步任务)"""
    # 1. 计算全年运势
    # 2. 生成PDF
    # 3. 上传到CDN
    # 4. 通知用户
    pass

# API调用
@app.post("/api/v1/report/annual")
async def request_annual_report(user_id: str):
    # 异步任务
    task = generate_annual_report.delay(user_id)
    
    return {"task_id": task.id, "status": "processing"}
```

---

## 🧪 测试策略

```python
# tests/test_bazi.py
import pytest
from app.core.fortune.bazi import BaziCalculator
from datetime import datetime

def test_bazi_calculation():
    """测试八字排盘"""
    calculator = BaziCalculator(
        birth_datetime=datetime(1990, 5, 15, 8, 0),
        gender="男"
    )
    
    bazi = calculator.get_bazi()
    
    assert bazi["year"]["gan"] == "庚"
    assert bazi["year"]["zhi"] == "午"
    assert bazi["day"]["gan"] == "癸"

def test_wuxing_analysis():
    """测试五行分析"""
    calculator = BaziCalculator(
        birth_datetime=datetime(1990, 5, 15, 8, 0),
        gender="男"
    )
    
    wuxing = calculator.analyze_wuxing()
    
    assert "木" in wuxing["missing"]
    assert wuxing["strong"] == "火"

# 集成测试
@pytest.mark.asyncio
async def test_fortune_api():
    """测试命理API"""
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.post(
            "/api/v1/fortune/bazi",
            json={
                "birth_datetime": "1990-05-15T08:00:00",
                "gender": "男"
            },
            headers={"Authorization": f"Bearer {test_token}"}
        )
        
        assert response.status_code == 200
        data = response.json()
        assert "bazi" in data
        assert "wuxing" in data
```

---

## 📚 技术栈总结

| 层级 | 技术选型 | 说明 |
|------|---------|------|
| **前端** | Flutter | 跨平台,高性能 |
| **后端** | FastAPI + Python | 异步高性能,AI生态好 |
| **AI/ML** | Qwen2.5 + LoRA微调 | 中文能力强,成本可控 |
| **数据库** | PostgreSQL + MongoDB | 关系型+文档型 |
| **缓存** | Redis | 高性能缓存 |
| **任务队列** | Celery | 异步任务处理 |
| **容器化** | Docker + K8s | 微服务部署 |
| **监控** | Sentry + Grafana | 错误追踪+性能监控 |
| **CDN** | CloudFlare | 全球加速 |

---

## 🎯 开发优先级

### Phase 1: MVP (3个月)
1. ✅ 用户认证 (手机号+JWT)
2. ✅ 八字排盘引擎
3. ✅ GPT-4 API集成
4. ✅ 基础对话功能
5. ✅ 支付宝/微信支付
6. ✅ 每日运势推送

### Phase 2: V1.0 (6个月)
7. ✅ 紫微斗数、星座
8. ✅ Qwen微调模型部署
9. ✅ RAG知识库
10. ✅ 专项咨询
11. ✅ 社区功能

### Phase 3: V2.0 (12个月)
12. ✅ 手相面相识别
13. ✅ 完全自研AI模型
14. ✅ 实时语音对话
15. ✅ 国际化(英日文)

---

**文档版本**: v1.0  
**最后更新**: 2026-03-19  
**维护者**: 开发团队
