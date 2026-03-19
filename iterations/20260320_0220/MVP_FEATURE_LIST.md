# MVP功能清单

**版本**: v0.3  
**更新时间**: 2026-03-19 21:20 JST  
**目标**: 2人兼职团队，4-6周完成

---

## 🎯 MVP原则

1. **只做八字大师** - 不扩展其他命理系统
2. **核心功能优先** - 对话 + 排盘 + 付费
3. **前端外包** - 自己专注后端和AI逻辑
4. **快速验证** - 先跑起来，再优化

---

## 📋 功能清单

### P0 - 必须有 (MVP核心)

#### 1. 用户系统

| 功能 | 描述 | 优先级 | 开发方式 |
|------|------|--------|---------|
| 手机号注册 | 短信验证码 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 用户登录 | JWT token | ⭐⭐⭐⭐⭐ | 自己开发 |
| 密码重置 | 短信验证码 | ⭐⭐⭐⭐ | 自己开发 |

**开发时间**: 2-3天

---

#### 2. 八字排盘

| 功能 | 描述 | 优先级 | 开发方式 |
|------|------|--------|---------|
| 出生信息录入 | 日期、时间、地点 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 八字计算 | 四柱、五行、十神 | ⭐⭐⭐⭐⭐ | 使用 lunar-python 库 |
| 大运计算 | 起运时间、大运干支 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 流年计算 | 当前流年分析 | ⭐⭐⭐⭐ | 自己开发 |
| 命盘展示 | 可视化展示 | ⭐⭐⭐⭐ | 前端展示 |

**开发时间**: 3-4天

**技术方案**:
```python
# 使用 lunar-python 库
from lunar_python import Lunar, Solar

class BaziCalculator:
    def calculate(self, birth_datetime):
        solar = Solar.fromDate(birth_datetime)
        lunar = Lunar.fromSolar(solar)
        
        return {
            "year": {"gan": lunar.getYearGan(), "zhi": lunar.getYearZhi()},
            "month": {"gan": lunar.getMonthGan(), "zhi": lunar.getMonthZhi()},
            "day": {"gan": lunar.getDayGan(), "zhi": lunar.getDayZhi()},
            "hour": {"gan": self.getHourGan(), "zhi": lunar.getTimeZhi()}
        }
```

---

#### 3. AI对话 (核心)

| 功能 | 描述 | 优先级 | 开发方式 |
|------|------|--------|---------|
| 多轮对话 | 支持上下文 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 情绪识别 | 识别用户情绪 | ⭐⭐⭐⭐⭐ | GPT-4 + Prompt |
| RAG检索 | 检索命理知识 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 回复生成 | 温暖专业的回复 | ⭐⭐⭐⭐⭐ | GPT-4 |
| 对话历史 | 保存聊天记录 | ⭐⭐⭐⭐ | 自己开发 |

**开发时间**: 5-7天

**技术方案**:
```python
# RAG + LLM 流程
class AIDialogueService:
    def chat(self, user_message, user_id):
        # 1. 获取用户画像
        user_profile = self.get_user_profile(user_id)
        
        # 2. 检索知识
        knowledge = self.kb.query(
            query=user_message,
            fortune_type="bazi"
        )
        
        # 3. 计算命盘 (如果需要)
        bazi_chart = self.bazi_calculator.calculate(
            user_profile["birth_datetime"]
        )
        
        # 4. 构建Prompt
        prompt = self.build_prompt(
            user_message=user_message,
            user_profile=user_profile,
            knowledge=knowledge,
            bazi_chart=bazi_chart
        )
        
        # 5. 调用GPT-4
        response = self.llm.generate(prompt)
        
        # 6. 保存对话
        self.save_conversation(user_id, user_message, response)
        
        return response
```

---

#### 4. 知识库

| 功能 | 描述 | 优先级 | 开发方式 |
|------|------|--------|---------|
| 知识存储 | ChromaDB向量存储 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 语义检索 | 相似度搜索 | ⭐⭐⭐⭐⭐ | 使用 BGE embedding |
| 知识导入 | 批量导入命理知识 | ⭐⭐⭐⭐ | 脚本实现 |
| 知识更新 | 增量更新 | ⭐⭐⭐ | 后续迭代 |

**开发时间**: 3-4天

**技术方案**:
```python
from langchain.vectorstores import Chroma
from langchain.embeddings import HuggingFaceEmbeddings

class KnowledgeBase:
    def __init__(self):
        self.embeddings = HuggingFaceEmbeddings(
            model_name="BAAI/bge-large-zh-v1.5"
        )
        self.vectorstore = Chroma(
            persist_directory="./chroma_db",
            embedding_function=self.embeddings
        )
    
    def query(self, query, k=3):
        return self.vectorstore.similarity_search(query, k=k)
```

---

#### 5. 付费系统

