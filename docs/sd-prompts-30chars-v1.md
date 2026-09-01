# KOF大粉模拟器 — 30角色 SD 提示词 Brief v1.0

> **文档代号**：SD-PROMPT-V1
> **编写者**：Agent B（美术/视觉）
> **监制**：人类制作人
> **日期**：2026-08-06
> **状态**：待审核
> **依赖**：characters-core-10.md、characters-remaining-20.md、sd-model-selection-v1.md
> **底模**：DreamShaper XL v21
> **画风LoRA**：kof_webtoon_dark_v1（权重 0.6-0.8）

---

## 1. 通用画风关键词

所有角色生成均使用以下通用画风前缀：

```
korean webtoon style, dark psychological, clean linework, semi-realistic,
desaturated color palette, 7.5 head body ratio, cel shading with soft gradient,
halftone texture overlay, sharp directional lighting, deep shadows,
minimalist background, cinematic composition, high contrast
```

通用负面提示词：

```
anime style, chibi, big eyes, colorful, bright, 3d render, photorealistic,
watermark, signature, text, low quality, blurry, deformed, extra limbs,
bad anatomy, bad hands, fused fingers
```

---

## 2. 四纪元差异化 Prompt 模板

### 2.1 C 纪元（冷峻制服系）

**SD端生成参数**（中性基础图）：

```
Positive: {通用画风} + cold tone, steel gray palette, desaturated 85%+,
stiff posture, formal uniform, high collar, sharp features,
single directional parallel light, hard edge shadows,
clean minimalist background, industrial atmosphere

Negative: warm colors, soft lighting, casual clothing, smile, emotional expression

CFG: 7.0 | Steps: 30 | Sampler: DPM++ 2M Karras | LoRA权重: 0.7
```

**Shader端处理**：
- 色板映射 → 钢灰/冷白/炭黑
- 半色调网点叠加 40-50 LPI, opacity 35%
- 对比度提升 +20%, 饱和度降至 <15%

### 2.2 K 纪元（情绪特写系）

**SD端生成参数**：

```
Positive: {通用画风} + warm tone, muted amber palette, desaturated 65%,
soft side lighting, gentle gradient shadows, emotional expression,
layered clothing, knit fabric texture, blush detail,
intimate framing, shallow depth of field

Negative: cold colors, hard shadows, rigid posture, industrial atmosphere

CFG: 6.5 | Steps: 28 | Sampler: DPM++ 2M Karras | LoRA权重: 0.65
```

**Shader端处理**：
- 色板映射 → 米色/暗金/暖灰
- 网点叠加 15 LPI, opacity 15%
- 柔光 filter, 暖色偏移 +10

### 2.3 J 纪元（沉默氛围系）

**SD端生成参数**：

```
Positive: {通用画风} + neutral cool tone, fog gray palette, desaturated 80%,
curtain diffused light, soft blurred light spots, minimal composition,
extreme negative space, figure placed at frame edge,
delicate hand gesture detail, quiet atmosphere, slow rhythm

Negative: strong contrast, dramatic lighting, central composition, busy background

CFG: 7.0 | Steps: 32 | Sampler: DPM++ SDE Karras | LoRA权重: 0.7
```

**Shader端处理**：
- 色板映射 → 雾灰/旧白/墨绿
- 网点叠加 5 LPI, opacity 8%
- 留白 mask（画面边缘 30% 区域降低饱和度）

### 2.4 T 纪元（极端光反差系）

**SD端生成参数**：

```
Positive: {通用画风} + extreme contrast, pure black and white,
no midtones, split lighting, rim light, hard top light,
broken composition, fractured visual elements,
high saturation accent points, dangerous atmosphere

Negative: soft lighting, gradient shadows, warm tones, calm, peaceful

CFG: 8.0 | Steps: 30 | Sampler: Euler a | LoRA权重: 0.8
```

