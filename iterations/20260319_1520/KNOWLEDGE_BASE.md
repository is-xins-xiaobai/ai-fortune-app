# 知识库构建方案

**版本**: v0.2  
**更新时间**: 2026-03-19 15:20 JST  
**目标**: 回答"知识库如何构建？"

---

## 📚 知识库是什么？

**定义**: AI算命的"大脑"，存储所有命理知识

**包含内容**:
- 命理理论 (八字、星座、塔罗的基础知识)
- 案例分析 (真实算命案例及解读)
- 解读模板 (如何回答不同类型的问题)

**为什么重要？**
- 没有知识库，LLM会瞎编 (幻觉)
- 知识库保证回答有依据
- 知识库可以持续更新优化

---

## 🗂️ 知识库结构

### 总体架构

```
Knowledge Base/
├── 01_fortune_systems/          # 命理体系
│   ├── bazi/                     # 八字
│   ├── zodiac/                   # 星座
│   └── tarot/                    # 塔罗
│
├── 02_theory/                   # 理论知识
│   ├── basic_concepts/           # 基础概念
│   ├── advanced_techniques/      # 进阶技法
│   └── interpretation_methods/   # 解读方法
│
├── 03_cases/                    # 案例库
│   ├── career/                   # 事业案例
│   ├── relationship/             # 感情案例
│   ├── wealth/                   # 财运案例
│   └── health/                   # 健康案例
│
├── 04_templates/                # 解读模板
│   ├── daily_fortune/            # 每日运势
│   ├── yearly_report/            # 年度报告
│   └── specific_questions/       # 专项问题
│
└── 05_user_feedback/            # 用户反馈 (持续优化)
    ├── good_responses/           # 好评回答
    └── bad_responses/            # 差评回答
```

---

## 📖 详细内容

### 1. 命理体系 (fortune_systems/)

#### 八字 (bazi/)

```
bazi/
├── 01_ganzhi/                    # 干支
│   ├── tiangan_meanings.md       # 天干含义 (甲乙丙丁...)
│   ├── dizhi_meanings.md         # 地支含义 (子丑寅卯...)
│   └── ganzhi_combinations.md    # 干支组合
│
├── 02_wuxing/                    # 五行
│   ├── wuxing_basics.md          # 五行基础
│   ├── wuxing_shengke.md         # 五行生克
│   └── wuxing_in_bazi.md         # 八字中的五行
│
├── 03_ten_gods/                  # 十神
│   ├── ten_gods_overview.md      # 十神总论
│   ├── zhengyin.md               # 正印
│   ├── pianyin.md                # 偏印
│   ├── shangguan.md              # 伤官
│   └── ... (其他十神)
│
├── 04_patterns/                  # 格局
│   ├── patterns_overview.md      # 格局总论
│   ├── congge.md                 # 从格
│   ├── shengcheng.md             # 身强格
│   └── ruoruo.md                 # 身弱格
│
└── 05_da_yun/                    # 大运
    ├── dayun_calculation.md      # 大运计算
    ├── liunian_analysis.md       # 流年分析
    └── shishen_in_dayun.md       # 大运十神
```

**示例文件: tiangan_meanings.md**

```markdown
# 天干含义

## 甲木
- 性质: 阳木，参天大树
- 性格: 正直、向上、有领导力
- 优点: 有担当、有远见
- 缺点: 固执、不够灵活
- 适合职业: 管理者、创业者、领导者

## 乙木
- 性质: 阴木，花草藤蔓
- 性格: 柔韧、细腻、善于协调
- 优点: 适应力强、人际关系好
- 缺点: 优柔寡断、容易受外界影响
- 适合职业: 设计师、顾问、协调者

## 丙火
- 性质: 阳火，太阳
- 性格: 热情、开朗、有感染力
- 优点: 乐观、积极、有号召力
- 缺点: 急躁、容易冲动
- 适合职业: 销售、演艺、公关

... (其他天干)
```

---

### 2. 理论知识 (theory/)

```
theory/
├── basic_concepts/
│   ├── what_is_fortune_telling.md    # 什么是算命
│   ├── fortune_vs_psychology.md      # 算命vs心理学
│   └── how_to_read_bazi.md           # 如何看八字
│
├── advanced_techniques/
│   ├── special_patterns.md           # 特殊格局
│   ├── combinations.md               # 合冲刑害
│   └── void_branches.md              # 空亡
│
└── interpretation_methods/
    ├── holistic_approach.md          # 整体论命法
    ├── palace_analysis.md            # 宫位分析法
    └── element_balance.md            # 五行平衡法
```

---

### 3. 案例库 (cases/) ⭐ 核心

**结构**:

```
cases/
├── career/
│   ├── case_001.md
│   ├── case_002.md
│   └── ...
│
└── relationship/
    ├── case_001.md
    ├── case_002.md
    └── ...
```

