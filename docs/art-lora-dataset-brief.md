# KOF大粉模拟器 — 画风LoRA数据集准备规格

> **项目**：KOF大粉模拟器  
> **模块**：美术/视觉（Agent B）  
> **底模**：DreamShaper XL  
> **训练目标**：统一 webtoon 暗黑风格 LoRA  
> **数据集规模**：80-120张精选（基准100张）  
> **版本**：v1.0  
> **日期**：2026-08-06  

画风定位为成熟向韩漫（webtoon），参考《甜蜜家园》《地狱公使》《神之塔》但更暗黑心理化。核心视觉特征：去饱和灰调占比70%以上、锐利干净的线条、强方向光加深阴影、7.5-8头身比例、赛璐璐加柔渐变渲染、半网点纹理配大量留白。四个纪元在统一基调上做差异化处理。

---

## 1. 数据集收集规范

### 1.1 来源

三类来源按优先级排列：

**A. 韩漫截图（主要来源，60-70张）**

参考作品以《甜蜜家园》(Sweet Home)、《地狱公使》(Hellbound)、《神之塔》(Tower of God) 为核心，扩展至其他成熟向/暗黑向韩漫。截图时从单话中选取风格代表性画面，避免连续帧重复。优先选取无对话气泡或气泡占比小的画面，减少后期处理负担。

**B. 风格匹配画师作品（补充来源，20-30张）**

筛选作品整体风格接近目标 webtoon 暗黑调的画师。优先选择已公开授权或明确允许用于训练参考的画师作品。避免直接使用有明显个人符号（签名、水印）的作品，防止模型学到画师个人标识。

**C. AI生成初筛图（辅助来源，10-15张）**

用 DreamShaper XL 配合风格提示词生成候选图，人工筛选后作为数据补充，弥补特定构图/光影类型的不足。占比不超过15%，避免底模自身特征回声导致过拟合。

### 1.2 筛选标准

每张入选图片需同时满足以下条件：

| 维度 | 要求 |
|------|------|
| 分辨率 | 原图长边 >= 1536px，保证裁剪后不损失细节 |
| 画质 | 无 JPEG 压缩伪影，无噪点过度，无模糊 |
| 风格匹配 | 去饱和灰调明显，线条锐利干净，有方向光和深阴影 |
| 人物比例 | 若含人物，头身比接近 7.5-8 头身 |
| 构图完整 | 主体清晰，未被画框/气泡截断 |
| 渲染特征 | 赛璐璐或赛璐璐+柔渐变渲染，可见半网点纹理 |
| 留白 | 画面有呼吸感，非满构图 |

### 1.3 剔除标准

出现以下任一情况的图片直接剔除：

- 分辨率不足（原图长边 < 1024px）
- 有水印、Logo、签名等明显标识
- 日漫风格特征明显（超大眼、夸张变形、高饱和彩色）
- 色彩过于鲜艳饱和（灰调占比不足50%）
- 线条模糊、锯齿严重或像素化
- 对话气泡/文字占比超过画面30%
- 与已选图片高度相似（同一场景不同帧、同一构图角度）
- 3D渲染质感过重（需保持2D手绘感）
- 写实过度（照片级渲染，失去 webtoon 特征）
- 版权状态不明确且无法确认

---

## 2. 图片分类与配比

基准总量 100 张，按纪元风格、构图类型、光影类型三个维度交叉分配。

### 2.1 按纪元风格分配

| 纪元 | 风格特征 | 数量 | 占比 | 说明 |
|------|----------|------|------|------|
| C纪元 | 最冷灰调，近乎单色 | 30张 | 30% | 基准风格，占比最高 |
| K纪元 | 微暖色调，blush腮红，拟声词 | 25张 | 25% | 唯一允许暖色点缀的纪元 |
| J纪元 | 柔光，大量留白，空灵 | 25张 | 25% | 负空间占比最大的纪元 |
| T纪元 | 极端光反差，明暗对撞 | 20张 | 20% | 戏剧性最强，数量适中 |

C纪元作为最冷灰调的基准风格，占比最高（30%），为模型建立统一灰调底色。K纪元引入微暖色和拟声词，训练模型对"暖色点缀"的克制运用。J纪元强调留白和柔光，训练负空间构图能力。T纪元的极端反差数量控制在20%，避免高反差画面主导整体风格。

### 2.2 按构图类型分配