**Shader端处理**：
- 色板映射 → 纯黑/纯白/高饱和点缀
- 无网点
- 对比度拉满, 裂面着色（鼻梁为界左右明暗分割）

---

## 3. 30角色 SD 提示词

### 提示词结构

每个角色的 prompt 遵循以下结构：

```
{角色触发词}, {通用画风}, {纪元关键词}, {外貌描述}, {服装}, {表情}, {构图}
```

角色触发词格式：`kof_char_XX`（XX = c01-c10, k01-k10, j01-j06, t01-t04）

---

### C 纪元 10人

#### char_c01 — Leon

```
kof_char_c01, 1boy, tall slim build 185cm, slicked back black short hair,
pale gray-blue eyes, sharp jawline, impeccable black uniform,
high collar buttoned to top, long pianist fingers,
neutral cold expression, looking at viewer,
half body shot, centered composition, practice room background
```

#### char_c02 — Miles

```
kof_char_c02, 1boy, slim build 180cm, medium length light gray hair,
bangs covering right eye, porcelain-like flawless face, no warmth,
white uniform with silver pen in chest pocket,
standard smile (slightly mechanical, same angle every time),
half body shot, studio background
```

#### char_c03 — Clark

```
kof_char_c03, 1boy, thin build 178cm, dark gray short hair slightly long at back,
dark circles under eyes, tired cold expression,
oversized black uniform like wearing someone else's clothes,
exhausted look, looking down,
full body shot, corridor background, dim lighting
```

#### char_c04 — Hugo

```
kof_char_c04, 1boy, tall 188cm, silver gray short hair perfectly trimmed,
very pale gray almost transparent eyes, rigid straight posture,
perfectly tailored black uniform, ruler-like stance,
intense focused expression ignoring camera,
half body shot, practice room with stage lights
```

#### char_c05 — Luke

```
kof_char_c05, 1boy, slender 177cm, deep black short hair with cowlick at back,
warm deep brown eyes, standard black uniform with top button undone,
quiet observant expression, glancing sideways,
half body shot, practice room background
```

#### char_c06 — Mark

```
kof_char_c06, 1boy, average build 180cm, standard gray-black short hair,
ordinary forgettable face, standard black uniform no accessories,
holding folded white handkerchief, neutral expression,
half body shot, neutral background
```

#### char_c07 — Shaw

```
kof_char_c07, 1boy, 174cm shortest in C era, warm brown short hair,
genuine smile with real wrinkles around eyes,
black uniform with small fan gifts in pocket,
warm approachable expression, looking at viewer with slight smile,
half body shot, fan meeting background
```

#### char_c08 — Wade

```
kof_char_c08, 1boy, small thin 175cm, gray-black long bangs covering eyes,
forgettable features, loose black uniform like wearing brother's clothes,
receding into background, timid posture,
full body shot, edge of group, dim corner
```

#### char_c09 — Penn

```
kof_char_c09, 1boy, 182cm, once handsome but worn down,
messy dark gray medium hair with oily texture, heavy dark circles,
wrinkled unironed black uniform, messy collar,
hollow tired expression, looking away,
half body shot, practice room corner, depressing atmosphere
```

#### char_c10 — Jean

```
kof_char_c10, 1girl, 170cm, silver gray ear-length bob cut, sharp and clean,
high cheekbones, thin lips, narrow elongated eyes,
modified female black-white-gray uniform with silver collar pin,
efficient no-nonsense expression, looking at viewer,
half body shot, practice room background
```

---

### K 纪元 10人

#### char_k01 — Wyatt

```
kof_char_k01, 1boy, 183cm, warm brown medium wavy hair honey-colored in light,
amber eyes crescent-moon smile, beige knit sweater warm gray coat,
old ring on left ring finger,
perfect warm smile, looking at viewer,
half body shot, soft warm lighting, fan meeting background
```

#### char_k02 — Sven

