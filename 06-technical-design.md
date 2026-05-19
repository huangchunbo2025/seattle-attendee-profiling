# 技术设计方案 — 参会公司深度画像与活动执行系统

> CTO 审查: 有条件批准 — 2026-05-18

> 架构师: Senior Frontend Architect | 2026-05-18
> 需求来源: 05-prd.md (唯一需求来源)
> 设计语言参考: socialhub-overview.html (navy/cyan/purple)
> 3 轮自我对抗迭代

---

## 一、架构决策

### 1.1 文件结构: 单 HTML 文件

**决策**: 单个自包含 HTML 文件，CSS/JS 全部内联。

**理由**:
- NFR-01 要求离线可用，无外部依赖
- NFR-04 要求 < 500KB (预估纯文本+CSS+JS 约 80-120KB，远低于限制)
- 目标用户 (CEO) 需要"双击打开即用"的零门槛体验
- 11 家公司的数据量有限，不需要分文件加载

**文件布局**:

```
attendee-profiling.html  (单文件，约 100-120KB)
├── <style>        内联 CSS (~15KB)
├── <script>       数据模型 JSON + 交互 JS (~25KB)
└── <body>         HTML 结构 + 内容 (~60-80KB)
```

### 1.2 数据与视图分离

虽然是单文件，内部采用 **数据驱动** 架构:

```html
<script>
  // ===== 数据层: 所有公司数据集中定义 =====
  const COMPANIES = [ /* 11 家公司数据对象 */ ];
  const CEO_CARDS = [ /* 7 张速查卡数据 */ ];
  const WFIT_SCORES = [ /* WFIT 评分矩阵 */ ];
  const DEMO_MODULES = [ /* 5 个 Demo 模块 */ ];
  const RESPONSE_CARDS = [ /* 5 张应对策略卡 */ ];
  const EMAIL_TEMPLATES = [ /* 3 档感谢邮件 */ ];
  const CHECKLIST_ITEMS = [ /* 按时段分组的清单项 */ ];
</script>
```

**好处**:
- 数据修正只需改 JSON 对象，不用翻找 HTML
- 未来 V1.1/V2 迭代时，数据可抽离为独立文件
- 降低内容填充阶段的出错率

### 1.3 渲染策略: 混合模式

| 区域 | 渲染方式 | 理由 |
|---|---|---|
| CEO 速查卡 | JS 动态渲染 | 数据结构一致，模板化效率高 |
| 完整情报卡 | JS 动态渲染 | 11 张卡片结构统一，四层折叠 |
| WFIT 矩阵表 | JS 动态渲染 | 需要排序交互 |
| Demo 策略表 | 静态 HTML | 仅 5 行，无交互需求 |
| Checklist | JS 动态渲染 | 需要 localStorage 持久化 |
| 应对策略卡 | 静态 HTML | 仅 5 条，固定内容 |
| 感谢邮件模板 | 静态 HTML | 固定内容，复制即用 |
| 统一叙事 | 静态 HTML | 固定内容，每张卡底部重复 |

---

## 二、数据模型

### 2.1 公司核心数据 (Company)

```javascript
const COMPANIES = [
  {
    id: "canada-goose",
    name: "Canada Goose",
    tier: "P0",                    // P0 | P1 | P1-watch | P2 | P3 | Partner
    industry: "高端外套/服饰",
    whyNow: "新 CDIO + 招 VP AI + D365 核心 = 技术路线图重写中",

    // 参会者 (支持多人，如 IHG 双人)
    attendees: [
      {
        name: "Johnney Cao",
        title: "Director, Dynamics 365 Solution Architecture",
        influence: "强影响者",      // 决策者 | 强影响者 | 执行者
        background: "D365 架构专家",
        linkedIn: "https://linkedin.com/in/...",
        sharedContext: null          // 共同背景 (如 Wei 的 Lululemon 经历)
      }
    ],

    // L2: 对话准备
    conversation: {
      hook: "D365 在 DTC 场景的客户数据统一架构怎么做的？",
      pain: [
        "DTC 72% 收入但客户数据分散在 D365 + 电商 + 门店",
        "招 VP AI 说明内部 AI 能力不足"
      ],
      ask: "约 30 分钟 D365 集成 demo",
      redLines: [
        "别暗示 D365 不够用",
        "别假设他们没有数据策略"
      ],
      techPrep: null                 // Canada Goose 专属: D365 技术问题准备
    },

    // L3: 深度背景
    depth: {
      aiStatus: "...",              // AI 近况 (富文本/markdown)
      loyaltyStatus: "...",         // 会员/忠诚度现状
      martech: "...",               // MarTech 技术栈
      competition: "...",           // 竞争态势
      fitScore: 5                   // F 分 (1-5)
    },

    // 切入路径
    pathway: "A",                   // A: Microsoft 生态 | B: AI 增强层 | C: 从零构建
    demoModules: ["D1"]             // 推荐 Demo 模块 ID
  },
  // ... 其余 10 家
];
```

### 2.2 WFIT 评分 — 内嵌到 Company 对象 (CTO 修正 R1-1)

> **CTO 修正**: 原方案将 WFIT 评分独立为 `WFIT_SCORES` 数组，与 `COMPANIES` 存在 tier/companyId 重复。
> CEO 活动前一天临时调整优先级时，需同步修改多处，易出错。
> **修正**: 将 WFIT 字段合并到 `COMPANIES[i].wfit` 中，`tier` 仅在 Company 上定义一次。
> `CEO_CARDS` 的 `companyId` 字段通过引用 `COMPANIES` 数据派生，不硬编码 tier。

```javascript
// WFIT 评分合并到 Company 对象内
const COMPANIES = [
  {
    id: "canada-goose",
    // ...其余字段不变...
    wfit: {
      W: 5,   // Window 窗口期
      F: 5,   // Fit 产品契合
      I: 3,   // Influence 参会者影响力
      T: 4,   // Ticket 预算潜力
    },
    // tier 和 total 由 getter 函数派生
  },
  // ... 其余 10 家
];

// 加权公式: Total = W*0.3 + F*0.25 + I*0.25 + T*0.2
function calcWFIT(w) { return +(w.W*0.3 + w.F*0.25 + w.I*0.25 + w.T*0.2).toFixed(1); }

// WFIT 表格数据从 COMPANIES 派生，不独立维护
function getWFITScores() {
  return COMPANIES
    .filter(c => c.wfit)
    .map(c => ({ ...c.wfit, companyId: c.id, name: c.name, total: calcWFIT(c.wfit), tier: c.tier }))
    .sort((a, b) => b.total - a.total);
}
```

### 2.3 CEO 速查卡 (CEOCard)

