# 多系统一致性设计方案

**版本**: v0.2  
**更新时间**: 2026-03-19 15:20 JST  
**目标**: 回答"如果构建不同算命系统的大师，如何保持它们之间的一致性？"

---

## 🤔 核心问题

### 矛盾场景示例

```
用户: 1990年5月15日早上8点出生

八字大师说: "你今年事业运势低迷，不宜变动"
星座大师说: "你今年木星入事业宫，是跳槽的好时机"
塔罗师抽牌: "命运之轮正位，适合迎接新变化"

→ 结论矛盾！用户该听谁的？
```

**问题本质**:
- 不同命理体系有不同的逻辑和语言
- 同一时间点，不同系统可能给出相反结论
- 用户会困惑，甚至失去信任

---

## 💡 核心解决方案

### 总体思路: 统一底层 + 多元表达

```
┌─────────────────────────────────────────────┐
│              统一用户画像层                   │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐       │
│  │ 性格特征 │ │ 运势趋势 │ │ 人生阶段 │       │
│  └────┬────┘ └────┬────┘ └────┬────┘       │
└───────┼───────────┼───────────┼─────────────┘
        │           │           │
        └───────────┴───────────┘
                    │
        ┌───────────▼───────────┐
        │    多视角表达层        │
┌───────┴───────────────────────┴───────┐
│                                       │
│  八字视角: "官杀混杂，事业波动期..."     │
│  星座视角: "木星逆行，事业需调整..."     │
│  塔罗视角: "高塔牌出现，旧模式将打破..." │
│                                       │
└───────────────────────────────────────┘
```

**关键原则**: 
- 底层性格和运势趋势是一致的
- 不同系统只是用不同的语言描述同一个事实
- 避免结论矛盾，允许表达方式不同

---

## 🏗️ 三层架构

### 第一层: 统一用户画像 (Unified Profile)

**存储用户的核心特征**:

```json
{
  "user_id": "uuid",
  "basic_info": {
    "birth_datetime": "1990-05-15T08:00:00",
    "gender": "男",
    "birth_location": "北京"
  },
  
  "core_traits": {
    "personality": {
      "introversion": 0.7,        // 内向程度 0-1
      "rationality": 0.8,         // 理性程度
      "sensitivity": 0.6,         // 敏感度
      "creativity": 0.5,          // 创造力
      "leadership": 0.4           // 领导力
    },
    "strengths": ["逻辑思维", "细心", "有耐心"],
    "weaknesses": ["过于谨慎", "不善表达", "容易焦虑"],
    "communication_style": "间接委婉"
  },
  
  "life_patterns": {
    "career_trend": "growth",      // growth/stable/challenge
    "relationship_trend": "stable",
    "wealth_trend": "fluctuating",
    "health_trend": "good",
    "current_phase": "transition"  // 当前人生阶段
  },
  
  "current_state": {
    "mood": "anxious",             // 当前情绪
    "stress_level": "high",        // 压力水平
    "major_concern": "career",     // 当前关注点
    "decision_pending": "job_change"
  },
  
  "historical_insights": {
    "consistent_themes": ["事业变动", "人际关系"],
    "recurring_patterns": ["年初焦虑", "年底反思"]
  }
}
```

**如何生成？**

1. **首次建档时**
   - 用户输入生辰信息
   - 多个系统分别计算
   - AI综合分析，提取共同特征
   - 存储到统一画像

2. **每次对话后更新**
   - AI分析用户的情绪、关注点
   - 更新current_state
   - 识别长期模式，更新historical_insights

---

### 第二层: 命理计算层 (Fortune Calculation)

**各系统独立计算，但结果要"翻译"成统一语言**

```python
class FortuneSystem:
    def calculate(self, user_profile: dict) -> dict:
        """
        计算命理结果，返回统一格式
        """
        pass

class BaziSystem(FortuneSystem):
    def calculate(self, user_profile: dict) -> dict:
        # 1. 八字排盘
        bazi_chart = self.calculate_bazi(
            user_profile["birth_datetime"]
        )
        
        # 2. 分析五行
        wuxing_analysis = self.analyze_wuxing(bazi_chart)
        
        # 3. 看大运流年
        dayun_analysis = self.analyze_dayun(bazi_chart)
        
        # 4. 转换为统一格式
        return {
            "system": "bazi",
            "fortune_type": "career",  # 事业
            "trend": "challenge",      # 趋势: 挑战期
            "timing": "not_now",       # 时机: 不是现在
            "strengths": ["逻辑思维", "细心"],
            "challenges": ["过于谨慎", "不善表达"],
            "advice": "稳步前行，不要贸然变动",
            "confidence": 0.8            # 置信度
        }

class AstroSystem(FortuneSystem):
    def calculate(self, user_profile: dict) -> dict:
        # 1. 计算星盘
        chart = self.calculate_chart(
            user_profile["birth_datetime"],
            user_profile["birth_location"]
        )
        
        # 2. 分析行星位置
        planet_analysis = self.analyze_planets(chart)
        
        # 3. 转换为统一格式
        return {
            "system": "astro",
            "fortune_type": "career",
            "trend": "challenge",      # 同样是挑战期
            "timing": "not_now",       # 时机未到
            "strengths": ["逻辑思维", "细心"],
            "challenges": ["过于谨慎", "不善表达"],
            "advice": "土星逆行期间，适合内省而非变动",
            "confidence": 0.75
        }
```