```
kof_char_k02, 1boy, slender 176cm, black straight short hair to ear,
deep brown calm melancholic eyes, dark gray oversized hoodie sleeves past wrists,
quiet expression, not smiling not frowning,
half body shot, corner of practice room, warm but subdued lighting
```

#### char_k03 — Jade

```
kof_char_k03, 1girl, small frame 172cm, warm orange hair in low ponytail with loose strands,
small tiger tooth when smiling, fine lines under eyes from exhaustion,
bright warm colored outfit trying to look energetic,
forced too-wide smile, anxious energy,
half body shot, practice room, bright lighting
```

#### char_k04 — Ruby

```
kof_char_k04, 1girl, petite 165cm, warm golden long wavy hair like halo,
large amber eyes thick lashes, silk warm tone top,
excessive jewelry three necklaces two bracelets,
radiant stage smile, spotlight composition,
half body shot, stage performance, bright spotlight
```

#### char_k05 — Lily

```
kof_char_k05, 1girl, 168cm, light brown short hair to chin no styling,
deep brown eyes neutral unexpressive face, beige cotton t-shirt old jeans canvas shoes,
plain unremarkable appearance, natural unstaged expression,
half body shot, casual practice room, flat lighting
```

#### char_k06 — Chloe

```
kof_char_k06, 1girl, 172cm, dark chestnut long wavy hair with warm orange ombre ends,
large eyes with genuine slightly varied smile curve,
ruffle blouse A-line skirt ribbon hair accessory, youthful appearance,
eager warm smile, looking at viewer,
half body shot, fan meeting, warm lighting
```

#### char_k07 — Sia

```
kof_char_k07, 1girl, petite round 160cm, peach pink twin tails shoujo manga style,
round eyes dimples baby fat, pink series ribbon lace outfit,
nail bite marks on fingers, emotionally unstable expression,
half body shot, practice room, fluctuating lighting
```

#### char_k08 — Vera

```
kof_char_k08, 1girl, 170cm, slender angular frame, black long straight hair in low ponytail,
sharp dark eyes observant gaze, pure black turtleneck dark gray pants,
minimalist K-era outfit worn like C-era cold, no jewelry,
sharp analytical expression, looking past camera,
half body shot, backstage corridor, mixed lighting
```

#### char_k09 — Sam

```
kof_char_k09, 1boy, 173cm, thin hunched back, light brown medium greasy hair,
apologetic face, warm colored oversized clothes like clothes eating him,
sad tired expression, looking down,
full body shot, back of practice room, dim lighting
```

#### char_k10 — Simon

```
kof_char_k10, 1boy, 181cm, slim balanced, dark brown side-parted neat hair,
gold-rimmed glasses scholarly look, solid color shirt fitted pants loafers,
minimalist clean outfit, calm analytical expression,
half body shot, practice room, even lighting
```

---

### J 纪元 6人

#### char_j01 — Ian

```
kof_char_j01, 1boy, slender fragile 175cm, black-gray silky straight hair to shoulder covering half face,
nearly black deep eyes, cotton-linen loose shirt old white pants,
fingertips touching nearby object, transparent quiet presence,
extreme negative space composition, figure at frame edge,
half body shot, window with curtain diffused light
```

#### char_j02 — Orion

```
kof_char_j02, 1boy, slender 177cm, light brown short hair with cowlick swirl at back,
pale gray-green eyes like water reflection, dark jacket over J-era outfit,
hands in pockets, restless quiet expression,
standing at boundary between light and shadow,
full body shot, transitional space between era zones
```

#### char_j03 — Cole

```
kof_char_j03, 1boy, slender upright 176cm, warm black short hair showing forehead,
warm deep brown eyes with temperature, cotton-linen shirt sleeves rolled to elbows,
old burn scar on wrist, stable candle-like presence,
calm warm expression with slight depth,
half body shot, table with soft light, quiet room
```

#### char_j04 — Bo