```javascript
const CEO_CARDS = [
  // 卡 1-5: P0/P1 公司
  {
    type: "company",             // company | summary | host
    companyId: "canada-goose",
    headline: "在招 VP AI，D365 核心，CDP 空白",
    opener: "D365 在 DTC 场景的数据统一",
    goal: "约 D365 集成 demo",
    redLine: "别暗示 D365 不够用"
  },
  // 卡 6: 其他参会者汇总
  {
    type: "summary",
    items: [
      { name: "Russ Kasymov", company: "Lululemon", note: "架构师，碎片化整合" },
      { name: "Tom Hanrahan", company: "RealReal", note: "新 CRO，2 个月窗口" },
      // ...
    ]
  },
  // 卡 7: 东道主提醒 + 通用指导
  {
    type: "host",
    hostDuties: [
      "门口迎接每一位到场客人，不分优先级",
      "2-3 分钟开场致辞: 感谢 Microsoft + 定位行业交流",
      "逐一介绍参会嘉宾",
      "每 30 分钟扫视全场，确保无人被冷落"
    ],
    unifiedNarrative: "我们帮品牌用 AI Agent 把百万级会员当个人来运营...",
    fallbackPhrase: "这个领域我们还在学习，能多跟您请教吗？",
    responseCards: [
      { if: "P0 目标对产品话题不感兴趣", then: "转入行业交流模式，不硬推" },
      // ...
    ]
  }
];
```

### 2.4 Demo 模块 (DemoModule)

```javascript
const DEMO_MODULES = [
  {
    id: "D1",
    name: "CIP-D365 原生集成",
    duration: "15 min",
    targets: ["canada-goose", "arcteryx"],
    focus: "数据流架构 + API + 实时同步",
    style: "技术架构图",
    trigger: "Johnney 表示对集成感兴趣时",
    techDepth: "Dataverse API、实体同步、ISV 认证"
  },
  // D2-D5...
];
```

### 2.5 Checklist 项 (ChecklistItem)

```javascript
const CHECKLIST_ITEMS = [
  {
    phase: "host",           // host | before | arrival | dinner | free | closing | post-0 | post-48h | post-72h | post-1w | post-2w
    phaseLabel: "最高优先 — 东道主职责",
    role: "CEO",             // CEO | GTM | ALL
    text: "在门口迎接每一位到场客人（不分优先级，先到先迎）",
    storageKey: "ck-host-01" // localStorage key
  },
  // ...
];
```

### 2.6 感谢邮件模板 (EmailTemplate)

```javascript
const EMAIL_TEMPLATES = [
  {
    level: "deep",
    label: "深度对话版",
    scenario: "与 CEO 有 10+ 分钟对话的参会者",
    subject: "很高兴昨晚聊到 [具体话题] — [姓名]",
    body: "...",             // 含 [placeholder] 标记
    variables: ["name", "topic", "insight", "nextStep"]
  },
  // short, brief...
];
```

---

## 三、组件设计

### 3.1 页面整体结构

```
┌─────────────────────────────────────────────────────┐
│  HEADER: 固定顶部导航栏 (64px, navy 半透明)          │
│  [速查卡] [情报卡] [优先级] [Demo] [Checklist]       │
│  [邮件模板] [应对策略]                                │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S1: HERO BANNER (精简版)                            │
│  "Socialhub.AI x Microsoft Seattle 5/22"             │
│  "参会公司深度画像与活动执行系统"                       │
│  活动日期倒计时                                       │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S2: CEO 速查卡 (scroll-snap 水平滚动)               │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐      │
│  │CG P0 │→│IHG P0│→│AT P1 │→│JC P1 │→│其他  │→ ... │
│  └──────┘ └──────┘ └──────┘ └──────┘ └──────┘      │
│  每张卡底部: 统一叙事 (固定)                          │
│  卡片指示器 (小圆点)                                  │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S3: WFIT 优先级矩阵                                 │
│  [评分表 Tab] [切入路径 Tab] [信号判读 Tab]           │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S4: 完整情报卡 (11 张折叠面板)                       │
│  ▸ Canada Goose [P0] — WHY NOW 一句话               │
│  ▸ IHG [P0]                                         │
│  ▸ Arc'teryx [P1]                                   │
│  ▸ JCPenney [P1]                                    │
│  ▸ RealReal [P1-watch]                              │
│  ... (按 WFIT 总分降序排列)                          │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S5: Demo 策略速查表                                  │
│  D1-D5 表格，点击行展开详情                           │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S6: 活动执行 Checklist                              │
│  按时段分组的可勾选清单                               │
│  [全部展开] [全部折叠] [重置勾选]                     │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S7: CEO 应对策略卡                                   │
│  5 张 "如果...那么..." 卡片                          │
│                                                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  S8: 感谢邮件模板 (3 档)                             │
│  每档: 适用场景 + 邮件主题 + 正文 + [复制] 按钮      │
│                                                      │
├─────────────────────────────────────────────────────┤
│  FOOTER: 版本信息 + 生成时间                          │
└─────────────────────────────────────────────────────┘
```

### 3.2 组件 S2: CEO 速查卡 (核心组件)

**HTML 结构**:

```html
<section id="ceo-cards" class="section">
  <div class="card-carousel">
    <!-- JS 动态渲染 7 张卡 -->
  </div>
  <div class="card-dots">
    <!-- 7 个圆点指示器 -->
  </div>
</section>
```

**CSS 关键实现**:

```css
.card-carousel {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;  /* iOS 惯性滚动 */
  scrollbar-width: none;              /* 隐藏滚动条 */
  gap: 16px;
  padding: 0 16px;
}

.card-carousel::-webkit-scrollbar { display: none; }

.ceo-card {
  flex: 0 0 calc(100vw - 32px);      /* 手机端: 几乎全屏宽 */
  max-width: 420px;                   /* 桌面端: 限制最大宽度 */
  scroll-snap-align: center;
  border-radius: 16px;
  padding: 24px;
  min-height: 380px;                  /* 确保单屏内显示完 */
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

/* 优先级色彩: 卡片顶部 4px 色条 */
.ceo-card[data-tier="P0"]::before { background: #DC2626; }
.ceo-card[data-tier="P1"]::before { background: #EA580C; }
.ceo-card[data-tier="P2"]::before { background: #2563EB; }
.ceo-card[data-tier="P3"]::before { background: #6B7280; }
.ceo-card[data-tier="Partner"]::before { background: #16A34A; }
```

**JS 渲染逻辑**:

```javascript
function renderCEOCards() {
  const container = document.querySelector('.card-carousel');
  container.innerHTML = CEO_CARDS.map((card, i) => {
    if (card.type === 'company') return renderCompanyCard(card, i);
    if (card.type === 'summary') return renderSummaryCard(card, i);
    if (card.type === 'host') return renderHostCard(card, i);
  }).join('');
  initCardDots();
}
```

**卡片内部布局 (company 类型)**:

```
┌─ 4px 色条 (tier 颜色) ─────────────────┐
│                                          │
│  CANADA GOOSE              P0 ★★★★★     │  ← 公司名 + 优先级标签
│  Johnney Cao / D365 架构师               │  ← 参会者
│  ─────────────────────────────────────   │
│                                          │
│  在招 VP AI，D365 核心，CDP 空白          │  ← 一句话情报 (加粗，20px)
│                                          │
│  ✦ 开场: D365 在 DTC 场景的数据统一      │  ← 开场话题
│                                          │
│  ✦ 目标: 约 30 分钟 D365 集成 demo       │  ← 期望动作
│                                          │
│  ⚠ 别暗示 D365 不够用                    │  ← 红线 (红色)
│                                          │
│  ──────────────────────────────────────  │
│  💬 "我们帮品牌用 AI Agent 把百万级       │  ← 统一叙事 (固定底部)
│  会员当个人来运营..."                     │
└──────────────────────────────────────────┘
```

### 3.3 组件 S4: 完整情报卡 (11 张折叠面板)

**HTML 结构**: 使用原生 `<details>` + `<summary>`

```html
<section id="intel-cards" class="section">
  <div class="intel-controls">
    <button onclick="expandAll()">全部展开</button>
    <button onclick="collapseAll()">全部折叠</button>
    <input type="search" placeholder="搜索公司/人名..." oninput="filterCards(this.value)" />
  </div>

  <!-- JS 动态渲染 11 张 -->
  <details class="intel-card" data-company="canada-goose" data-tier="P0">
    <summary class="intel-l1">
      <span class="tier-badge p0">P0</span>
      <span class="company-name">Canada Goose</span>
      <span class="attendee">Johnney Cao / Dir, D365 Architecture</span>
      <span class="why-now">新 CDIO + 招 VP AI + D365 核心</span>
    </summary>

    <!-- L2: 对话准备 -->
    <details class="intel-layer" open>
      <summary>对话准备</summary>
      <div class="layer-content">
        <div class="field hook">✦ 开场: ...</div>
        <div class="field pain">⚡ 痛点: ...</div>
        <div class="field ask">🎯 目标: ...</div>
        <div class="field redline">⚠ 红线: ...</div>
        <div class="field tech-prep">🔧 技术准备: ...</div>  <!-- Canada Goose 专属 -->
      </div>
    </details>

    <!-- L3: 深度背景 -->
    <details class="intel-layer">
      <summary>深度背景</summary>
      <div class="layer-content">
        <div class="field">AI 近况: ...</div>
        <div class="field">会员/忠诚度: ...</div>
        <div class="field">MarTech 技术栈: ...</div>
        <div class="field">竞争态势: ...</div>
      </div>
    </details>

    <!-- L4: 个人档案 -->
    <details class="intel-layer">
      <summary>参会者档案</summary>
      <div class="layer-content">
        <div class="field">职业履历: ...</div>
        <div class="field">决策影响力: ...</div>
        <div class="field">共同背景: ...</div>
        <div class="field">LinkedIn: <a href="...">链接</a></div>
      </div>
    </details>
  </details>
</section>
```

**折叠面板样式**:

```css
.intel-card {
  border: 1px solid var(--line);
  border-radius: 12px;
  margin-bottom: 12px;
  overflow: hidden;
  transition: box-shadow 0.2s;
}

.intel-card[open] {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.intel-l1 {
  padding: 16px 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
  list-style: none;  /* 去掉默认三角 */
}

.intel-l1::before {
  content: '▸';
  transition: transform 0.2s;
}

.intel-card[open] > .intel-l1::before {
  transform: rotate(90deg);
}

/* 嵌套 details 的层级缩进 */
.intel-layer {
  border-top: 1px solid var(--line);
  margin: 0 20px;
}

.intel-layer > summary {
  padding: 12px 0;
  font-weight: 600;
  color: var(--navy);
  cursor: pointer;
}
```

### 3.4 组件 S3: WFIT 优先级矩阵 (CTO 修正 R3: 去 Tab，纵向排列)

> **CTO 修正 R3-1**: Tab 切换增加 JS 复杂度但无用户价值 — CEO/GTM 会从头到尾扫一遍，
> 不会在 Tab 之间反复切换。改为纵向依次排列: 评分表 → 切入路径 → 信号判读。
> 同时去掉列排序 — 默认按总分降序一次渲染即可。

```html
<section id="priority" class="section section-alt">
  <h2>合作机会优先级</h2>

  <!-- 评分表: 固定按总分降序 -->
  <h3>WFIT 评分矩阵</h3>
  <div class="table-wrapper">
    <table class="wfit-table">
      <thead>
        <tr>
          <th>公司</th><th>W</th><th>F</th><th>I</th><th>T</th><th>总分</th><th>Tier</th>
        </tr>
      </thead>
      <tbody><!-- JS 渲染，getWFITScores() 已按总分降序 --></tbody>
    </table>
  </div>

  <!-- 切入路径: 静态 HTML -->
  <h3>切入路径</h3>
  <div id="pathways"><!-- 三条路径 A/B/C 静态卡片 --></div>

  <!-- 信号判读: 静态 HTML -->
  <h3>信号判读指南</h3>
  <div id="signals"><!-- 高意向 vs 社交信号对比表 --></div>
</section>
```

### 3.5 组件 S6: 活动执行 Checklist

**localStorage 持久化**:

```javascript
function initChecklist() {
  const container = document.getElementById('checklist');
  container.innerHTML = groupByPhase(CHECKLIST_ITEMS).map(group => `
    <details class="ck-phase" open>
      <summary class="ck-phase-title">${group.phaseLabel}</summary>
      ${group.items.map(item => `
        <label class="ck-item" data-role="${item.role}">
          <input type="checkbox"
            ${isChecked(item.storageKey) ? 'checked' : ''}
            onchange="saveCheck('${item.storageKey}', this.checked)"
          />
          <span class="ck-role">${item.role}</span>
          <span class="ck-text">${item.text}</span>
        </label>
      `).join('')}
    </details>
  `).join('');
}

function saveCheck(key, checked) {
  const state = JSON.parse(localStorage.getItem('checklist-state') || '{}');
  state[key] = checked;
  localStorage.setItem('checklist-state', JSON.stringify(state));
  updateProgress();
}

function isChecked(key) {
  const state = JSON.parse(localStorage.getItem('checklist-state') || '{}');
  return state[key] || false;
}

function updateProgress() {
  const state = JSON.parse(localStorage.getItem('checklist-state') || '{}');
  const total = CHECKLIST_ITEMS.length;
  const done = Object.values(state).filter(Boolean).length;
  document.getElementById('ck-progress').textContent = `${done}/${total}`;
}

function resetChecklist() {
  if (confirm('确认重置所有勾选？')) {
    localStorage.removeItem('checklist-state');
    initChecklist();
  }
}
```

### 3.6 组件 S8: 感谢邮件模板

**一键复制功能**:

```javascript
// CTO 修正 R1-2: file:// 协议下 navigator.clipboard 不可用，
// execCommand('copy') 在 iOS Safari 也不可靠。
// 改为"选中文本"策略: 用户手动长按复制，零兼容性风险。
function selectTemplate(level) {
  const el = document.getElementById(`email-tpl-${level}`);
  if (!el) return;
  const range = document.createRange();
  range.selectNodeContents(el);
  const sel = window.getSelection();
  sel.removeAllRanges();
  sel.addRange(range);
  showToast('已选中，请长按复制');
}
```

