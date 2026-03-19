# 技术架构详解

**版本**: v0.2  
**更新时间**: 2026-03-19 15:20 JST  
**目标**: 回答"AI算命的实现逻辑是什么？"

---

## 🏗️ 整体架构

### 三层分离架构

```
┌─────────────────────────────────────────────────────────────────┐
│                        用户交互层                                │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │   Web App   │  │  Mobile App │  │   WeChat    │             │
│  │   (React)   │  │  (Flutter)  │  │   MiniApp   │             │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘             │
└─────────┼────────────────┼────────────────┼────────────────────┘
          │                │                │
          └────────────────┴────────────────┘
                           │
          ┌────────────────▼────────────────┐
          │        API Gateway              │
          │     (Nginx / Kong)              │
          └────────────────┬────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│                        业务逻辑层                                │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  对话层 (LLM Service)                     │   │
│  │  ┌─────────────────────────────────────────────────────┐ │   │
│  │  │  GPT-4 / Claude 3.5                                │ │   │
│  │  │  - 负责温暖的对话表达                               │ │   │
│  │  │  - 结合知识库生成回答                               │ │   │
│  │  │  - Persona管理 (不同大师风格)                        │ │   │
│  │  └─────────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  知识层 (RAG Service)                     │   │
│  │  ┌─────────────────────────────────────────────────────┐ │   │
│  │  │  ChromaDB (向量数据库)                              │ │   │
│  │  │  - 命理知识向量化存储                               │ │   │
│  │  │  - 语义检索 (相似度搜索)                             │ │   │
│  │  │  - 知识片段召回                                     │ │   │
│  │  └─────────────────────────────────────────────────────┘ │   │
│  │                                                                  │
│  │  ┌─────────────────────────────────────────────────────┐ │   │
│  │  │  Embedding Model (BGE-large-zh)                     │ │   │
│  │  │  - 将文本转为向量                                   │ │   │
│  │  │  - 支持中文语义理解                                 │ │   │
│  │  └─────────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                 计算层 (Fortune Engine)                   │   │
│  │  ┌─────────────────────────────────────────────────────┐ │   │
│  │  │  八字计算引擎 (Python)                              │ │   │
│  │  │  - 干支历转换                                       │ │   │
│  │  │  - 五行强弱计算                                     │ │   │
│  │  │  - 大运流年推算                                     │ │   │
│  │  └─────────────────────────────────────────────────────┘ │   │
│  │                                                                  │
│  │  ┌─────────────────────────────────────────────────────┐ │   │
│  │  │  星座计算引擎 (Python)                              │ │   │
│  │  │  - 星历表计算 (Swiss Ephemeris)                     │ │   │
│  │  │  - 宫位系统                                         │ │   │
│  │  │  - 相位分析                                         │ │   │
│  │  └─────────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│                         数据层                                   │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │  PostgreSQL │  │  ChromaDB   │  │    Redis    │             │
│  │  (用户数据)  │  │ (知识向量)   │  │   (缓存)     │             │
│  └─────────────┘  └─────────────┘  └─────────────┘             │
└──────────────────────────────────────────────────────────────────┘
```

---

## 🔧 各层详细说明

### 第一层：对话层 (LLM Service)

**职责**: 负责温暖的对话表达，不是"算命"，而是"陪伴"

**为什么用LLM？**
- ✅ 自然语言理解能力强
- ✅ 可以生成温暖、共情的回复
- ✅ 支持多轮对话
- ❌ 不需要LLM"学会算命"（那是知识层和计算层的事）

**Prompt Engineering策略**:

```python
# system_prompt.py

BAZI_MASTER_PERSONA = """
你是一位精通八字命理的AI大师，名叫"小天"。

你的风格:
- 温暖、有耐心，像一位智慧的朋友
- 说话简洁，每次回复不超过3段
- 先用共情回应用户的情绪，再给出分析
- 用命理语言，但不迷信，强调"参考"而非"注定"

你的知识来源:
- 你会检索知识库获取命理依据
- 你会参考用户的命盘计算结果
- 你会记住之前的对话历史

你的边界:
- 不做医疗诊断
- 不做投资建议
- 遇到严重心理问题，建议寻求专业帮助

回复格式:
1. 共情回应 (1句话)
2. 命理分析 (结合知识库，2-3句话)
3. 开放性问题 (引导用户思考，1句话)
"""
```

**示例对话流程**:

```
用户: "我最近工作很不顺心，想辞职又不敢"

↓

[对话层] 理解用户情绪 (焦虑、迷茫)
         ↓ 调用知识层检索相关知识

[知识层] 检索: "工作不顺的八字解读"
         返回: "官杀混杂，事业波动期..."
         ↓ 调用计算层获取用户命盘

[计算层] 计算用户八字
         返回: "当前大运: 辛丑，流年: 甲辰，事业宫受冲"
         ↓

[对话层] 生成回复:
"听起来你最近压力很大 🫂

从你的命盘看，今年确实进入了事业变动期(官杀混杂)，
这种纠结是正常的。但你的命格也显示，你有能力应对这个挑战。

不如我们一起分析一下: 是什么具体的事情让你想辞职？"
```