```
kof_char_j04, 1boy, thin fragile 172cm, grayish-white medium hair lightest in J-era,
pale gray drooping eyes dazed look, oversized J-era outfit sleeves past fingers,
following behind someone, dependent posture,
full body shot, behind another figure, curtain light
```

#### char_j05 — Blair

```
kof_char_j05, 1girl, fragile 168cm, light gray long hair covering half face,
very pale gray eyes like diluted ink, old white shirt gray skirt,
everything fading like sun-bleached photograph,
empty neutral expression, not sad not happy,
half body shot, large empty room, minimal furniture
```

#### char_j06 — Wren

```
kof_char_j06, 1girl, 170cm, gentle but resilient frame, dark green medium hair darkest in J-era,
deep ink-green eyes like deep water, J-era outfit with light scarf around neck,
listening posture slightly tilted head, attentive quiet expression,
half body shot, quiet corner of practice room, curtain light
```

---

### T 纪元 4人

#### char_t01 — Kai

```
kof_char_t01, 1boy, muscular 186cm, pure black short hair shaved sides top long like blade,
very pale gray almost white eyes, black leather jacket torn white t-shirt black pants combat boots,
faint scar on left wrist, split lighting cutting face in half,
intense burning expression with hollow flash in eyes,
half body shot, extreme top light + rim light, pure black background
```

#### char_t02 — Milo

```
kof_char_t02, 1boy, lean but strong 179cm, deep black medium hair bangs covering eyes,
dark brown eyes split by extreme lighting, black turtleneck dark gray pants studded black boots,
lowering head to avoid extreme light, quiet compliant expression,
most harmless looking in T-era, calm despite chaos,
half body shot, light-dark boundary, fractured background
```

#### char_t03 — Ash

```
kof_char_t03, 1boy, balanced 180cm, deep black short hair clean and neat,
dark amber eyes golden in light-dark boundary, black turtleneck dark gray pants black boots,
no studs no tears no metal, cleanest look in T-era,
calm warm expression, standing at light-shadow junction,
half body shot, split lighting but face naturally merging both sides
```

#### char_t04 — Blaze

```
kof_char_t04, 1boy, tall 185cm, loose and slouching like crooked tree,
flame red messy medium hair, pale green eyes like ghost fire,
black vest ripped jeans combat boots red scarf tied at waist,
chaotic outfit like exploded wardrobe, earnest but lost expression,
full body shot, backstage chaos, mixed extreme lighting
```

---

## 4. 服装变体提示词

每个角色需要 3 套服装：练习服 / 舞台服 / 便装。以 Leon (char_c01) 为例：

### 4.1 练习服

```
kof_char_c01, {通用画风}, practice uniform, simple white t-shirt, gray sweatpants,
no accessories, casual training outfit, practice room background,
natural lighting, mid-action stretching pose
```

### 4.2 舞台服

```
kof_char_c01, {通用画风}, stage costume, tailored black military-style jacket with silver epaulettes,
black slim pants, polished boots, stage makeup subtle,
dramatic stage lighting, spotlight from above, performance pose,
confident powerful expression
```

### 4.3 便装

```
kof_char_c01, {通用画风}, casual outfit, black turtleneck, dark gray overcoat,
black slim jeans, leather boots, minimal accessories,
walking in city street at dusk, natural expression,
half body shot, urban background, evening light
```

---

## 5. 微表情关键帧提示词

每个角色需要 6 种微表情关键帧。通用模板（以 Leon 为例）：

### F01 — Neutral

```
kof_char_c01, neutral expression, relaxed face, mouth closed,
eyes looking forward calmly, no emotion, resting state
```

### F02 — Genuine Smile

```
kof_char_c01, genuine subtle smile, eyes slightly narrowed,
pupils dilated naturally, mouth corners lifted 5 degrees,
crow's feet barely visible, micro expression of warmth
```

### F03 — Fake Smile