---

## 四、交互设计

### 4.1 导航系统

**固定顶部导航栏**:

```css
.nav {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  height: 56px;
  background: rgba(18, 28, 61, 0.95);
  backdrop-filter: blur(12px);
  display: flex;
  align-items: center;
  padding: 0 16px;
  overflow-x: auto;                    /* 手机端可水平滚动 */
  scrollbar-width: none;
  gap: 4px;
}

.nav-btn {
  white-space: nowrap;
  padding: 8px 14px;
  border-radius: 8px;
  color: rgba(255, 255, 255, 0.7);
  font-size: 13px;
  font-weight: 500;
  border: none;
  background: transparent;
  cursor: pointer;
  transition: all 0.2s;
}

.nav-btn.active,
.nav-btn:hover {
  background: rgba(6, 147, 227, 0.2);
  color: #fff;
}
```

**锚点平滑滚动**:

```javascript
document.querySelectorAll('.nav-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const target = document.getElementById(btn.dataset.target);
    target.scrollIntoView({ behavior: 'smooth', block: 'start' });
    // 补偿固定导航栏高度
    window.scrollBy(0, -64);
    // 更新 active 状态
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
  });
});

// IntersectionObserver 自动高亮当前 section
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const id = entry.target.id;
      document.querySelectorAll('.nav-btn').forEach(btn => {
        btn.classList.toggle('active', btn.dataset.target === id);
      });
    }
  });
}, { rootMargin: '-64px 0px -60% 0px' });

document.querySelectorAll('section[id]').forEach(s => observer.observe(s));
```

### 4.2 搜索/筛选

情报卡区域提供搜索框，实现即时筛选:

```javascript
function filterCards(query) {
  const q = query.toLowerCase().trim();
  document.querySelectorAll('.intel-card').forEach(card => {
    const text = card.textContent.toLowerCase();
    card.style.display = (!q || text.includes(q)) ? '' : 'none';
  });
}
```

### 4.3 速查卡圆点指示器

```javascript
function initCardDots() {
  const carousel = document.querySelector('.card-carousel');
  const dots = document.querySelector('.card-dots');
  const cards = carousel.querySelectorAll('.ceo-card');

  dots.innerHTML = Array.from(cards).map((_, i) =>
    `<span class="dot${i === 0 ? ' active' : ''}" data-index="${i}"></span>`
  ).join('');

  // 监听滚动更新圆点
  carousel.addEventListener('scroll', () => {
    const scrollLeft = carousel.scrollLeft;
    const cardWidth = cards[0].offsetWidth + 16; // 含 gap
    const activeIndex = Math.round(scrollLeft / cardWidth);
    dots.querySelectorAll('.dot').forEach((d, i) => {
      d.classList.toggle('active', i === activeIndex);
    });
  });

  // 点击圆点跳转
  dots.addEventListener('click', (e) => {
    if (e.target.classList.contains('dot')) {
      const index = parseInt(e.target.dataset.index);
      const cardWidth = cards[0].offsetWidth + 16;
      carousel.scrollTo({ left: index * cardWidth, behavior: 'smooth' });
    }
  });
}
```

### 4.4 Toast 提示

```javascript
function showToast(msg) {
  const toast = document.createElement('div');
  toast.className = 'toast';
  toast.textContent = msg;
  document.body.appendChild(toast);
  requestAnimationFrame(() => toast.classList.add('show'));
  setTimeout(() => {
    toast.classList.remove('show');
    setTimeout(() => toast.remove(), 300);
  }, 2000);
}
```

---

## 五、视觉设计系统

### 5.1 设计语言对齐 (socialhub-overview.html)

从参考文件提取并复用的设计 token:

```css
:root {
  /* 品牌色 — 继承 socialhub-overview.html */
  --navy: #121C3D;
  --blue: #066aab;
  --cyan: #0693e3;
  --purple: #9b51e0;

  /* 文字 */
  --ink: #1a1a2e;
  --muted: #4a5568;

  /* 背景 */
  --light: #f7f8fc;
  --white: #ffffff;
  --line: #e2e8f0;

  /* 业务专属色 — PRD 定义的优先级色 */
  --p0: #DC2626;
  --p1: #EA580C;
  --p1w: #F59E0B;       /* P1-watch: 橙黄区分 */
  --p2: #2563EB;
  --p3: #6B7280;
  --partner: #16A34A;

  /* 间距系统 (8px 基准) */
  --sp-xs: 4px;
  --sp-sm: 8px;
  --sp-md: 16px;
  --sp-lg: 24px;
  --sp-xl: 32px;
  --sp-2xl: 48px;

  /* 圆角 */
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 16px;

  /* 字体 — 与 socialhub-overview 一致 */
  --font: -apple-system, "Segoe UI", Arial, "PingFang SC", "Microsoft YaHei", sans-serif;

  /* 阴影 */
  --shadow-sm: 0 2px 12px rgba(0,0,0,0.04);
  --shadow-md: 0 4px 20px rgba(0,0,0,0.08);
  --shadow-lg: 0 8px 30px rgba(0,0,0,0.12);
}
```

### 5.2 字体层级

| 层级 | 用途 | 桌面 | 手机 | 字重 |
|---|---|---|---|---|
| H1 | Hero 标题 | 36px | 28px | 800 |
| H2 | Section 标题 | 28px | 22px | 700 |
| H3 | 卡片标题/公司名 | 20px | 18px | 700 |
| Body | 正文 | 16px | 15px | 400 |
| Caption | 标签/标注 | 13px | 12px | 600 |
| Micro | 次要信息 | 12px | 11px | 400 |

### 5.3 优先级标签组件

```css
.tier-badge {
  display: inline-block;
  padding: 2px 10px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 700;
  letter-spacing: 0.5px;
  color: #fff;
}

.tier-badge.p0 { background: var(--p0); }
.tier-badge.p1 { background: var(--p1); }
.tier-badge.p1w { background: var(--p1w); color: #1a1a2e; }
.tier-badge.p2 { background: var(--p2); }
.tier-badge.p3 { background: var(--p3); }
.tier-badge.partner { background: var(--partner); }
```

### 5.4 Hero 区域

精简版，不需要 socialhub-overview 那样的完整品牌展示:

```css
.hero {
  margin-top: 56px;  /* 导航栏高度 */
  padding: 48px 24px 36px;
  background: linear-gradient(135deg, #0a1128, var(--navy), #1a3a6e);
  color: #fff;
  text-align: center;
}

.hero h1 {
  font-size: 28px;
  font-weight: 800;
  margin-bottom: 8px;
}

.hero h1 span { color: var(--cyan); }

.hero .subtitle {
  font-size: 15px;
  color: rgba(255,255,255,0.7);
  margin-bottom: 16px;
}

.hero .countdown {
  display: inline-block;
  padding: 6px 16px;
  border-radius: 20px;
  background: rgba(255,255,255,0.1);
  border: 1px solid rgba(255,255,255,0.2);
  font-size: 14px;
  font-weight: 600;
  color: var(--gold, #f6ad55);
}
```