| 构图类型 | 数量 | 占比 | 说明 |
|----------|------|------|------|
| 特写（close-up / portrait） | 20张 | 20% | 表情、眼神、面部光影细节 |
| 半身（upper body / cowboy shot） | 30张 | 30% | webtoon最常见构图，主力训练 |
| 全身（full body） | 25张 | 25% | 体态、服装、站姿 |
| 群像（multiple characters） | 10张 | 10% | 角色互动、空间关系 |
| 场景/环境（scenery / background） | 15张 | 15% | 无人物纯环境，训练氛围渲染 |

半身构图占30%，因为这是 webtoon 最普遍的画面类型，直接影响生成时的默认构图倾向。群像仅占10%，暗黑心理向风格中多人场景相对少用。场景/环境占15%，用于训练无人物时的氛围渲染和建筑/空间表现。

### 2.3 按光影类型分配

| 光影类型 | 数量 | 占比 | 适用纪元 |
|----------|------|------|----------|
| 强方向光（strong directional light） | 30张 | 30% | 全纪元通用，C/T纪元优先 |
| 柔光漫射（soft diffuse light） | 20张 | 20% | J/K纪元优先 |
| 逆光/轮廓光（backlight / rim light） | 15张 | 15% | J纪元优先 |
| 低照度暗调（low-key dark） | 20张 | 20% | C/T纪元优先 |
| 极端反差（extreme contrast / chiaroscuro） | 15张 | 15% | T纪元专用 |

强方向光占30%，是本项目画风的核心光影特征。极端反差全部归入T纪元，其他纪元不使用该光影类型，维持纪元间的差异化。

### 2.4 交叉配比矩阵

三个维度需交叉覆盖，每个纪元内尽量覆盖全部构图和光影类型。以C纪元30张为例：

| 构图＼光影 | 强方向光 | 柔光 | 逆光 | 低照度 | 极端反差 | 小计 |
|-----------|----------|------|------|--------|----------|------|
| 特写 | 4 | 1 | 1 | 2 | 0 | 8 |
| 半身 | 4 | 2 | 1 | 3 | 0 | 10 |
| 全身 | 3 | 1 | 1 | 2 | 0 | 7 |
| 群像 | 1 | 0 | 0 | 1 | 0 | 2 |
| 场景 | 1 | 0 | 0 | 1 | 1 | 3 |
| 小计 | 13 | 4 | 3 | 9 | 1 | 30 |

C纪元不使用极端反差（该光影专属T纪元），柔光和逆光数量较少（C纪元以冷硬方向光和暗调为主）。其余纪元按同样逻辑分配：

- **K纪元**（25张）：柔光占比提高，含blush和拟声词标签，不使用极端反差
- **J纪元**（25张）：逆光和柔光占比最高，留白标签突出，不使用极端反差
- **T纪元**（20张）：极端反差占主导，低照度暗调为辅，强方向光补充

---

## 3. 标注模板与范例

### 3.1 标注流程

1. **WD14 Tagger** 自动打标，生成初始 Booru 标签
2. 人工修正：删除错误标签、补充风格特征标签
3. 每张图片统一追加画风前缀标签
4. 保存为与图片同名的 `.txt` 文件（如 `era_c_closeup_directional_001.png` 对应 `era_c_closeup_directional_001.txt`）

### 3.2 标签结构模板

每张图片的标签按以下顺序组织，用英文逗号分隔：

```
[统一画风标签], [角色/主体标签], [构图标签], [表情/姿态标签], [光影标签], [色调标签], [渲染标签], [纹理标签], [背景/环境标签], [纪元特色标签], [氛围/情绪标签]
```

统一画风标签（每张必加）：

```
korean webtoon style, dark psychological, clean linework, semi-realistic
```

纪元特色标签对照：

| 纪元 | 特色标签 |
|------|----------|
| C纪元 | `cold gray palette, monochrome atmosphere, desaturated` |
| K纪元 | `slight warm tone, blush, onomatopoeia, sound effect text` |
| J纪元 | `soft light, large negative space, ethereal, pale tones` |
| T纪元 | `extreme contrast, chiaroscuro, deep black shadows, blinding highlight` |

### 3.3 标注范例

以下5个范例覆盖不同纪元、构图和光影类型，展示完整的标签组织方式。

**范例1：C纪元 / 特写 / 强方向光**

文件名：`era_c_closeup_directional_001.txt`

```
korean webtoon style, dark psychological, clean linework, semi-realistic, 1girl, solo, close-up, portrait, serious expression, cold gaze, strong directional lighting, harsh shadow on face, deep shadows, cold gray palette, monochrome atmosphere, desaturated, cel shading, soft gradient, semi-halftone texture, sharp eyes, detailed eyes, dark background, negative space, melancholic mood, tense atmosphere
```

标签要点：C纪元核心冷灰调标签齐全（`cold gray palette, monochrome atmosphere, desaturated`），强方向光配合面部硬阴影（`harsh shadow on face`），特写构图突出眼神细节。