**案例格式**:

```markdown
# 案例 001: 事业迷茫期

## 背景信息
- 性别: 男
- 出生: 1990年5月15日 8:00
- 问题: "工作3年了，感觉没有发展，想辞职创业，但不确定"

## 八字排盘
- 年柱: 庚午
- 月柱: 辛巳
- 日柱: 癸亥
- 时柱: 丙辰

## 命理分析
### 五行分析
- 金: 2 (年干庚金、月干辛金)
- 木: 0 (缺木)
- 水: 2 (日干癸水、日支亥水)
- 火: 2 (年支午火、月支巳火)
- 土: 2 (时支辰土、隐含)

### 格局判断
- 日主: 癸水
- 格局: 从弱格 (日主偏弱，顺从火土金)
- 用神: 火、土

### 大运流年
- 当前大运: 辛丑 (28-37岁)
- 流年: 甲辰 (2024年)
- 分析: 事业宫受冲，变动之象

## 解读要点
1. **现状分析**: 正处于事业变动期，想换工作是正常的
2. **优势**: 命格显示有创业潜质 (伤官透，有创新思维)
3. **风险**: 目前大运不算最佳时机，建议先做好准备
4. **建议**: 
   - 可以先兼职尝试，不要贸然全职
   - 选择与自己五行相合的行业 (火、土相关)
   - 2025年(乙巳)时机更好

## 对话示例

**用户**: "我真的很想辞职，但又害怕"

**AI回复**:
"理解你的纠结。从你的命盘看，你确实进入了事业变动期，这种想改变的想法是正常的。

你的命格显示你有创业的潜质 (伤官透，思维活跃)，但今年(甲辰年)事业宫受冲，变动中容易有波折。

我的建议是: 可以先利用业余时间尝试，验证想法，等明年(乙巳年)时机更成熟再全职投入。你觉得这个节奏怎么样？"

## 标签
- 主题: 事业、创业
- 格局: 从弱格
- 大运: 辛丑
- 关键词: 辞职、创业、迷茫
```

---

### 4. 解读模板 (templates/)

```
templates/
├── daily_fortune/
│   ├── morning_greeting.md         # 早安问候
│   ├── daily_analysis.md           # 今日运势分析
│   └── lucky_elements.md           # 今日宜忌
│
├── yearly_report/
│   ├── report_structure.md         # 报告结构
│   ├── career_section.md           # 事业部分模板
│   ├── love_section.md             # 感情部分模板
│   └── wealth_section.md           # 财运部分模板
│
└── specific_questions/
    ├── should_i_change_job.md      # 跳槽咨询
    ├── is_this_person_right.md     # 感情咨询
    └── when_to_invest.md           # 投资咨询
```

**模板示例: should_i_change_job.md**

```markdown
# 跳槽咨询回复模板

## 分析框架
1. **现状确认**: 了解当前工作的不满之处
2. **命盘分析**: 看当前大运流年是否适合变动
3. **优势识别**: 用户的优势和适合的方向
4. **风险评估**: 变动的风险和应对
5. **时机建议**: 最佳行动时间
6. **行动建议**: 具体可执行的步骤

## 回复结构

### 开场 (共情)
"听起来你在现在的岗位上遇到了一些困扰。能具体说说是什么让你想离开吗？"

### 分析 (结合命盘)
"从你的命盘看，[根据八字分析]..."

### 建议 (温和引导)
"基于以上分析，我的建议是..."
"当然，这只是参考，最终决定权在你。你觉得呢？"

### 追问 (引导深入)
"如果有一个机会可以让你[理想状态]，你觉得需要满足什么条件？"

## 注意事项
- ✅ 不给绝对答案 ("应该" / "不应该")
- ✅ 强调用户的选择权
- ✅ 提供具体的行动框架
- ❌ 不恐吓用户 ("不换工作会倒霉")
- ❌ 不承诺具体结果 ("换了就会好")
```

---

## 🔧 知识库构建流程

### 阶段1: 数据收集 (1-2周)

**来源**:
1. **经典书籍** (数字化)
   - 《易经》
   - 《滴天髓》
   - 《三命通会》
   - 《紫微斗数全书》
   
2. **现代书籍**
   - 命理入门书籍
   - 案例分析书籍
   
3. **网络资源**
   - 命理博客 (授权后使用)
   - 论坛精华帖
   
4. **专家输入**
   - 聘请命理师撰写案例
   - 录制解读视频转文字

**注意事项**:
- ⚠️ 版权问题 (使用公开领域内容或获得授权)
- ⚠️ 质量筛选 (去重、去错)

---

### 阶段2: 数据处理 (1周)

**步骤**:

1. **清洗**
   - 去除HTML标签
   - 统一编码 (UTF-8)
   - 修正错别字

2. **分割** (Chunking)
   ```python
   from langchain.text_splitter import RecursiveCharacterTextSplitter
   
   splitter = RecursiveCharacterTextSplitter(
       chunk_size=500,      # 每个chunk 500字
       chunk_overlap=50     # 重叠50字，保持上下文
   )
   
   chunks = splitter.split_text(text)
   ```

3. **标注元数据**
   ```python
   for chunk in chunks:
       chunk.metadata = {
           "fortune_type": "bazi",        # 八字
           "category": "theory",          # 理论
           "topic": "wuxing",             # 主题: 五行
           "difficulty": "beginner",      # 难度: 初级
           "source": "滴天髓",             # 来源
           "author": "任铁樵",             # 作者
           "verified": True               # 是否验证
       }
   ```

---

### 阶段3: 向量化入库 (3-5天)

**流程**:

```python
# 1. 加载embedding模型
from langchain.embeddings import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings(
    model_name="BAAI/bge-large-zh-v1.5"
)

# 2. 初始化向量数据库
from langchain.vectorstores import Chroma

vectorstore = Chroma(
    persist_directory="./chroma_db",
    embedding_function=embeddings
)

# 3. 入库
vectorstore.add_texts(
    texts=[chunk.page_content for chunk in chunks],
    metadatas=[chunk.metadata for chunk in chunks]
)

# 4. 持久化
vectorstore.persist()
```

**时间估算**:
- 1000条知识: 10-20分钟
- 10000条知识: 1-2小时

---

### 阶段4: 测试验证 (持续)

**方法**:

1. **检索测试**
   ```python
   # 测试检索质量
   query = "五行缺木的人性格怎么样？"
   results = vectorstore.similarity_search(query, k=3)
   
   # 人工检查:
   # - 返回的结果是否相关？
   # - 排名是否合理？
   ```

2. **生成测试**
   ```python
   # 测试AI回答质量
   # 用RAG生成的回答 vs 不用RAG的回答
   # 人工评分
   ```

3. **用户反馈**
   - 收集用户对AI回答的反馈
   - 好评回答 → 标记为优质案例
   - 差评回答 → 分析原因，补充知识

---

## 💰 成本估算

### 构建成本 (一次性)

| 项目 | 成本 | 说明 |
|------|------|------|
| 数据收集 | ¥0-5000 | 自己整理免费，外包需要费用 |
| 数据处理 | ¥0 | 自己写脚本 |
| 向量化 | ¥0 | 开源模型，本地运行 |
| **总计** | **¥0-5000** | 主要是人工成本 |

### 维护成本 (每月)

| 项目 | 成本 | 说明 |
|------|------|------|
| 向量数据库 | ¥0-500 | ChromaDB本地免费，云端付费 |
| 知识更新 | ¥0 | 自己维护 |
| **总计** | **¥0-500** | 主要是时间成本 |

---

## 📊 知识库规模建议

### MVP阶段 (上线前)

| 类别 | 数量 | 优先级 |
|------|------|--------|
| 基础理论 | 500条 | ⭐⭐⭐⭐⭐ |
| 案例分析 | 500条 | ⭐⭐⭐⭐⭐ |
| 解读模板 | 50个 | ⭐⭐⭐⭐ |
| **总计** | **1050条** | |

**时间**: 1-2周可以完成

---

### 运营阶段 (持续扩充)

| 类别 | 目标 | 频率 |
|------|------|------|
| 案例分析 | 5000条 | 每周100条 |
| 用户好评 | 1000条 | 持续收集 |
| **总计** | **6000+条** | |

---

## 🎯 MVP知识库任务清单

### Week 1: 数据收集
- [ ] 整理八字基础理论 (100条)
- [ ] 收集事业案例 (50条)
- [ ] 收集感情案例 (50条)
- [ ] 编写解读模板 (10个)

### Week 2: 处理入库
- [ ] 清洗数据
- [ ] 分割chunk
- [ ] 标注元数据
- [ ] 向量化入库

### Week 3: 测试优化
- [ ] 检索测试 (20个query)
- [ ] 生成测试 (对比实验)
- [ ] 根据测试结果优化

---

## ✅ 总结

**知识库是AI算命的"大脑"**，构建好知识库就成功了一半。

**关键要点**:
1. **结构化** - 清晰的分类体系
2. **高质量** - 精选内容，去重去错
3. **可持续** - 持续更新，用户反馈驱动
4. **可验证** - 测试检索和生成质量

**下一步**:
1. 开始收集数据 (先从基础理论入手)
2. 搭建RAG流程
3. 测试验证效果

---

**知识库构建完成，AI就有"脑子"了！** 🧠📚