---

## 六、响应式设计

### 6.1 断点策略 (手机优先)

```css
/* 默认: 手机 (375px - 767px) */
/* 所有基础样式均为手机优先编写 */

/* 平板 (768px+) */
@media (min-width: 768px) {
  .nav { padding: 0 32px; }
  .section { padding: 48px 32px; }
  .ceo-card { max-width: 420px; }
  .wfit-table { font-size: 14px; }
  .intel-l1 { flex-wrap: nowrap; }
}

/* 桌面 (1024px+) */
@media (min-width: 1024px) {
  .section { padding: 60px 48px; max-width: 1200px; margin: 0 auto; }
  .card-carousel {
    justify-content: center;       /* 桌面端居中显示 */
  }
  .ceo-card {
    flex: 0 0 380px;               /* 桌面端固定宽度 */
  }
}
```

### 6.2 手机端关键适配

| 组件 | 手机适配 | 实现方式 |
|---|---|---|
| CEO 速查卡 | 全屏宽度，单卡一屏 | `flex: 0 0 calc(100vw - 32px)` + scroll-snap |
| 导航栏 | 可横向滚动 | `overflow-x: auto` |
| WFIT 表格 | 可横向滚动 | 外层 `overflow-x: auto` wrapper |
| 情报卡 L1 速览 | 堆叠布局 | `flex-wrap: wrap` |
| Checklist | 全宽度 | 自然响应 |
| 感谢邮件 | 全宽度 | 自然响应 |

### 6.3 触摸交互

```css
/* 增大触摸目标 (Apple HIG: 最小 44px) */
.nav-btn { min-height: 44px; min-width: 44px; }
.intel-l1 { min-height: 48px; }
.ck-item { min-height: 44px; padding: 10px 0; }
.tab { min-height: 44px; }

/* 禁止双击缩放 */
* { touch-action: manipulation; }
```

---

## 七、打印优化

### 7.1 @media print 策略

```css
@media print {
  /* 打印色彩保留 */
  * {
    -webkit-print-color-adjust: exact !important;
    print-color-adjust: exact !important;
  }

  /* 隐藏交互元素 */
  .nav,
  .card-carousel,
  .card-dots,
  .tab-bar,
  .intel-controls,
  .copy-btn,
  .toast,
  .ck-reset-btn { display: none !important; }

  /* 展开所有折叠 */
  details { display: block !important; }
  details > summary { display: block !important; }
  details[open] > summary ~ * { display: block !important; }
  /* 强制展开 */
  details:not([open]) > *:not(summary) {
    display: block !important;
  }

  /* 打印专用版本: 不显示速查卡滚动区，改为线性列表 */
  .ceo-card {
    flex: none;
    width: 100%;
    max-width: 100%;
    page-break-inside: avoid;
    margin-bottom: 16px;
    border: 1px solid #ccc;
  }

  /* 情报卡分页控制 */
  .intel-card {
    page-break-inside: avoid;
    page-break-after: auto;
  }

  /* Checklist 紧凑模式: 目标双面 A4 一张 */
  .ck-phase { margin-bottom: 4px; }
  .ck-item {
    font-size: 10pt;
    padding: 2px 0;
    min-height: auto;
  }
  .ck-phase-title { font-size: 11pt; margin-bottom: 2px; }

  /* 缩减间距 */
  .section { padding: 24px 16px; }
  body { font-size: 10pt; line-height: 1.4; }

  /* 页眉页脚 */
  @page {
    margin: 1.5cm;
    @bottom-center {
      content: "Socialhub.AI x Microsoft Seattle 5/22 — 内部机密";
    }
  }
}
```

### 7.2 Checklist 打印专项

Checklist 需要在双面 A4 一张纸内打印完毕。按 PRD FR-05 内容估算:

- 共约 35-40 个 checklist 项
- A4 双面 = ~110 行 (10pt, 1.4 行高)
- 40 项 + 12 个阶段标题 = ~52 行，加上间距约 60 行
- **结论: 可在单面 A4 内完成，双面有充足余量**

增加"仅打印 Checklist"按钮:

```javascript
function printChecklist() {
  document.body.classList.add('print-checklist-only');
  window.print();
  document.body.classList.remove('print-checklist-only');
}
```

```css
@media print {
  .print-checklist-only *:not(#checklist):not(#checklist *) {
    display: none !important;
  }
  .print-checklist-only #checklist {
    display: block !important;
  }
}
```

---

## 八、离线与性能

### 8.1 零外部依赖清单

| 类别 | 方案 | 替代了什么 |
|---|---|---|
| 字体 | 系统字体栈 | Google Fonts / CDN 字体 |
| 图标 | CSS 伪元素 + Unicode 符号 (✦ ⚠ ▸) | Icon 库 (FontAwesome 等) |
| 图表 | 无 (MVP 为表格; V1.1 用内联 SVG) | Chart.js / D3.js |
| 滑动 | CSS scroll-snap | Swiper.js |
| 折叠 | `<details>` 原生标签 | jQuery Accordion |
| 动画 | CSS transition | GSAP / Animate.css |
| 框架 | Vanilla JS | React / Vue |

### 8.2 性能预估

| 项目 | 预估大小 |
|---|---|
| HTML 结构 | ~30KB |
| 内联 CSS | ~15KB |
| 内联 JS (含数据) | ~40KB |
| 11 家公司文字内容 | ~35KB |
| **总计** | **~120KB** (远低于 500KB 限制) |

### 8.3 首屏渲染优化

```html
<!-- CSS 放 head 内，JS 放 body 底部 -->
<head>
  <style>/* 所有 CSS 内联 */</style>
</head>
<body>
  <!-- 静态骨架: Hero + 导航 -->
  <!-- 动态内容区 -->

  <script>
    // 数据定义
    // DOMContentLoaded 后渲染
    document.addEventListener('DOMContentLoaded', () => {
      renderCEOCards();
      renderWFITTable();
      renderIntelCards();
      initChecklist();
      initNavObserver();
    });
  </script>
</body>
```

---

## 九、JS 模块划分 (单文件内逻辑分区)

虽然是单文件，JS 部分使用注释分区保持可维护性:

```javascript
// ═══════════════════════════════════════
// SECTION 1: DATA MODEL
// ═══════════════════════════════════════
const COMPANIES = [...];
const CEO_CARDS = [...];
const WFIT_SCORES = [...];
const DEMO_MODULES = [...];
const CHECKLIST_ITEMS = [...];
const EMAIL_TEMPLATES = [...];
const RESPONSE_CARDS = [...];

// ═══════════════════════════════════════
// SECTION 2: RENDER — CEO 速查卡
// ═══════════════════════════════════════
function renderCEOCards() { ... }
function renderCompanyCard(card, index) { ... }
function renderSummaryCard(card, index) { ... }
function renderHostCard(card, index) { ... }
function initCardDots() { ... }

// ═══════════════════════════════════════
// SECTION 3: RENDER — WFIT 矩阵
// ═══════════════════════════════════════
function renderWFITTable() { ... }  // CTO R3: 去掉 sortTable，数据已预排序

// ═══════════════════════════════════════
// SECTION 4: RENDER — 完整情报卡
// ═══════════════════════════════════════
function renderIntelCards() { ... }
function renderSingleIntelCard(company) { ... }
function filterCards(query) { ... }
function expandAll() { ... }
function collapseAll() { ... }

// ═══════════════════════════════════════
// SECTION 5: CHECKLIST + localStorage
// ═══════════════════════════════════════
function initChecklist() { ... }
function saveCheck(key, checked) { ... }
function isChecked(key) { ... }
function updateProgress() { ... }
function resetChecklist() { ... }
function printChecklist() { ... }

// ═══════════════════════════════════════
// SECTION 6: NAVIGATION + INTERACTION
// ═══════════════════════════════════════
function initNavObserver() { ... }
function showToast(msg) { ... }
function copyTemplate(level) { ... }

// ═══════════════════════════════════════
// SECTION 7: INIT  (CTO R3: 去掉 initTabs)
// ═══════════════════════════════════════
document.addEventListener('DOMContentLoaded', () => {
  renderCEOCards();
  renderWFITTable();
  renderIntelCards();
  initChecklist();
  initNavObserver();
});
```

---

## 十、三轮自我对抗迭代

### Round 1: "破坏者" — 离线环境下图片加载？大屏/小屏切换？打印断页？

**攻击 1: 图片/图标在离线环境下加载失败？**

> 分析: PRD 已明确排除参会者照片。方案中不使用任何外部图片、图标字体或 CDN 资源。所有视觉元素使用 CSS 生成 (伪元素、border、gradient) 和 Unicode 符号 (✦ ⚠ ▸ ● ○)。
>
> **结论: 不存在此风险。** socialhub-overview.html 中的 nav-logo 使用了外部 URL，本方案不会引入此类依赖。所有装饰性元素纯 CSS 实现。

**攻击 2: 大屏/小屏切换时布局是否崩溃？**

> 测试场景: CEO 在手机上看速查卡(375px)，GTM 在笔记本上看情报卡(1440px)，投影展示(1920px)。
>
> **发现的问题**:
> - CEO 速查卡在桌面端不应该显示为全屏宽卡片，否则信息密度过低
> - WFIT 表格在 375px 宽度下会水平溢出
>
> **修正**:
> - 速查卡桌面端 `max-width: 420px`，居中展示，可见 2-3 张卡的边缘暗示滑动
> - WFIT 表格外层增加 `overflow-x: auto` wrapper，手机端可横滚
> - 已在 Section 六 (响应式设计) 中体现修正

**攻击 3: 打印时折叠内容丢失？分页断在卡片中间？**

> **发现的问题**:
> - `<details>` 未展开时，子内容在 DOM 中存在但隐藏。`@media print` 中需要强制覆盖 display
> - 情报卡包含四层嵌套 `<details>`，打印时全部展开可能导致单张卡跨页
>
> **修正**:
> - 打印样式中 `details:not([open]) > *:not(summary) { display: block !important; }` 强制展开
> - 每张情报卡增加 `page-break-inside: avoid`
> - 当内容超过一页时，允许在 L3/L4 层之间断页 (用 `page-break-before: auto` 在 layer 级别)
> - 已在 Section 七 (打印优化) 中体现修正

**攻击 4: localStorage 在隐私模式下不可用？**

> Safari 隐私模式下 localStorage 可用但容量为 0 (旧版本) 或正常可用 (iOS 15+)。
>
> **修正**: 在 `saveCheck` 中增加 try/catch:
>
> ```javascript
> function saveCheck(key, checked) {
>   try {
>     const state = JSON.parse(localStorage.getItem('checklist-state') || '{}');
>     state[key] = checked;
>     localStorage.setItem('checklist-state', JSON.stringify(state));
>   } catch (e) {
>     // 隐私模式或存储已满: 静默降级，勾选仅当前会话有效
>   }
>   updateProgress();
> }
> ```

---

### Round 2: "设计一致性" — 与 socialhub.ai 设计语言的对齐度？配色/字体/间距？

**检查 1: 配色是否完全对齐？**

