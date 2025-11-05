# “糖尿病自疗”AI 智能 App｜整体方案（V1.0）

> 本文档基于《糖尿病居家调养自疗金典》（向红丁）拆解卡、既有模板及 ADA/CDC 等权威指南，结合现有产品基线，形成一份可直接落地的智能 App 产品与技术一体化方案。目标是将“吃—动—睡—测”四大闭环转化为用户可执行的日常任务，并确保安全边界清晰、信息合规。

---

## 1. 产品概述

### 1.1 产品定位

- **角色**：面向 2 型糖尿病、糖耐量受损以及代谢综合征成年人的“自我管理教练”，不替代专业医疗诊疗。
- **核心价值**：提供循证、安全、易执行的生活干预行动卡，帮助用户在饮食、运动、睡眠、监测等维度建立长期可持续的习惯。
- **差异化**：
  1. **中西合参**：在循证框架下提供辅助性的中医食疗与经穴保健 SOP，所有足底刺激均依据风险自动禁用。
  2. **情境化剧本**：内置低血糖 15–15、病日（Sick Day）、足部护理、外食助手等场景剧本，实现“一键执行”。
  3. **会话 + 任务双引擎**：ChatGPT 会话教练提供知识与陪伴，任务系统负责计划、提醒、打卡、复盘。

### 1.2 用户细分

| 分层 | 特征 | 设计重点 |
| --- | --- | --- |
| A 新诊断 | 刚确诊，需要从零建立日常 | 快速入门、基础教育、引导目标设置 |
| B 波动管理 | 控糖波动、伴随高血压/高脂血症 | 数据趋势、风险提示、个性化调整 |
| C 老年/照护者 | 视觉/操作能力有限 | 简化界面、大字模式、语音播报 |
| D 用药人群 | 胰岛素、磺脲类、SGLT2 等 | 高安全警报、药食/运动时序提示 |

### 1.3 核心原则

1. **安全优先**：红旗症状优先识别，必要时中断自疗建议并给出就医指引。
2. **循证为本**：所有建议与行动卡均锚定 ADA/CDC/中华医学会指南及书籍拆解卡。
3. **非药物优先**：饮食、运动、作息、心理干预优先，药物内容仅做通用教育。
4. **个体化执行节奏**：依据分层及数据表现自动调整目标与提醒频率。

---

## 2. 功能架构

```
┌───────────────────────┐
│ 智能教练（ChatGPT）    │
│  ├─问答/微课程/自查    │
│  └─TCM 辅助（证型→SOP）│
├───────────────────────┤
│ 计划与打卡             │
│  ├─吃/动/睡/测计划      │
│  └─提醒/打卡/复盘       │
├───────────────────────┤
│ 情境剧本               │
│  ├─低血糖 15–15        │
│  ├─病日规则            │
│  ├─足部护理            │
│  └─外食助手            │
├───────────────────────┤
│ 数据记录与仪表盘       │
│  ├─手动&设备数据        │
│  └─周报&趋势&安全指标   │
└───────────────────────┘
```

### 2.1 智能教练模块

- **问答与辅导**：围绕饮食、运动、睡眠、压力、足部护理、外出等常见问题提供多轮问答。
- **微课程**：每日推送 1 条卡片式知识点，可收藏、复习，形成学习路径。
- **TCM 辅助**：提供证型自评问卷，生成对应食疗与经穴保健 SOP；如检测到外周神经病变/足底风险，自动屏蔽足底刺激建议。

### 2.2 计划与打卡

- **吃**：一日三餐结构建议、碳水分配、餐前餐后提醒、餐后 15 分步行提示。
- **动**：周≥150 分中等强度有氧 + 2–3 次抗阻；不足时给分解计划（10 分钟起步）。
- **睡**：固定作息、晚餐距睡≥3 小时、睡眠卫生提示。
- **测**：空腹/餐后 2h/睡前/运动前后血糖，自动生成周复盘。

### 2.3 情境化剧本

1. **低血糖 15–15**：识别→吞咽评估→速效糖清单→ 15 分倒计时→复测→循环/就医→复盘。
2. **病日规则**：症状收集→补液/监测/危险信号→就医提示→药物暂缓须咨询医生。
3. **足部护理**：每日足检清单、风险上报、照片留痕、异常自动建议就医。
4. **外食助手**：菜系→盘子法组合→碳水估算→替换建议→餐后计划→收藏。