```
kof_char_c01, fake smile, mouth corners lifted mechanically,
eyes not engaged, pupils not dilated, no crow's feet,
smile too symmetrical too perfect, cold undercurrent
```

### F04 — Hollow Eyes

```
kof_char_c01, hollow eyes, pupils contracted, blank stare,
expression completely drops for 0.1 second,
lights just turned off, moment of truth, micro expression breakdown
```

### F05 — Suppressed Anger

```
kof_char_c01, suppressed anger, jaw tightened, lips pressed thin,
eyes focused intensely, nostrils slightly flared,
controlled fury, tension in forehead muscles
```

### F06 — Genuine Sadness

```
kof_char_c01, genuine sadness, eyebrows pulled together and up,
eyes glistening, lower eyelid tense, corners of mouth down,
vulnerability in eyes, authentic sorrow
```

---

## 6. 批量生成 ComfyUI 参数

### 6.1 角色种子集生成参数

| 参数 | 值 | 说明 |
|------|-----|------|
| 底模 | dreamshaperXL_v21.safetensors | |
| 画风LoRA | kof_webtoon_dark_v1.safetensors | 权重 0.7 |
| 分辨率 | 1024×1024 | 方形，适合半身/特写 |
| 采样器 | DPM++ 2M Karras | 速度与质量平衡 |
| Steps | 30 | |
| CFG | 7.0 | C/J纪元 7.0, K纪元 6.5, T纪元 8.0 |
| 批量 | 4张/角色 | 每角色4张种子图 |
| 种子 | 固定基础种子 42 + 角色编号 | 保证可复现 |
| IP-Adapter | 启用, weight 0.5 | 面部特征参考 |
| ControlNet | OpenPose (半身) | 姿态控制 |

### 6.2 四纪元差异化参数速查

| 参数 | C纪元 | K纪元 | J纪元 | T纪元 |
|------|-------|-------|-------|-------|
| CFG | 7.0 | 6.5 | 7.0 | 8.0 |
| Steps | 30 | 28 | 32 | 30 |
| Sampler | DPM++ 2M Karras | DPM++ 2M Karras | DPM++ SDE Karras | Euler a |
| LoRA权重 | 0.7 | 0.65 | 0.7 | 0.8 |
| 色温偏移 | -15 (冷) | +10 (暖) | -5 (微冷) | 极端 |
| 饱和度 | <15% | 25-35% | <20% | 极端对比 |

---

## 7. 生成计划与验收标准

### 7.1 生成阶段

| 阶段 | 内容 | 数量 | 预计耗时 |
|------|------|------|----------|
| 阶段1 | 30角色种子集（每人4张） | 120张 | 6小时 |
| 阶段2 | 人工筛选（每角色选20张最佳） | 600→600张 | 3小时 |
| 阶段3 | 服装变体（每角色3套×2张） | 180张 | 9小时 |
| 阶段4 | 微表情关键帧（每角色6种×1张） | 180张 | 9小时 |
| 阶段5 | 四纪元Shader测试图（每角色4纪元×1张） | 120张 | 6小时 |
| **合计** | | **~1200张** | **~33小时** |

### 7.2 验收标准

| 维度 | 标准 | 检查方式 |
|------|------|----------|
| 角色一致性 | 同角色不同图中面部特征相似度 >85% | IP-Adapter cosine similarity |
| 画风一致性 | 100%图片符合韩漫暗黑风格 | 人工对比参考图 |
| 纪元差异 | 四纪元视觉差异清晰可辨 | 四纪元对比测试矩阵 |
| 微表情可辨识 | 6种表情肉眼可区分 | 人工盲测（3人独立判断） |
| 线条质量 | 干净锐利无杂线 | 400%放大检查 |

---

> **文档结束**
> 《KOF大粉模拟器》30角色SD提示词 Brief v1.0
> 编写：Agent B | 监制：人类制作人