> - 品牌色 (`--navy`, `--cyan`, `--purple`, `--blue`) 直接复用 socialhub-overview.html 的 CSS 变量值
> - 新增业务专属色 (`--p0` ~ `--partner`) 来自 PRD NFR-06，与品牌色不冲突
> - Hero 渐变方向与 socialhub-overview.html 一致: `linear-gradient(135deg, #0a1128, var(--navy), #1a3a6e)`
>
> **结论: 对齐。** P1-watch (#F59E0B 橙黄色) 需要与 P1 (#EA580C 橙色) 有足够区分度，已确认。

**检查 2: 字体栈是否一致？**

> - 完全复用 socialhub-overview.html 的字体栈: `-apple-system, "Segoe UI", Arial, "PingFang SC", "Microsoft YaHei", sans-serif`
> - 中文渲染: PingFang SC (macOS/iOS) + Microsoft YaHei (Windows)
> - 行高: 正文 1.7 与参考文件一致
>
> **结论: 对齐。**

**检查 3: 间距和圆角是否一致？**

> - socialhub-overview.html 使用 `border-radius: 12px` (卡片)、`padding: 32px` (卡片内边距)
> - 本方案统一为 8px 基准间距系统，圆角 8/12/16px 三档
> - CEO 速查卡用 16px 圆角 (比标准卡片更圆，强调手感)
>
> **结论: 基本对齐，CEO 速查卡略有差异但有合理理由。**

**检查 4: 导航栏风格是否一致？**

> - socialhub-overview.html: `background: rgba(18,28,61,0.95)` + `backdrop-filter: blur(12px)` + 64px 高
> - 本方案: 相同背景色和模糊，高度缩至 56px (手机端更紧凑)
> - 导航项: 参考文件为文字链接，本方案为按钮式 (因为是锚点导航而非页面跳转)
>
> **结论: 风格一致，形态因场景不同做了合理调整。**

---

### Round 3: "新入职工程师" — 开发步骤清晰吗？一个人 6-8 小时能完成吗？

**工时估算 (CTO 修正 R1-3: 原始估算偏乐观)**:

> **CTO 注**: 原估算 9.5h 优化到 7.5h 的思路正确，但"数据填充 2h"严重低估。
> 11 家公司的深度内容需要从调研文档中提炼并格式化为 JSON，实际耗时 3-4h。
> 修正方案: 数据填充由 AI 辅助批量生成 JSON，人工审核修正，压缩到 1.5h。

| 步骤 | 任务 | 预估工时 | 依赖 |
|---|---|---|---|
| 1 | 搭建 HTML 骨架 + CSS 设计系统 + 导航栏 + Hero | 1h | 无 |
| 2 | CEO 速查卡组件 (scroll-snap + 渲染) | 0.75h | 步骤 1 |
| 3 | WFIT 矩阵 (纵向排列，不做 Tab 切换，不做列排序) | 0.5h | 步骤 1 |
| 4 | 完整情报卡 (details 嵌套 + 四层折叠 + 搜索) | 1.25h | 步骤 1 |
| 5 | Demo 策略表 + 应对策略 + 感谢邮件 (全部静态 HTML) | 0.5h | 步骤 1 |
| 6 | Checklist (localStorage + 进度 + 重置) | 0.75h | 步骤 1 |
| 7 | 数据填充 (AI 生成 JSON + 人工审核) | 1.5h | 步骤 4 |
| 8 | 响应式调试 + 打印优化 + 离线测试 | 1h | 步骤 2-7 |
| | **总计** | **7.25h** | |

**已采纳的削减** (从原始 9.5h 削减):

| 削减项 | 节省 | 理由 |
|---|---|---|
| Tab 切换改为纵向排列 | -0.5h | 去掉 Tab JS 逻辑，YAGNI |
| WFIT 去掉列排序 | -0.5h | 默认按总分降序即可 |
| 邮件复制改为选中文本 | -0.25h | file:// 协议下 Clipboard API 不可用 (R1-2) |
| 圆点指示器去掉点击跳转 | -0.25h | scroll-snap 手势已足够 |
| 数据填充由 AI 辅助 | -0.75h | 从调研文档批量生成 JSON 结构 |

**开发顺序建议 (CTO 修正: 关键路径优先)**:

```
Phase A: 骨架 + 数据 (2h) — 可并行
  A1. 创建 HTML 文件，写入 CSS 变量和基础样式 + 8 个 section 空壳 + 导航栏 (1h)
  A2. [并行] AI 辅助生成 11 家公司 JSON 数据，人工审核 (1h，与 A1 并行)

Phase B: 核心组件 (2.75h)
  B1. CEO 速查卡渲染 + scroll-snap (0.75h)
  B2. 完整情报卡渲染 + 四层折叠 + 搜索 (1.25h)
  B3. Checklist + localStorage (0.75h)

Phase C: 补充组件 (1h)
  C1. WFIT 表格 + 切入路径 + 信号判读 (纵向排列) (0.5h)
  C2. Demo 策略 + 应对策略 + 邮件模板 (静态 HTML) (0.5h)

Phase D: 收尾 + CEO 验收 (1.5h)
  D1. 人工审核/修正数据 (0.5h，A2 审核剩余部分)
  D2. 响应式调试 (375px / 768px / 1024px) (0.5h)
  D3. 打印测试 + 离线飞行模式测试 (0.25h)
  D4. CEO 5 分钟速查卡体验测试 + 紧急修正缓冲 (0.25h)
```

**CTO 增加: D-2 交付检查点**

> 活动在 5/22，代码必须在 5/20 晚完成，5/21 白天给 CEO 做验收+修正。
> 不允许 5/21 晚上还在写代码。如果 5/20 晚代码未完成，启动降级方案:
> 砍掉情报卡搜索功能、砍掉打印优化、Checklist 改为静态 HTML 不做持久化。

---

## 十一、验收对照表

对照 PRD Section 九 的 10 条验收标准:

| # | PRD 验收标准 | 技术实现保障 |
|---|---|---|
| 1 | CEO 速查卡 iPhone 14 Pro 单手可操作 | scroll-snap + 44px 最小触摸目标 + `touch-action: manipulation` |
| 2 | 单张速查卡一屏展示 | `min-height: 380px` + 内容精简为 4 字段 + 375px 实测 |
| 3 | 情报卡折叠状态 11 家一屏可见 | 每张折叠高度 ~56px，11 张 = ~616px + 导航区 < 900px (桌面可见) |
| 4 | 打开到找到目标 < 10 秒 | 固定导航锚点跳转 + 搜索筛选 + IntersectionObserver 高亮 |
| 5 | Checklist 双面 A4 | ~40 项 + 12 标题 = ~52 行，10pt 单面 A4 可容纳 ~55 行 |
| 6 | 离线完整可用 | 零外部依赖，所有资源内联 |
| 7 | 优先级色彩日光下可辨 | P0=#DC2626, P1=#EA580C, P2=#2563EB 均为高饱和度色 |
| 8 | 东道主职责位于 Checklist 最顶部 | `phase: "host"` 排序为第一组 |
| 9 | 速查卡底部固定统一叙事 | 每张卡模板尾部包含 `unifiedNarrative` 渲染 |
| 10 | 应对策略 5 秒内找到 | 导航栏包含"应对策略"按钮 + 锚点跳转 |

---

## 十二、风险与缓解 (CTO 修正 R2: 生产事故回溯视角)

> **CTO 场景设定**: CEO 在活动前 30 分钟打开手机浏览器，页面出问题。没有工程师在场。
> 以下按"出事概率 x 影响"排序，增加 R2 新发现的风险。

| # | 风险 | 概率 | 影响 | 缓解 |
|---|---|---|---|---|
| **R2-1** | **CEO 手机上 JS 执行失败，页面一片空白** | 中 | **致命** | **新增渐进增强策略**: 所有 CEO 速查卡同时写一份静态 HTML 骨架在 `<noscript>` 内。即使 JS 全挂，CEO 仍能看到纯文本版速查卡。详见下方 R2-1 修正。 |
| **R2-2** | **文件传输方式出错: 微信/邮件传文件改了扩展名** | 中 | **致命** | 交付时附带 3 种方式: (1) 邮件附件 .html (2) AirDrop/蓝牙直传 (3) 预先存到 CEO 手机上验证打开。**5/21 必须在 CEO 实际手机上完成一次完整打开测试。** |
| **R2-3** | **iOS Safari 字体渲染: 中英文混排行高异常** | 中 | 中 | CSS 增加 `-webkit-text-size-adjust: 100%`，body 增加 `font-size: 16px` (避免 iOS 自动缩放)。开发阶段必须在真机 Safari 测试。 |
| R2-4 | 11 家公司数据填充耗时超预期 | 高 | 延迟交付 | 先填充 P0+P1 (5 家)，P2/P3 可简写。AI 辅助生成。 |
| R2-5 | CEO 反馈速查卡信息不够/太多 | 中 | 返工 | 先出 1 张卡原型给 CEO 确认，再批量产出 |
| R2-6 | Safari/iOS 的 scroll-snap 行为不一致 | 低 | 滑动体验差 | 标准 CSS 属性，iOS 13+ 支持良好 |
| R2-7 | localStorage 被清空 | 低 | Checklist 丢失 | 接受风险 |
| R2-8 | 打印时配色不正确 | 中 | 可读性降 | `print-color-adjust: exact` + 打印预览 |

### R2-1 修正: 渐进增强 — noscript 静态降级

```html
<!-- 在 CEO 速查卡 section 内增加 noscript 降级 -->
<noscript>
  <div class="noscript-cards">
    <h2>CEO 速查卡 (静态版)</h2>
    <div class="card-static">
      <h3>Canada Goose [P0] — Johnney Cao</h3>
      <p><strong>在招 VP AI，D365 核心，CDP 空白</strong></p>
      <p>开场: D365 在 DTC 场景的数据统一</p>
      <p>目标: 约 D365 集成 demo</p>
      <p style="color:#DC2626">⚠ 别暗示 D365 不够用</p>
    </div>
    <!-- 其余 6 张卡同样写死 -->
  </div>
</noscript>
```

> **CTO 要求**: 这不是可选优化，是 P0 必做项。CEO 手机如果 JS 出任何问题，
> 静态版速查卡是最后的安全网。实现成本 < 15 分钟 (直接复制数据写 HTML)。

---

## 十三、MVP 明确不做

| 项 | 理由 | 迭代时机 |
|---|---|---|
| SVG 气泡图 | 开发耗时 ~2h，表格已足够 | V1.1 |
| 情报卡内搜索高亮 | 浏览器原生 Ctrl+F 已够用 | V1.1 |
| 暗色模式 | 非核心需求 | V2 |
| 动画/过渡效果 | 影响性能和开发时间 | V1.1 |
| 国际化 | 全中文，用户群固定 | 无需求 |
| Service Worker 离线缓存 | 单文件已离线可用，不需要 SW | 无需求 |

---

## 附录 A: 完整 HTML 文件骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>参会公司画像 — Socialhub.AI x Microsoft Seattle 5/22</title>
  <style>
    /* ── CSS VARIABLES ── */
    :root { /* 见 Section 五 设计系统 */ }

    /* ── RESET ── */
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: var(--font); color: var(--ink); background: var(--white); line-height: 1.7; font-size: 16px; -webkit-text-size-adjust: 100%; } /* CTO R2-3: 防 iOS 自动缩放 */

    /* ── NAV ── */
    .nav { /* 见 Section 四 导航系统 */ }

    /* ── HERO ── */
    .hero { /* 见 Section 五 Hero 区域 */ }

    /* ── CEO CARDS ── */
    .card-carousel { /* 见 Section 三 S2 */ }
    .ceo-card { /* 见 Section 三 S2 */ }

    /* ── WFIT TABLE ── */
    .wfit-table { /* 标准表格样式 */ }

    /* ── INTEL CARDS ── */
    .intel-card { /* 见 Section 三 S4 */ }
    .intel-layer { /* 嵌套折叠 */ }

    /* ── CHECKLIST ── */
    .ck-phase { /* 阶段分组 */ }
    .ck-item { /* 单项 */ }

    /* ── RESPONSE CARDS ── */
    .response-card { /* if-then 卡片 */ }

    /* ── EMAIL TEMPLATES ── */
    .email-tpl { /* 邮件模板卡 */ }

    /* ── UTILITIES ── */
    .tier-badge { /* 优先级标签 */ }
    .toast { /* 提示气泡 */ }

    /* ── RESPONSIVE ── */
    @media (min-width: 768px) { /* 平板 */ }
    @media (min-width: 1024px) { /* 桌面 */ }

    /* ── PRINT ── */
    @media print { /* 见 Section 七 */ }
  </style>