**范例2：K纪元 / 半身 / 柔光**

文件名：`era_k_upperbody_soft_001.txt`

```
korean webtoon style, dark psychological, clean linework, semi-realistic, 1girl, solo, upper body, cowboy shot, slight smile, blush, soft lighting, gentle gradient, warm tone accent, slight warmth, cel shading, semi-halftone texture, sound effect text, onomatopoeia, school uniform, indoor, window light, light negative space, bittersweet mood
```

标签要点：K纪元独有标签 `blush, onomatopoeia, sound effect text, warm tone accent` 区别于其他纪元。柔光配合 `gentle gradient`，暖色仅作为点缀（`slight warmth`）而非主调。

**范例3：J纪元 / 全身 / 逆光**

文件名：`era_j_fullbody_backlight_001.txt`

```
korean webtoon style, dark psychological, clean linework, semi-realistic, 1boy, solo, full body, standing, contemplative pose, backlight, rim lighting, silhouette edge glow, soft light, ethereal atmosphere, large negative space, minimalist composition, pale tones, desaturated, cel shading, soft gradient, semi-halftone texture, white background, light rays, serene mood, lonely atmosphere
```

标签要点：J纪元强调 `large negative space, minimalist composition, ethereal atmosphere, pale tones`，逆光配合 `rim lighting, silhouette edge glow`，整体氛围空灵孤寂。

**范例4：T纪元 / 场景 / 极端反差**

文件名：`era_t_scenery_extreme_001.txt`

```
korean webtoon style, dark psychological, clean linework, semi-realistic, scenery, no humans, environment, extreme lighting contrast, chiaroscuro, deep black shadows, blinding highlight, dramatic lighting, high contrast, desaturated gray tones, cold gray palette, cel shading, semi-halftone texture, architectural elements, urban decay, abandoned building, wide shot, ominous mood, oppressive atmosphere
```

标签要点：T纪元极端反差标签 `extreme lighting contrast, chiaroscuro, deep black shadows, blinding highlight` 为该纪元专属。无人物场景用 `scenery, no humans` 标注，训练环境氛围渲染能力。

**范例5：群像 / 低照度暗调（C纪元）**

文件名：`era_c_group_lowkey_001.txt`

```
korean webtoon style, dark psychological, clean linework, semi-realistic, multiple girls, 2girls, group, confrontation, tense expression, low key lighting, dark atmosphere, dim environment, minimal light source, deep shadows, cold gray palette, desaturated, monochrome, cel shading, soft gradient, semi-halftone texture, indoor, corridor, dramatic tension, suspenseful mood
```

标签要点：群像用 `multiple girls, 2girls, group` 标注角色数量，低照度暗调用 `low key lighting, dark atmosphere, dim environment, minimal light source`，配合C纪元冷灰调标签，营造对峙紧张感。

---

## 4. 数据增强策略

### 4.1 增强规则

| 操作 | 是否使用 | 参数 | 说明 |
|------|----------|------|------|
| 水平翻转 | 是 | 50%概率 | 人物面朝方向随机化，避免模型偏向左侧构图 |
| 随机旋转 | 否 | — | 破坏 webtoon 水平视线逻辑 |
| 随机缩放 | 否 | — | 改变头身比例参照，影响7.5-8头身学习 |
| 色彩抖动 | 否 | — | 干扰去饱和灰调的学习稳定性 |
| 裁剪抖动 | 否 | — | 统一裁剪至目标分辨率，不做随机偏移 |
| 噪声添加 | 否 | — | 底模已能处理，额外噪声影响线条清晰度 |

仅使用水平翻转，其余增强方式全部禁用。webtoon 画风对线条角度、头身比例、灰调浓度高度敏感，旋转/缩放/色彩抖动会破坏这些特征的一致性，导致模型学到错误分布。

### 4.2 预处理流程

1. 统一裁剪至 1024x1024（横构图/方构图）或 768x1024（竖构图）
2. 裁剪策略：以人物面部或视觉中心为基准居中裁剪
3. 色彩空间统一转 sRGB
4. 保存为 PNG 格式（无损）
5. 对翻转后的图片生成独立标签文件，方向性标签相应调整

### 4.3 翻转标签处理

水平翻转后，方向性标签需同步调整，非方向性标签保持不变：

| 原标签 | 翻转后 |
|--------|--------|
| `facing left` | `facing right` |
| `looking left` | `looking right` |
| `hair over left eye` | `hair over right eye` |
| `light from left` | `light from right` |

翻转副本文件名添加 `_flip` 后缀，如 `era_c_closeup_directional_001_flip.png`。