---

### 第二层：知识层 (RAG Service)

**职责**: 存储和检索命理知识

**为什么用RAG？**
- ✅ 知识可更新（不用重新训练模型）
- ✅ 成本低（比微调模型便宜10倍）
- ✅ 可追溯（知道AI回答的依据）
- ✅ 避免幻觉（有知识库支撑）

**知识库结构**:

```
chroma_db/
├── collections/
│   ├── bazi_theory/           # 八字理论
│   │   ├── 天干地支含义
│   │   ├── 十神详解
│   │   ├── 五行生克
│   │   └── 格局判断
│   │
│   ├── bazi_cases/            # 八字案例
│   │   ├── 事业案例 (1000条)
│   │   ├── 感情案例 (1000条)
│   │   ├── 财运案例 (500条)
│   │   └── 健康案例 (500条)
│   │
│   ├── bazi_templates/        # 解读模板
│   │   ├── 事业运势模板
│   │   ├── 感情运势模板
│   │   └── 流年运势模板
│   │
│   ├── astro_theory/          # 星座理论
│   ├── astro_cases/           # 星座案例
│   ├── tarot_meanings/        # 塔罗牌义
│   └── tarot_spreads/         # 塔罗牌阵
│
└── metadata/                  # 元数据
    ├── source_info.json       # 知识来源
    └── version_info.json      # 版本信息
```

**RAG实现代码**:

```python
# rag_service.py

from langchain.embeddings import HuggingFaceEmbeddings
from langchain.vectorstores import Chroma

class FortuneKnowledgeBase:
    def __init__(self):
        # 使用中文embedding模型
        self.embeddings = HuggingFaceEmbeddings(
            model_name="BAAI/bge-large-zh-v1.5"
        )
        
        # 初始化ChromaDB
        self.vectorstore = Chroma(
            persist_directory="./chroma_db",
            embedding_function=self.embeddings
        )
    
    def query(self, question: str, fortune_type: str, k: int = 3) -> list:
        """
        检索相关知识
        
        Args:
            question: 用户问题
            fortune_type: 命理类型 (bazi/astro/tarot)
            k: 返回top-k个结果
        
        Returns:
            相关知识片段列表
        """
        # 根据命理类型过滤
        filter_dict = {"fortune_type": fortune_type}
        
        # 语义检索
        docs = self.vectorstore.similarity_search(
            query=question,
            k=k,
            filter=filter_dict
        )
        
        return [doc.page_content for doc in docs]
    
    def add_knowledge(self, texts: list, metadata: list):
        """添加新知识"""
        self.vectorstore.add_texts(texts, metadatas=metadata)
        self.vectorstore.persist()
```

**知识入库流程**:

```python
# knowledge_ingestion.py

def ingest_fortune_books():
    """将命理书籍导入知识库"""
    
    # 1. 读取文本
    loader = DirectoryLoader(
        "./data/fortune_books/",
        glob="**/*.txt"
    )
    documents = loader.load()
    
    # 2. 分割文本 (chunk)
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=500,      # 每个chunk 500字
        chunk_overlap=50     # 重叠50字，保持上下文
    )
    chunks = text_splitter.split_documents(documents)
    
    # 3. 添加元数据
    for chunk in chunks:
        chunk.metadata.update({
            "fortune_type": "bazi",      # 八字
            "category": "theory",         # 理论
            "source": "滴天髓",           # 来源
            "difficulty": "advanced"      # 难度
        })
    
    # 4. 存入向量数据库
    kb = FortuneKnowledgeBase()
    kb.vectorstore.add_documents(chunks)
    kb.vectorstore.persist()
```

---

### 第三层：计算层 (Fortune Engine)

**职责**: 精确的命理计算，保证准确性

**为什么不用LLM计算？**
- ❌ LLM不擅长精确计算
- ❌ 干支历转换有固定规则
- ❌ 需要保证100%准确
- ✅ 规则引擎更适合

**八字计算引擎**:

```python
# bazi_engine.py

from lunar_python import Lunar, Solar
from datetime import datetime

class BaziCalculator:
    """八字计算引擎"""
    
    # 天干
    TIANGAN = ["甲", "乙", "丙", "丁", "戊", "己", "庚", "辛", "壬", "癸"]
    
    # 地支
    DIZHI = ["子", "丑", "寅", "卯", "辰", "巳", "午", "未", "申", "酉", "戌", "亥"]
    
    # 五行属性
    WUXING = {
        "甲": "木", "乙": "木",
        "丙": "火", "丁": "火",
        "戊": "土", "己": "土",
        "庚": "金", "辛": "金",
        "壬": "水", "癸": "水"
    }
    
    def __init__(self, birth_datetime: datetime, gender: str):
        self.birth_datetime = birth_datetime
        self.gender = gender
        
        # 转换为农历
        solar = Solar.fromDate(birth_datetime)
        self.lunar = Lunar.fromSolar(solar)
    
    def calculate_bazi(self) -> dict:
        """
        计算八字四柱
        
        Returns:
            {
                "year": {"gan": "庚", "zhi": "午", "wuxing": "金/火"},
                "month": {"gan": "辛", "zhi": "巳", "wuxing": "金/火"},
                "day": {"gan": "癸", "zhi": "亥", "wuxing": "水/水"},
                "hour": {"gan": "丙", "zhi": "辰", "wuxing": "火/土"}
            }
        """
        return {
            "year": {
                "gan": self.lunar.getYearGan(),
                "zhi": self.lunar.getYearZhi()
            },
            "month": {
                "gan": self.lunar.getMonthGan(),
                "zhi": self.lunar.getMonthZhi()
            },
            "day": {
                "gan": self.lunar.getDayGan(),
                "zhi": self.lunar.getDayZhi()
            },
            "hour": {
                "gan": self._get_hour_gan(),
                "zhi": self.lunar.getTimeZhi()
            }
        }
    
    def analyze_wuxing(self) -> dict:
        """
        五行强弱分析
        
        Returns:
            {
                "counts": {"金": 2, "木": 0, "水": 2, "火": 3, "土": 1},
                "strong": "火",
                "weak": "木",
                "missing": ["木"]
            }
        """
        bazi = self.calculate_bazi()
        
        # 统计五行
        counts = {"金": 0, "木": 0, "水": 0, "火": 0, "土": 0}
        
        for pillar in bazi.values():
            gan_wx = self.WUXING.get(pillar["gan"], "")
            if gan_wx:
                counts[gan_wx] += 1
            # 地支五行 (简化版，实际更复杂)
            zhi_wx = self._get_zhi_wuxing(pillar["zhi"])
            counts[zhi_wx] += 1
        
        return {
            "counts": counts,
            "strong": max(counts, key=counts.get),
            "weak": min(counts, key=counts.get),
            "missing": [k for k, v in counts.items() if v == 0]
        }
    
    def _get_hour_gan(self) -> str:
        """根据日干和时支推算时干 (日上起时法)"""
        day_gan = self.lunar.getDayGan()
        hour_zhi = self.lunar.getTimeZhi()
        # 五鼠遁口诀实现
        # ...
        pass
    
    def _get_zhi_wuxing(self, zhi: str) -> str:
        """获取地支五行"""
        zhi_wuxing = {
            "子": "水", "丑": "土", "寅": "木", "卯": "木",
            "辰": "土", "巳": "火", "午": "火", "未": "土",
            "申": "金", "酉": "金", "戌": "土", "亥": "水"
        }
        return zhi_wuxing.get(zhi, "")
```

---

## 🔄 完整数据流程

```
用户提问: "我最近工作不顺，该怎么办？"
    ↓
[1. 理解意图]
对话层 (LLM) 分析用户情绪: 焦虑、迷茫
    ↓
[2. 检索知识]
知识层 (RAG) 检索: "工作不顺 八字解读"
返回: ["官杀混杂，事业波动...", "食神生财，适合创意工作..."]
    ↓
[3. 计算命盘]
计算层 (引擎) 计算用户当前大运、流年
返回: {"current_dayun": "辛丑", "liunian": "甲辰", "career_palace": "受冲"}
    ↓
[4. 生成回复]
对话层 (LLM) 结合知识+命盘+情绪，生成温暖回复
    ↓
返回给用户: "听起来你最近压力很大..."
```

---

## 💰 成本估算

### MVP阶段 (每月)

| 组件 | 服务 | 成本 |
|------|------|------|
| LLM | GPT-4 API | ¥1000-2000 |
| 向量数据库 | ChromaDB (本地) | ¥0 |
| 计算引擎 | Python (本地) | ¥0 |
| 服务器 | AWS t3.medium | ¥300-500 |
| **总计** | | **¥1300-2500/月** |

### 规模化后

- 向量数据库: Pinecone / Zilliz (¥500-1000/月)
- LLM: 可能微调开源模型降低成本
- 服务器: 根据用户量扩展

---

## 🎯 MVP技术选型 (推荐)

### 后端
```
框架: FastAPI
数据库: PostgreSQL (用户数据) + ChromaDB (知识向量)
缓存: Redis
部署: Docker + AWS/阿里云
```

### AI
```
LLM: GPT-4 API (MVP阶段)
Embedding: BGE-large-zh (开源)
RAG: LangChain + ChromaDB
```

### 前端
```
MVP: React (网页版，快速开发)
V1.0: Flutter (跨平台App)
```

---

## 📚 下一步

1. 实现八字计算引擎
2. 构建知识库 (先导入1000条核心知识)
3. 设计Prompt Engineering
4. 搭建RAG流程
5. 集成测试

---

**技术架构已确定，可以开始开发！** 🔧