| 功能 | 描述 | 优先级 | 开发方式 |
|------|------|--------|---------|
| 免费额度 | 每天3次对话 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 付费墙 | 超出后引导付费 | ⭐⭐⭐⭐⭐ | 自己开发 |
| 支付宝支付 | 接入支付宝 | ⭐⭐⭐⭐⭐ | 使用SDK |
| 微信支付 | 接入微信支付 | ⭐⭐⭐⭐ | 使用SDK |
| 订阅管理 | 月度/年度订阅 | ⭐⭐⭐⭐ | 自己开发 |

**开发时间**: 3-4天

---

### P1 - 最好有 (如果有时间)

| 功能 | 描述 | 优先级 | 开发方式 |
|------|------|--------|---------|
| 每日运势 | 每天推送运势 | ⭐⭐⭐ | 自己开发 |
| 对话分享 | 分享到朋友圈 | ⭐⭐⭐ | 前端实现 |
| 命盘导出 | PDF导出 | ⭐⭐ | 后续迭代 |
| 意见反馈 | 用户反馈入口 | ⭐⭐⭐ | 简单实现 |

---

### P2 - 暂不做 (V1.0再做)

- ❌ 星座、塔罗 (其他命理系统)
- ❌ 社区功能 (发帖、评论)
- ❌ 真人命理师 (视频咨询)
- ❌ 复杂UI动画
- ❌ 语音对话
- ❌ 手相识别

---

## 🛠️ 技术栈

### 后端

| 组件 | 选择 | 原因 |
|------|------|------|
| 框架 | FastAPI | 异步高性能，AI生态好 |
| 数据库 | PostgreSQL | 关系型数据存储 |
| 向量DB | ChromaDB | 开源，易用 |
| 缓存 | Redis | 会话存储 |
| LLM | GPT-4 API | 效果好，快速上线 |
| Embedding | BGE-large-zh | 中文效果好，开源 |

### 前端 (外包)

| 组件 | 选择 | 原因 |
|------|------|------|
| 框架 | React | 生态成熟，外包好找 |
| UI库 | Ant Design | 现成组件，开发快 |
| 状态管理 | Redux | 标准方案 |

---

## ⏰ 开发时间估算

### 后端开发 (自己)

| 模块 | 时间 | 负责人 |
|------|------|--------|
| 用户系统 | 2-3天 | 成员A |
| 八字计算 | 3-4天 | 成员A |
| 知识库 | 3-4天 | 成员B |
| AI对话 | 5-7天 | 成员B |
| 付费系统 | 3-4天 | 成员A |
| API开发 | 2-3天 | 成员A |
| **总计** | **18-25天** | |

### 前端开发 (外包)

| 模块 | 时间 | 费用 |
|------|------|------|
| UI设计 | 3-5天 | ¥3000-5000 |
| 页面开发 | 7-10天 | ¥8000-12000 |
| **总计** | **10-15天** | **¥11000-17000** |

### 整体时间线

| 周次 | 后端 | 前端 | 并行任务 |
|------|------|------|---------|
| Week 1 | 用户系统 + 八字计算 | UI设计 | 知识库搭建 |
| Week 2 | AI对话核心 | 页面开发 | 知识导入 |
| Week 3 | 付费系统 + API | 页面开发 | 联调测试 |
| Week 4 | 测试优化 | 测试优化 | Bug修复 |
| Week 5-6 | 缓冲时间 | - | 上线准备 |

**总计**: 4-6周 (兼职状态)

---

## 💰 成本估算

### 开发成本

| 项目 | 成本 | 说明 |
|------|------|------|
| 前端外包 | ¥11000-17000 | UI设计 + 开发 |
| 后端开发 | ¥0 | 自己开发 |
| 服务器 | ¥500/月 | AWS/阿里云 |
| GPT-4 API | ¥1000/月 | 开发期 |
| **总计** | **¥12500-18500** | 一次性 + 2个月运营 |

### 运营成本 (每月)

| 项目 | 成本 | 说明 |
|------|------|------|
| 服务器 | ¥500 | t3.medium |
| 数据库 | ¥200 | RDS |
| GPT-4 API | ¥1000-2000 | 按使用量 |
| 支付手续费 | ¥0.6% | 支付宝/微信 |
| **总计** | **¥1700-2700/月** | |

---

## ✅ 总结

### MVP核心
- ✅ 只做八字大师
- ✅ 5个核心功能 (用户、排盘、对话、知识库、付费)
- ✅ 前端外包，后端自己开发
- ✅ 4-6周完成

### 关键决策
1. **前端外包** - 节省时间，专注核心
2. **只用八字** - 验证假设后再扩展
3. **GPT-4 API** - 快速上线，效果好

### 下一步
1. 找外包设计师和前端
2. 启动后端开发
3. 搭建知识库

---

**MVP功能清单完成，可以开始开发了！** 🚀