**统一输出格式**:

```json
{
  "system": "bazi|astro|tarot",
  "fortune_type": "career|relationship|wealth|health",
  "trend": "growth|stable|challenge|transition",
  "timing": "now|soon|not_now|wait",
  "strengths": ["str1", "str2"],
  "challenges": ["ch1", "ch2"],
  "advice": "general advice",
  "confidence": 0.0-1.0
}
```

---

### 第三层: 多视角表达层 (Multi-Perspective Expression)

**根据统一结果，用不同系统的语言风格表达**

```python
class MasterPersona:
    def generate_response(
        self, 
        unified_result: dict,
        user_profile: dict,
        conversation_history: list
    ) -> str:
        pass

class BaziMaster(MasterPersona):
    """八字大师 - 传统、稳重、有深度"""
    
    def generate_response(self, unified_result, user_profile, history):
        # 获取知识库支持
        knowledge = self.kb.query(
            unified_result["fortune_type"],
            system="bazi"
        )
        
        # 生成八字风格的回复
        prompt = f"""
        你是一位八字大师，名叫"天机老人"。
        
        用户情况:
        - 性格: {user_profile["core_traits"]["personality"]}
        - 当前运势: {unified_result["trend"]}
        - 建议: {unified_result["advice"]}
        
        知识依据:
        {knowledge}
        
        请用八字命理的语言风格，给出温暖、专业的建议。
        重点提及: 五行、十神、大运、流年。
        """
        
        return self.llm.generate(prompt)

class AstroMaster(MasterPersona):
    """星座师 - 现代、轻松、鼓励"""
    
    def generate_response(self, unified_result, user_profile, history):
        knowledge = self.kb.query(
            unified_result["fortune_type"],
            system="astro"
        )
        
        prompt = f"""
        你是一位星座师，名叫"星空"。
        
        用户情况:
        - 性格: {user_profile["core_traits"]["personality"]}
        - 当前运势: {unified_result["trend"]}
        - 建议: {unified_result["advice"]}
        
        知识依据:
        {knowledge}
        
        请用星座占星的语言风格，给出轻松、鼓励的建议。
        重点提及: 太阳星座、月亮星座、行星位置、宫位。
        """
        
        return self.llm.generate(prompt)

class TarotMaster(MasterPersona):
    """塔罗师 - 神秘、直觉、启发"""
    
    def generate_response(self, unified_result, user_profile, history):
        # 塔罗需要抽牌
        cards = self.draw_cards()
        
        knowledge = self.kb.query(
            unified_result["fortune_type"],
            system="tarot"
        )
        
        prompt = f"""
        你是一位塔罗师，名叫"月影"。
        
        用户抽到的牌: {cards}
        用户情况:
        - 性格: {user_profile["core_traits"]["personality"]}
        - 当前运势: {unified_result["trend"]}
        - 建议: {unified_result["advice"]}
        
        知识依据:
        {knowledge}
        
        请用塔罗牌的语言风格，给出神秘、有启发的建议。
        重点解读抽到的牌的含义。
        """
        
        return self.llm.generate(prompt)
```

---

## 🎯 一致性保证机制

### 1. 冲突检测

```python
def detect_conflict(results: list) -> bool:
    """
    检测不同系统之间是否有冲突
    """
    trends = [r["trend"] for r in results]
    timings = [r["timing"] for r in results]
    
    # 检测趋势冲突
    if "growth" in trends and "challenge" in trends:
        return True
    
    # 检测时机冲突
    if "now" in timings and "not_now" in timings:
        return True
    
    return False
```

### 2. 冲突解决策略

```python
def resolve_conflict(results: list, user_profile: dict) -> dict:
    """
    解决系统间的冲突
    """
    # 策略1: 按置信度加权
    weighted_result = weighted_average(results)
    
    # 策略2: 取交集 (找共同点)
    common_strengths = set.intersection(
        *[set(r["strengths"]) for r in results]
    )
    common_challenges = set.intersection(
        *[set(r["challenges"]) for r in results]
    )
    
    # 策略3: 模糊化表达
    # 不说"好"或"坏"，而说"有挑战也有机会"
    
    return {
        "trend": "mixed",  # 混合趋势
        "timing": "depends",  # 视情况而定
        "strengths": list(common_strengths),
        "challenges": list(common_challenges),
        "advice": "综合考虑各方面因素，建议你..."
    }
```