---

## 5. 质量检查清单

人工抽检覆盖数据集的20%（约20张），从四个纪元中均匀抽取，按以下标准逐项检查。

### 5.1 图片质量检查

| 检查项 | 标准 | 判定 |
|--------|------|------|
| 分辨率 | 1024x1024 或 768x1024，无偏差 | |
| 格式 | PNG，无损压缩 | |
| 画质 | 无压缩伪影、无噪点、无模糊 | |
| 色彩空间 | sRGB | |
| 人物比例 | 含人物图片头身比 7.5-8 头身 | |
| 灰调占比 | 去饱和灰调占画面 70% 以上 | |
| 线条质量 | 锐利干净，无锯齿、无断线 | |
| 水印/标识 | 无水印、Logo、签名 | |

### 5.2 风格一致性检查

| 检查项 | 标准 |
|--------|------|
| 整体风格 | 符合韩漫 webtoon 暗黑调，无日漫/写实/3D混入 |
| 渲染方式 | 赛璐璐或赛璐璐+柔渐变，非厚涂/油画 |
| 纹理特征 | 可见半网点纹理或柔渐变过渡 |
| 留白 | 画面有呼吸感，非满构图 |
| 光影 | 有明确方向光和深阴影，非平光 |

### 5.3 标注质量检查

| 检查项 | 标准 |
|--------|------|
| 统一标签 | 每张含 `korean webtoon style, dark psychological, clean linework, semi-realistic` |
| 标签准确性 | WD14 误标已修正，无错误角色/物品标签 |
| 标签完整度 | 构图、光影、色调、氛围标签齐全 |
| 方向标签 | 翻转图片的方向标签已同步调整 |
| 文件对应 | 每张 `.png` 有同名 `.txt`，无遗漏 |
| 格式规范 | 逗号分隔，无重复标签，无空标签 |

### 5.4 配比检查

| 检查项 | 标准 |
|--------|------|
| 总量 | 80-120 张之间 |
| 纪元配比 | C:K:J:T 约 30:25:25:20 |
| 构图覆盖 | 五种构图类型均有覆盖 |
| 光影覆盖 | 五种光影类型均有覆盖 |
| 交叉覆盖 | 每个纪元内至少覆盖3种构图和3种光影 |

---

## 6. 数据集目录结构

```
lora_dataset/
├── raw/                            # 原始收集图片（未处理）
│   ├── era_c/
│   ├── era_k/
│   ├── era_j/
│   └── era_t/
├── curated/                        # 筛选后图片（通过剔除标准）
│   ├── era_c/
│   ├── era_k/
│   ├── era_j/
│   └── era_t/
├── processed/                      # 裁剪+统一分辨率后
│   ├── era_c/
│   │   ├── era_c_closeup_directional_001.png
│   │   ├── era_c_closeup_directional_001.txt
│   │   ├── era_c_upperbody_lowkey_001.png
│   │   ├── era_c_upperbody_lowkey_001.txt
│   │   └── ...
│   ├── era_k/
│   ├── era_j/
│   └── era_t/
├── augmented/                      # 增强后（含翻转副本）
│   ├── era_c/
│   ├── era_k/
│   ├── era_j/
│   └── era_t/
├── metadata/
│   ├── source_log.csv              # 来源记录
│   ├── tag_stats.json              # 标签频次统计
│   ├── split_config.json           # 训练/验证集划分配置
│   └── quality_report.md           # 质量抽检报告
└── config/
    ├── tag_template.txt            # 标注模板参考
    ├── wd14_config.json            # WD14 Tagger 配置
    └── augmentation_config.json    # 数据增强参数配置
```

### 6.1 文件命名规范

```
[纪元]_[构图]_[光影]_[序号].png
[纪元]_[构图]_[光影]_[序号].txt
```

示例：`era_c_closeup_directional_001.png` / `era_c_closeup_directional_001.txt`

翻转副本添加 `_flip` 后缀：

```
era_c_closeup_directional_001_flip.png
era_c_closeup_directional_001_flip.txt
```

### 6.2 source_log.csv 字段

| 字段 | 说明 | 示例 |
|------|------|------|
| filename | 文件名 | era_c_closeup_directional_001.png |
| source_type | 来源类型 | 截图 / 画师作品 / AI生成 |
| source_title | 作品标题或画师名 | Sweet Home S1E3 |
| source_url | 来源链接 | https://... |
| license | 授权状态 | 公开 / 需确认 / 自生成 |
| era | 所属纪元 | C |
| composition | 构图类型 | closeup |
| lighting | 光影类型 | directional |
| note | 备注 | 面部阴影层次好 |
