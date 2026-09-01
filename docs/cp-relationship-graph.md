# KOF大粉模拟器 — 全30人角色关系图谱

> 以下为 Mermaid 格式的关系图，支持在 Markdown 预览器、VS Code Mermaid 插件、或 [Mermaid Live Editor](https://mermaid.live) 中渲染。

---

## 图谱 1：CP 关系全览（按纪元分区）

```mermaid
graph LR
    %% ===== C 纪元 =====
    subgraph CE["C 纪元 · 冷峻制服系（10人）"]
        direction TB
        C01["Leon<br/>🎭 92/85/78<br/>⭐本命 · A级"]
        C02["Miles<br/>🎭 70/18/35<br/>B级"]
        C03["Clark<br/>🎭 58/50/28<br/>🔒赎罪 · F级"]
        C04["Hugo<br/>🎭 88/25/55<br/>A级"]
        C05["Luke<br/>🎭 55/60/72<br/>B级"]
        C06["Mark<br/>🎭 62/45/50<br/>C级"]
        C07["Shaw<br/>🎭 45/75/68<br/>B级"]
        C08["Wade<br/>🎭 35/35/45<br/>C级"]
        C09["Penn<br/>🎭 50/30/22<br/>D级"]
        C10["Jean<br/>🎭 68/42/55<br/>C级"]
    end

    %% ===== K 纪元 =====
    subgraph KE["K 纪元 · 情绪特写系（10人）"]
        direction TB
        K01["Wyatt<br/>🎭 65/88/42<br/>🔒赎罪 · A级"]
        K02["Sven<br/>🎭 80/82/76<br/>⭐本命 · B级"]
        K03["Jade<br/>🎭 38/62/15<br/>🔒赎罪 · F级"]
        K04["Ruby<br/>🎭 72/58/52<br/>B级"]
        K05["Lily<br/>🎭 50/65/38<br/>C级"]
        K06["Chloe<br/>🎭 60/85/48<br/>A级"]
        K07["Sia<br/>🎭 42/48/28<br/>D级"]
        K08["Vera<br/>🎭 65/35/70<br/>C级"]
        K09["Sam<br/>🎭 30/40/25<br/>E级"]
        K10["Simon<br/>🎭 78/62/48<br/>B级"]
    end

    %% ===== J 纪元 =====
    subgraph JE["J 纪元 · 沉默氛围系（6人）"]
        direction TB
        J01["Ian<br/>🎭 75/70/88<br/>⭐本命 · C级"]
        J02["Orion<br/>🎭 48/55/68<br/>🔒赎罪 · F级"]
        J03["Cole<br/>🎭 70/62/78<br/>C级"]
        J04["Bo<br/>🎭 40/45/60<br/>D级"]
        J05["Blair<br/>🎭 35/50/42<br/>E级"]
        J06["Wren<br/>🎭 55/68/45<br/>C级"]
    end

    %% ===== T 纪元 =====
    subgraph TE["T 纪元 · 极端光反差系（4人）"]
        direction TB
        T01["Kai<br/>🎭 90/12/20<br/>🔒赎罪 · D级"]
        T02["Milo<br/>🎭 85/80/82<br/>🔒赎罪⭐本命 · F级"]
        T03["Ash<br/>🎭 82/78/75<br/>⭐本命 · B级"]
        T04["Blaze<br/>🎭 25/72/55<br/>E级"]
    end

    %% ===== CP 关系线 =====
    %% C纪元内部
    C01 -->|"78"| C02
    C04 -->|"55"| C06
    C05 <-->|"72/68"| C07
    C08 -->|"45"| C04

    %% K纪元内部
    K01 -->|"42"| K02
    K02 -->|"76"| K01
    K04 -->|"52"| K10
    K06 -->|"48"| K01
    K08 -.->|"70 跨纪元"| K02

    %% J纪元内部
    J01 -->|"88"| J02
    J03 -->|"78"| J01
    J04 -->|"60"| J03

    %% T纪元内部
    T02 -->|"82"| T01
    T03 -->|"75"| T02

    %% 跨纪元 CP
    J02 -.->|"68 跨纪元"| T01
    K08 -.->|"70 跨纪元"| K02

    %% ===== 样式 =====
    %% 本命候选 - 金色边框
    style C01 fill:#1a1a2e,stroke:#FFD700,stroke-width:3px,color:#E0E0E0
    style K02 fill:#1a1a2e,stroke:#FFD700,stroke-width:3px,color:#E0E0E0
    style J01 fill:#1a1a2e,stroke:#FFD700,stroke-width:3px,color:#E0E0E0
    style T02 fill:#1a1a2e,stroke:#FFD700,stroke-width:3px,color:#E0E0E0
    style T03 fill:#1a1a2e,stroke:#FFD700,stroke-width:3px,color:#E0E0E0

    %% 赎罪者 - 红色边框
    style C03 fill:#1a1a2e,stroke:#FF4500,stroke-width:2px,color:#E0E0E0
    style K01 fill:#1a1a2e,stroke:#FF4500,stroke-width:2px,color:#E0E0E0
    style K03 fill:#1a1a2e,stroke:#FF4500,stroke-width:2px,color:#E0E0E0
    style J02 fill:#1a1a2e,stroke:#FF4500,stroke-width:2px,color:#E0E0E0
    style T01 fill:#1a1a2e,stroke:#FF4500,stroke-width:2px,color:#E0E0E0
    style T02 fill:#2a0a0a,stroke:#FF4500,stroke-width:3px,color:#FFD700

    %% 纪元子图样式
    style CE fill:#0a0a12,stroke:#4A4A4A,stroke-width:2px,color:#E0E0E0
    style KE fill:#120a0a,stroke:#8B7355,stroke-width:2px,color:#E0E0E0
    style JE fill:#0a120a,stroke:#3A5A4A,stroke-width:2px,color:#E0E0E0
    style TE fill:#1a0a0a,stroke:#FF4500,stroke-width:2px,color:#E0E0E0
```

---

## 图谱 2：CP 强度热力图（仅关系，按强度排序）

```mermaid
graph TD
    %% 按强度从高到低排列
    J01 -->|"88"| J02
    T02 -->|"82"| T01
    C01 -->|"78"| C02
    J03 -->|"78"| J01
    K02 -->|"76"| K01
    C05 -->|"72"| C07
    T03 -->|"75"| T02
    K08 -->|"70"| K02
    C07 -->|"68"| C05
    J02 -->|"68"| T01
    C04 -->|"55"| C06
    K04 -->|"52"| K10
    K06 -->|"48"| K01
    C08 -->|"45"| C04
    J04 -->|"60"| J03
    K01 -->|"42"| K02

    %% 强度样式
    linkStyle 0 stroke:#FF6B6B,stroke-width:4px
    linkStyle 1 stroke:#FF6B6B,stroke-width:4px
    linkStyle 2 stroke:#FFB347,stroke-width:3px
    linkStyle 3 stroke:#FFB347,stroke-width:3px
    linkStyle 4 stroke:#FFB347,stroke-width:3px
    linkStyle 5 stroke:#FFB347,stroke-width:3px
    linkStyle 6 stroke:#FFB347,stroke-width:3px
    linkStyle 7 stroke:#87CEEB,stroke-width:2px
    linkStyle 8 stroke:#87CEEB,stroke-width:2px
    linkStyle 9 stroke:#87CEEB,stroke-width:2px
    linkStyle 10 stroke:#908090,stroke-width:2px
    linkStyle 11 stroke:#908090,stroke-width:2px
    linkStyle 12 stroke:#908090,stroke-width:2px
    linkStyle 13 stroke:#908090,stroke-width:2px
    linkStyle 14 stroke:#908090,stroke-width:2px
    linkStyle 15 stroke:#908090,stroke-width:2px

    %% 节点样式
    style J01 fill:#FFD700,stroke:#FFD700,color:#000
    style J02 fill:#FF4500,stroke:#FF4500,color:#FFF
    style T02 fill:#FF4500,stroke:#FFD700,color:#FFF
    style T01 fill:#FF4500,stroke:#FF4500,color:#FFF
    style C01 fill:#FFD700,stroke:#FFD700,color:#000
    style K02 fill:#FFD700,stroke:#FFD700,color:#000
    style T03 fill:#FFD700,stroke:#FFD700,color:#000
    style K01 fill:#FF4500,stroke:#FF4500,color:#FFF
```

---

## 图谱 3：半记忆脉冲 × 真心值象限图

```mermaid
quadrantChart
    title "半记忆脉冲强度 vs 真心值（三轴均值）"
    x-axis "低真心值" --> "高真心值"
    y-axis "弱脉冲" --> "极强脉冲"
    quadrant-1 "高危区：脉冲强+真心高<br/>（假阳性·需要克服恐惧）"
    quadrant-2 "危险区：脉冲强+真心低<br/>（真危险·信任直觉）"
    quadrant-3 "安全区：脉冲弱+真心低<br/>（不值得投资）"
    quadrant-4 "本命区：脉冲弱+真心高<br/>（安全投资）"
    "Milo": [0.82, 0.80]
    "Ash": [0.78, 0.75]
    "Blaze": [0.51, 0.70]
    "Wyatt": [0.65, 0.95]
    "Kai": [0.41, 0.95]
    "Miles": [0.41, 0.60]
    "Clark": [0.45, 0.50]
    "Orion": [0.57, 0.55]
    "Hugo": [0.56, 0.60]
    "Ruby": [0.61, 0.55]
    "Chloe": [0.64, 0.70]
    "Shaw": [0.63, 0.40]
    "Penn": [0.34, 0.35]
    "Jean": [0.55, 0.35]
    "Sia": [0.39, 0.30]
    "Wren": [0.56, 0.35]
    "Leon": [0.85, 0.05]
    "Sven": [0.79, 0.05]
    "Ian": [0.78, 0.05]
    "Luke": [0.62, 0.05]
    "Mark": [0.52, 0.05]
    "Wade": [0.38, 0.05]
    "Lily": [0.51, 0.05]
    "Vera": [0.57, 0.05]
    "Sam": [0.32, 0.05]
    "Simon": [0.63, 0.05]
    "Cole": [0.70, 0.05]
    "Bo": [0.48, 0.05]
    "Blair": [0.42, 0.05]
```

---

## 图例说明

| 标记 | 含义 |
|------|------|
| 🎙️ 三轴值 | 舞台热忱 / 粉丝回馈 / CP羁绊 |
| ⭐ | 本命候选（三轴全 > 60） |
| 🔒 | 堕落赎罪者（F级起步 · 加倍练习） |
| ⭐🔒 | 赎罪者 + 本命候选（Milo专属） |
| 金色边框 | 本命候选 |
| 红色边框 | 赎罪者 |
| 实线箭头 | 纪元内 CP |
| 虚线箭头 | 跨纪元 CP |
| ↔ 双向箭头 | 双向 CP（Luke↔Shaw） |

---

## CP 关系统计

| 指标 | 数值 |
|------|------|
| CP 总数 | 16 条 |
| 双向 CP | 1 对（Luke↔Shaw 72/68） |
| 跨纪元 CP | 3 条（Orion→Kai、Vera→Sven、Ash→Milo） |
| 最强 CP | Ian→Orion（88） |
| 最弱 CP | Wyatt→Sven（42，假演利用） |
| 最不对等 CP | Milo→Kai（82/20） |
| 最核心 CP 线 | Milo→Kai（两个赎罪者的羁绊） |