### 3. 统一话术模板

```python
UNIFIED_TEMPLATES = {
    "career_growth": {
        "bazi": "你的命格显示，当前正处于事业上升期(印星得用)...",
        "astro": "从你的星盘看，木星正在你的事业宫，带来扩张的机会...",
        "tarot": "你抽到了权杖三，象征着事业的拓展和成长..."
    },
    
    "career_challenge": {
        "bazi": "今年官杀混杂，事业上会有一些挑战和变动...",
        "astro": "土星正在考验你的事业宫，这是一个调整期...",
        "tarot": "高塔牌出现，意味着旧的模式将被打破，但这是成长的契机..."
    },
    
    "relationship_stable": {
        "bazi": "你的夫妻宫稳定，感情生活比较和谐...",
        "astro": "金星在你的感情宫，带来温馨和稳定...",
        "tarot": "圣杯二正位，象征着平等和谐的伴侣关系..."
    }
}
```

---

## 💬 实际应用示例

### 场景: 用户咨询事业

**用户**: "我最近想换工作，但又不确定"

**步骤1: 计算各系统结果**

```python
# 八字系统
bazi_result = {
    "trend": "challenge",  # 挑战期
    "timing": "not_now",   # 不是好时机
    "advice": "稳步前行"
}

# 星座系统
astro_result = {
    "trend": "challenge",  # 同样是挑战期
    "timing": "not_now",   # 时机未到
    "advice": "先观察"
}

# 塔罗系统
tarot_result = {
    "trend": "transition", # 转变期
    "timing": "soon",      # 很快
    "advice": "准备迎接变化"
}
```

**步骤2: 冲突检测**

```python
# 检测到冲突: 塔罗说"soon"，其他说"not_now"
detect_conflict([bazi_result, astro_result, tarot_result])
# 返回: True
```

**步骤3: 冲突解决**

```python
# 综合分析: 趋势一致(都是挑战/转变期)，时机有分歧
# 解决方案: 折中表达

unified_result = {
    "trend": "transition",  # 统一为转变期
    "timing": "prepare",    # 准备期(折中)
    "advice": "可以先准备，但暂缓行动"
}
```

**步骤4: 多视角表达**

**八字大师**:
> "从你的命盘看，今年确实进入了事业变动期(官杀混杂)，这种想改变的想法是正常的。但今年流年不利，贸然变动可能有风险。我的建议是：可以先观察机会，做好准备，等明年时机更成熟再行动。"

**星座师**:
> "你的星盘显示，土星正在考验你的事业宫，这是一个内省和调整的时期。虽然你渴望变化，但现在更适合沉淀和积累。不妨利用这段时间提升自己，为下一次机会做准备。"

**塔罗师**:
> "你抽到了'隐士'牌，象征着需要独处和反思的时期。这不是行动的时候，而是准备的时候。命运之轮正在转动，变化会到来，但需要你耐心等待。"

**→ 结论一致**: 都是说"先准备，暂缓行动"

---

## 🛠️ MVP实现建议

### Phase 1: 只做八字 (验证核心假设)

**为什么？**
- 2人兼职团队，资源有限
- 八字是中国用户接受度最高的
- 避免多系统一致性的复杂性
- 先跑通MVP，再扩展

**实现**:
- 只做 `BaziMaster` persona
- 不做冲突检测 (只有一个系统)
- 简化统一画像 (只存八字相关)

---

### Phase 2: 增加星座 (处理两系统一致性)

**实现**:
- 增加 `AstroMaster`
- 实现两系统冲突检测
- 优化统一画像格式

**测试**:
- 找10个用户，分别用两个系统咨询同一问题
- 看结论是否一致
- 收集反馈，优化算法

---

### Phase 3: 增加塔罗 (完善多系统)

**实现**:
- 增加 `TarotMaster`
- 完善冲突解决策略
- 提供"多大师会诊"功能

**功能**:
- 用户可以选择咨询哪位大师
- 或者"综合咨询"(多位大师一起)

---

## ✅ 总结

### 一致性保证的核心

1. **统一用户画像** - 底层特征一致
2. **统一计算结果格式** - 便于比较
3. **冲突检测机制** - 发现问题
4. **冲突解决策略** - 消除矛盾
5. **模糊化表达** - 避免绝对化结论

### 关键原则

- ✅ **不说绝对的话** - "会有挑战"而非"会失败"
- ✅ **强调共同点** - 不同系统都说"现在是调整期"
- ✅ **给用户选择权** - "你可以考虑..."而非"你必须..."
- ❌ **避免直接矛盾** - 不说"八字说好，星座说坏"

### MVP建议

**先只做八字**，验证核心假设后，再逐步扩展其他系统。

---

**一致性设计完成，多系统可以和谐共存！** 🎯✨