</head>
<body>

  <!-- NAV -->
  <nav class="nav">
    <button class="nav-btn active" data-target="ceo-cards">速查卡</button>
    <button class="nav-btn" data-target="priority">优先级</button>
    <button class="nav-btn" data-target="intel-cards">情报卡</button>
    <button class="nav-btn" data-target="demo">Demo</button>
    <button class="nav-btn" data-target="checklist">Checklist</button>
    <button class="nav-btn" data-target="response">应对策略</button>
    <button class="nav-btn" data-target="email">邮件模板</button>
  </nav>

  <!-- HERO -->
  <section class="hero">
    <h1>Socialhub.AI x <span>Microsoft</span></h1>
    <p class="subtitle">Seattle 5/22 — 参会公司深度画像与活动执行系统</p>
    <div class="countdown" id="countdown"></div>
  </section>

  <!-- S2: CEO 速查卡 -->
  <section id="ceo-cards" class="section">
    <h2>CEO 速查卡</h2>
    <div class="card-carousel"></div>
    <div class="card-dots"></div>
  </section>

  <!-- S3: WFIT 优先级矩阵 -->
  <section id="priority" class="section section-alt">
    <h2>合作机会优先级</h2>
    <h3>WFIT 评分矩阵</h3>
    <div class="table-wrapper"><table class="wfit-table"></table></div>
    <h3>切入路径</h3>
    <div id="pathways"></div>
    <h3>信号判读指南</h3>
    <div id="signals"></div>
  </section>

  <!-- S4: 完整情报卡 -->
  <section id="intel-cards" class="section">
    <h2>完整公司情报卡</h2>
    <div class="intel-controls">
      <input type="search" placeholder="搜索公司/人名..." oninput="filterCards(this.value)" />
      <button onclick="expandAll()">全部展开</button>
      <button onclick="collapseAll()">全部折叠</button>
    </div>
    <div id="intel-list"></div>
  </section>

  <!-- S5: Demo 策略 -->
  <section id="demo" class="section section-alt">
    <h2>Demo 策略速查</h2>
    <!-- 静态 HTML 表格 -->
  </section>

  <!-- S6: Checklist -->
  <section id="checklist" class="section">
    <h2>活动执行 Checklist <span id="ck-progress"></span></h2>
    <div class="ck-actions">
      <button onclick="printChecklist()">打印 Checklist</button>
      <button onclick="resetChecklist()" class="ck-reset-btn">重置勾选</button>
    </div>
    <div id="ck-list"></div>
  </section>

  <!-- S7: 应对策略 -->
  <section id="response" class="section section-alt">
    <h2>CEO 应对策略</h2>
    <!-- 静态 HTML: 5 张 if-then 卡 -->
  </section>

  <!-- S8: 感谢邮件 -->
  <section id="email" class="section">
    <h2>感谢邮件模板</h2>
    <!-- 3 档模板 -->
  </section>

  <!-- FOOTER -->
  <footer class="footer">
    <p>Socialhub.AI — 内部机密 | 生成于 2026-05-18 | V1.0 MVP</p>
  </footer>

  <script>
    // ═══ DATA MODEL ═══
    const COMPANIES = [/* 11 家公司 */];
    const CEO_CARDS = [/* 7 张 */];
    const WFIT_SCORES = [/* 10 行 */];
    const DEMO_MODULES = [/* 5 个 */];
    const CHECKLIST_ITEMS = [/* ~40 项 */];
    const EMAIL_TEMPLATES = [/* 3 档 */];
    const RESPONSE_CARDS = [/* 5 条 */];

    // ═══ RENDER FUNCTIONS ═══
    // (见 Section 九 JS 模块划分)

    // ═══ INIT ═══
    document.addEventListener('DOMContentLoaded', () => {
      renderCEOCards();
      renderWFITTable();
      renderIntelCards();
      initChecklist();
      initNavObserver();
    });
  </script>
</body>
</html>
```