### 2.4 数据仪表盘

- 支持手动输入与设备同步（CGM、穿戴设备、体重秤等）。
- 周目标达成率、血糖趋势、腰围/体重变化、步数、睡眠质量与安全事件统计。
- 周报输出“AI 复盘 + 最小可行改动（MVA）”。

---

## 3. 会话工程

### 3.1 Prompt 结构

- **System**：
  - 明确角色为糖尿病居家管理教练。
  - 强调不提供诊断与个体化药剂量，优先识别红旗与禁忌。
  - 遇低血糖/无法吞咽/意识差等情况时立即进入急救剧本。
  - 对足部或外周神经病变用户禁足底刺激建议。
  - 输出格式：中文、分点描述、结尾附 1 条可执行步骤。
- **Developer**：
  - 优先使用本地知识库（书籍拆解卡、模板）。
  - 药物部分仅提供通用教育，不给剂量与停药建议。
  - 每轮只问 1 个问题确认。
- **User**：自然语言或结构化表单。

### 3.2 关键对话剧本

详见 [模块 PRD 节](#6-模块-prd) 对低血糖 15–15 与外食助手的状态机、话术及验收标准。

### 3.3 安全触发

- 命中红旗（胸痛、呼吸困难、持续呕吐、偏瘫、视物骤变、足部感染等）→ 即刻输出就医指引并停止一般建议。
- 药物相关触发：
  - 胰岛素/磺脲类 + 空腹/运动 → 自动提醒低血糖风险。
  - SGLT2 + 饥饿/感染/脱水 → 弹出酮症酸中毒风险教育。
- 足部禁区：有神经病变/溃疡史 → 屏蔽足底刺激相关建议。

---

## 4. 知识与 RAG 体系

### 4.1 知识库构成

1. **本地书籍拆解卡**：饮食、运动、监测、低血糖、病日、足部、药物相互作用。
2. **内部模板**：饮食策略对照表、一周菜单模板、经穴保健 SOP、低血糖剧本、外食组合库。
3. **外部权威摘要**：ADA/CDC/中华医学会指南条目结构化为可检索卡片。

### 4.2 检索策略

- 用户查询 → 关键词抽取（医学词典 + 同义词）→ 向量召回本地条目 → 按照重要性排序 → 不足时再检索外部（可配置）。
- 输出遵循“引用依据 + 行动步骤”格式，必要时给出禁忌提示。

### 4.3 RAG 技术栈

- 向量库：Faiss / Milvus / pgvector，支持分层命名空间。
- 数据预处理：文本切片、标题保留、医学名词扩展。
- 召回后处理：安全审查、规则引擎校验、格式化输出。

---

## 5. 技术架构

```
┌──────────────────────────────┐
│ Mobile/Web 客户端            │
│  ├─Flutter/React Native UI   │
│  ├─推送/提醒/离线缓存        │
└──────────────┬───────────────┘
               │
        ┌──────▼────────────────────────┐
        │ 应用服务层（Node.js / Python） │
        │  ├─用户与分层服务              │
        │  ├─计划与打卡服务              │
        │  ├─剧本引擎（低血糖/外食等）   │
        │  ├─数据记录 & 仪表盘          │
        │  ├─通知服务（FCM/ APNs）       │
        └──────┬────────────────────────┘
               │
   ┌───────────▼───────────┐     ┌─────────────────────┐
   │ AI 协同层              │     │ 数据与分析层         │
   │  ├─会话编排（LangChain）│     │  ├─OLTP：PostgreSQL │
   │  ├─RAG 检索服务         │     │  ├─对象存储（足部照）│
   │  ├─规则引擎（Drools）   │     │  ├─时序/指标库       │
   │  └─LLM API 适配（OpenAI）│     │  └─湖仓/BI（dbt+Looker）│
   └───────────┬───────────┘     └─────────────────────┘
               │
        ┌──────▼────────────┐
        │ 安全与合规          │
        │  ├─隐私&权限        │
        │  ├─审计日志         │
        │  └─模型提示审查     │
        └────────────────────┘
```

### 5.1 主要技术选型

- **客户端**：Flutter（主推）、Web 版可用 React。
- **后端框架**：FastAPI / NestJS，满足高并发和易扩展。
- **数据库**：PostgreSQL（结构化数据）、S3/OSS（足部照片）、Redis（队列/缓存）。
- **AI 编排**：LangChain/ LlamaIndex，用于构建 RAG 与工具调用。
- **规则引擎**：JSON 规则 + Drools（或自研），支持动态更新安全策略。
- **分析**：dbt + Looker/Metabase，事件埋点统一写入 `product.events`。

### 5.2 接口设计

详见 [附录 H](#h-openapi-31-草案节选) 与 [I 节](#i-postman-示例请求v21-片段)。核心接口包含：

- `/onboarding`、`/plan`、`/records`、`/dashboard` 基础服务。
- `/events/hypo`、`/alerts/push`、`/meal/assist`、`/favorites` 等模块专用接口。
- `LLM tools`：`hypo_protocol`、`meal_planner` 等函数调用定义。

### 5.3 数据模型（核心实体）

- `User`：基础信息、药物、合并症、风险标签。
- `Glucose`：血糖测量记录（来源/场景）。
- `Meal`：饮食记录、估算碳水、笔记。
- `Activity`：运动类型、时长、强度。
- `FootCheck`：足部检查、照片、发现。
- `EventHypo`：低血糖事件生命周期字段。

---

## 6. 模块 PRD

### 6.1 低血糖 15–15 剧本

- **目标**：实现低血糖识别→处置→复测→复盘的流程化闭环，目标闭环率 ≥90%。
- **范围**：症状/数值触发、护理者代操作；不涉及药物剂量调整。
- **用户故事**：三步内进入处置、提供 15g 速效糖清单、15 分自动提醒、复盘诱因。
- **流程**：详见状态机图（参见原始 PRD），涵盖吞咽评估、倒计时、复测、循环、就医。
- **关键文案**：分步骤提示、速效糖等价表、复测提醒、结束总结与预防建议。
- **规则引擎**：R1–R4（低血糖触发、吞咽异常急救、循环次数上限、SGLT2 风险提示）。
- **API 合同**：`POST /events/hypo`、`POST /alerts/push`、`hypo_protocol` 函数。
- **验收标准**：症状触发三步内进入流程、倒计时提醒准确、数据写入成功、离线策略预留。
- **埋点**：`HYPO_TRIGGERED`、`HYPO_CYCLE_DONE`、`HYPO_CLOSED`、`HYPO_ESCALATED`。

### 6.2 外食助手

- **目标**：外食点餐→估算→替换→餐后策略闭环，完成率 ≥80%。
- **范围**：常见菜系，估算级营养，不含精确数据库（后续接第三方）。
- **用户故事**：三套组合、碳水估算与提醒、一键收藏。
- **流程**：收集上下文→生成盘子法组合→碳水估算→替换→确认→餐后计划。
- **规则引擎**：甜饮提示、主食过量提醒、肾病高钾提示。
- **API 合同**：`POST /meal/assist`、`POST /favorites`、`meal_planner` 函数。
- **验收标准**：返回≥2 套组合、主食替换更新碳水、收藏成功、生成餐后计划。
- **埋点**：`MEAL_ASSIST_OPENED`、`MEAL_SET_ACCEPTED`、`SWAP_APPLIED`、`POST_MEAL_PLAN_ACCEPTED`、`FAVORITE_SAVED`。

---

## 7. 安全与合规

1. **红旗管理**：统一词典，触发后所有模块转入就医指引。
2. **药物安全**：识别胰岛素/磺脲类/SGLT2 风险场景，提供防范提示。
3. **足部禁忌**：神经病变/溃疡史标记后禁用足底刺激建议。
4. **隐私保护**：最小必要采集、端到端加密、数据脱敏、导出删除功能、审计日志。
5. **模型安全**：Prompt 黑名单、输出审查、敏感问题 fallback。

---

## 8. 指标体系

- **使用指标**：7 日留存、日均对话数、计划达成率、外食助手完成率。
- **健康指标**：自填 HbA1c、平均血糖与变异度、腰围、低血糖事件率。
- **安全指标**：红旗触发率、事件关闭时长、误导内容工单数。
- **模块 KPI**：低血糖闭环率、升级率；外食助手收藏率等。
- 详见 [J 节](#j-埋点仪表盘lookml--sql示例) 中的 LookML/SQL 报表及 dbt 模型。

---

## 9. 路线图

| 里程碑 | 周期 | 主要交付 |
| --- | --- | --- |
| M0 | 0–2 周 | RAG 知识库 1.0、低血糖/病日/足部剧本 MVP |
| M1 | 4–6 周 | 计划与打卡、仪表盘、外食助手 MVP |
| M2 | 8–10 周 | 设备接入（步数/手环/CGM Beta）、个体化推送 |
| M3 | 12–16 周 | 人群分层、A/B 测试、社群与微课程 |

---

## 10. 对外传播与合作

- **一句话卖点**：把“吃动睡测”拆成小步骤，安全、可坚持、看得见进步。
- **三大卖点**：情境剧本、一对一会话教练、中西合参的辅助方案。
- **免责声明**：App 不提供诊断或药物剂量调整，紧急情况请立即就医。
- **合作方向**：接入 DSMES 课程、线下门店/诊所转诊、第三方设备与营养 API。
- **合规**：隐私协议、数据加密备份、定期指南更新与医疗风险评审。

---

## 11. 附录

- [模块 PRD—低血糖 15–15 & 外食助手](#6-模块-prd)
- [用户测试脚本](#g-端到端用户测试脚本含伪数据)
- [OpenAPI 3.1 草案](#h-openapi-31-草案节选)
- [Postman 示例请求](#i-postman-示例请求v21-片段)
- [埋点与分析 SQL/LookML](#j-埋点仪表盘lookml--sql示例)
- [dbt 项目骨架](#k-dbt-项目打包models--macros--sources)

---

## G. 端到端用户测试脚本（含伪数据）

> 验证低血糖 15–15 与外食助手模块的触发、执行、提醒、记录及指标闭环。时间均为 ISO8601 伪数据。

### G1｜低血糖—症状触发—一次循环成功

1. 用户 `u_123`（男，45 岁，基础-餐时胰岛素）。
2. 输入“手抖出汗头晕” → 三步内进入 HYPO 流程。
3. 能吞咽 → 选择葡萄糖片 4 片（≈15 g）。
4. 启动 15 分倒计时，提醒复测，输入 4.7 mmol/L。
5. 系统给出碳水+蛋白加餐建议，记录事件与诱因（运动前未加餐）。
6. 事件写入 `/events/hypo`，仪表盘可见。

### G2｜低血糖—CGM 数值触发—两轮后升级

1. 用户 `u_456`（女，62 岁，磺脲类）。
2. CGM 连续 3.4 mmol/L → 自动触发 HYPO。
3. 第一轮含糖饮料 150 ml；15 分后 3.6 → 第二轮。
4. 第二次仍 <3.9 → 系统提示联系医生/急诊，事件标记升级。

### G3｜外食助手—点餐→替换→收藏→复盘

1. 用户 `u_789` 输入“今晚两人吃川菜，想控糖少油”。
2. 系统返回至少 2 套组合及碳水估算与提示。
3. 用户将米饭换为花椰菜饭（-20 g 碳水），收藏组合。
4. 系统生成餐后计划（步行 15–20 分 + 2h 复测）。
5. 用户 2h 后上报餐后血糖 8.9 mmol/L → 系统给出下次优化建议。

---

## H. OpenAPI 3.1 草案（节选）

```yaml
openapi: 3.1.0
info:
  title: Diabetes Self-care API
  version: 1.0.0
servers:
  - url: https://api.example.com
paths:
  /events/hypo:
    post:
      summary: Log a hypoglycemia event
      operationId: createHypoEvent
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EventHypo'
      responses:
        '201': { description: Created }
  /alerts/push:
    post:
      summary: Send a push/local alert (e.g., 15-min recheck)
      operationId: sendAlert
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AlertPush'
      responses:
        '202': { description: Accepted }
  /meal/assist:
    post:
      summary: Generate restaurant meal sets with carb estimates and swaps
      operationId: mealAssist
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/MealAssistRequest'
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/MealAssistResponse'
  /favorites:
    post:
      summary: Save a favorite meal set
      operationId: saveFavorite
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Favorite'
      responses:
        '201': { description: Created }
components:
  schemas:
    AlertPush:
      type: object
      properties:
        user_id: { type: string }
        type: { type: string, enum: [timer, announcement] }
        payload:
          type: object
          properties:
            title: { type: string }
            body: { type: string }
            ts: { type: string, format: date-time }
    EventHypo:
      type: object
      required: [user_id, ts, trigger]
      properties:
        user_id: { type: string }
        ts: { type: string, format: date-time }
        trigger: { type: string, enum: [symptom, value, cgm] }
        value: { type: number, description: 'lowest measured glucose in mmol/L' }
        carb_taken:
          type: object
          properties:
            type: { type: string, enum: [glucose_tabs, sugary_drink, juice, sugar_cubes, honey] }
            grams: { type: number }
        recheck:
          type: object
          properties:
            ts: { type: string, format: date-time }
            value: { type: number }
        cycles: { type: integer, minimum: 0 }
        follow_snack: { type: boolean }
        notes: { type: string }
        risk_flags:
          type: array
          items: { type: string }
        escalated: { type: boolean }
        closed_at: { type: string, format: date-time, nullable: true }
    MealAssistRequest:
      type: object
      properties:
        user_id: { type: string }
        cuisine: { type: string }
        preferences:
          type: object
          properties:
            low_gi: { type: boolean }
            low_salt: { type: boolean }
            no_alcohol: { type: boolean }
        party_size: { type: integer, minimum: 1 }
    MealAssistResponse:
      type: object
      properties:
        sets:
          type: array
          items:
            $ref: '#/components/schemas/MealPlan'
        swap_suggestions:
          type: array
          items:
            type: object
            properties:
              from: { type: string }
              to: { type: string }
              carb_delta: { type: number }
        post_meal_plan:
          type: object
          properties:
            walk_min: { type: integer }
            recheck_after_min: { type: integer }
    MealPlan:
      type: object
      properties:
        name: { type: string }
        items:
          type: array
          items:
            type: object
            properties:
              dish: { type: string }
              carb_g: { type: number }
              note: { type: string }
        est_carb: { type: number }
        tips:
          type: array
          items: { type: string }
    Favorite:
      type: object
      properties:
        user_id: { type: string }
        title: { type: string }
        payload: { $ref: '#/components/schemas/MealPlan' }
```

---

## I. Postman 示例请求（v2.1）

```json
{
  "info": {
    "name": "Diabetes Self-care API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Create Hypo Event",
      "request": {
        "method": "POST",
        "header": [
          {"key": "Authorization", "value": "Bearer {{token}}"},
          {"key": "Content-Type", "value": "application/json"}
        ],
        "url": "{{base_url}}/events/hypo",
        "body": {
          "mode": "raw",
          "raw": "{\n  \"user_id\":\"u_123\",\n  \"ts\":\"2025-11-05T09:30:00Z\",\n  \"trigger\":\"symptom\",\n  \"value\":3.6,\n  \"carb_taken\":{\"type\":\"glucose_tabs\",\"grams\":15},\n  \"recheck\":{\"ts\":\"2025-11-05T09:45:00Z\",\"value\":4.7},\n  \"cycles\":1,\n  \"follow_snack\":true,\n  \"notes\":\"brisk walk before lunch\"\n}"
        }
      }
    },
    {
      "name": "Meal Assist",
      "request": {
        "method": "POST",
        "header": [
          {"key": "Authorization", "value": "Bearer {{token}}"},
          {"key": "Content-Type", "value": "application/json"}
        ],
        "url": "{{base_url}}/meal/assist",
        "body": {
          "mode": "raw",
          "raw": "{\n  \"user_id\":\"u_789\",\n  \"cuisine\":\"Chinese\",\n  \"preferences\":{\"low_gi\":true,\"low_salt\":true,\"no_alcohol\":true},\n  \"party_size\":2\n}"
        }
      }
    },
    {
      "name": "Save Favorite",
      "request": {
        "method": "POST",
        "header": [
          {"key": "Authorization", "value": "Bearer {{token}}"},
          {"key": "Content-Type", "value": "application/json"}
        ],
        "url": "{{base_url}}/favorites",
        "body": {
          "mode": "raw",
          "raw": "{\n  \"user_id\":\"u_789\",\n  \"title\":\"公司楼下川菜稳妥组合\",\n  \"payload\":{\n    \"name\":\"组合A\",\n    \"items\":[{\"dish\":\"清炒时蔬\",\"carb_g\":6},{\"dish\":\"清蒸鱼\",\"carb_g\":0},{\"dish\":\"半碗杂粮饭\",\"carb_g\":25}],\n    \"est_carb\":31,\n    \"tips\":[\"先菜后饭\",\"少油少盐\"]\n  }\n}"
        }
      }
    }
  ]
}
```

---

## J. 埋点仪表盘：LookML / SQL 示例

- **事件表**：`product.events`（字段：`event_type`、`user_id`、`ts`、`payload`、`closed_at`、`escalated` 等）。
- **关键报表**：
  1. HYPO 闭环率与关闭时长（日）。
  2. HYPO 升级率。
  3. 外食助手转化漏斗。
  4. 7 日留存（滚动）。
- SQL 见下文；BigQuery 需替换 `EXTRACT(EPOCH ...)` 与 `FILTER` 语法。

### J.1 LookML 视图与探索

```yaml
view: events {
  sql_table_name: product.events ;;
  dimension_group: time {
    type: time
    timeframes: [raw, date, week, month]
    sql: ${TABLE}.ts ;
  }
  dimension: event_type { sql: ${TABLE}.event_type ; }
  dimension: user_id { sql: ${TABLE}.user_id ; }
  measure: events { type: count }
  measure: users { type: count_distinct; sql: ${user_id} ; }
  measure: closed_rate {
    type: number
    sql: CASE WHEN COUNT(*)=0 THEN 0 ELSE SUM(CASE WHEN ${TABLE}.closed_at IS NOT NULL THEN 1 ELSE 0 END)::float/COUNT(*) END ;
    value_format_name: percent
  }
  measure: avg_time_to_close_seconds {
    type: average
    sql: EXTRACT(EPOCH FROM (${TABLE}.closed_at - ${TABLE}.ts)) ;
  }
}
```

### J.2 SQL 报表示例

```sql
-- HYPO 闭环率
SELECT
  DATE(ts) AS d,
  COUNT(*) AS hypo_total,
  SUM(CASE WHEN closed_at IS NOT NULL THEN 1 ELSE 0 END) AS hypo_closed,
  ROUND(100.0*SUM(CASE WHEN closed_at IS NOT NULL THEN 1 ELSE 0 END)/COUNT(*),2) AS closed_rate_pct,
  AVG(EXTRACT(EPOCH FROM (closed_at - ts)))/60.0 AS avg_close_min
FROM product.events
WHERE event_type = 'HYPO'
GROUP BY 1
ORDER BY 1;
```

更多 SQL 与 dbt 模型参见原文档中给出的完整示例（保留 BigQuery 改写说明）。

---

## K. dbt 项目骨架

- 目录结构：`models/sources.yml`、`models/marts/analytics/*.sql`、`macros/metrics.sql`、`dbt_project.yml`。
- 提供跨仓兼容的 `seconds_between`、`safe_percent` 宏，统一指标计算。
- 运行步骤：`dbt debug` → `dbt run --select marts.analytics` → `dbt docs generate`。

---

## L. 风险与缓解

1. **误将建议视为医疗指示**：全流程免责声明、红旗触发就医、药物提示强调“咨询医生”。
2. **营养估算偏差**：提供区间与趋势管理定位，后续接入第三方营养数据库校准。
3. **警报疲劳**：红旗与提醒设定静默窗口，合并周计划推送。
4. **隐私与合规**：最小采集、端到端加密、可导出与删除、季度风险评审。

---

## M. 后续工作建议

1. 将 OpenAPI 与 Postman 文件拆分为独立可下载资产。
2. 构建 RAG 数据管道的自动更新流程（CI/CD）。
3. 设计 UI 样稿（外食助手组合卡片、低血糖流程动效）。
4. 与医疗顾问建立季度审查机制，更新指南卡片与安全规则。

