# KOF大粉模拟器 — Agent B 美术/视觉 SD底模选型报告与画风测试方案

| 字段 | 内容 |
|------|------|
| 文档编号 | sd-model-selection-v1 |
| 版本 | v1.0 |
| 负责人 | Agent B（美术/视觉） |
| 创建日期 | 2026-08-06 |
| 项目 | KOF大粉模拟器（虚拟偶像生存游戏） |
| 技术栈 | Stable Diffusion (ComfyUI) + Phaser.js (Fragment Shader) |

---

## 1 文档概述

### 1.1 项目背景

KOF大粉模拟器是一款虚拟偶像生存游戏，核心叙事围绕10名角色在四纪元（C/K/J/T）中的心理博弈与生存竞争展开。游戏采用Phaser.js进行渲染，所有角色立绘与场景美术由Stable Diffusion本地推理生成，四纪元的视觉风格差异通过Fragment Shader实时实现，以最小化美术资源的重复制作成本。

项目的画风定位为成熟向韩漫（webtoon）风格，视觉参考包括《甜蜜家园》的暗黑恐怖氛围、《地狱公使》的超自然压迫感、《神之塔》的层次化叙事构图，但在心理深度与暗黑基调上更进一步——角色的内心状态直接映射到画面色调与光影处理上。

### 1.2 核心约束

| 约束维度 | 具体要求 |
|----------|----------|
| 画风 | 成熟向韩漫（webtoon），暗黑心理化基调 |
| 角色规模 | 10个核心角色，每人3套服装（练习服/舞台服/便装），6种微表情关键帧 |
| 四纪元差异 | 通过Fragment Shader实现，SD生成相对中性的基础图 |
| 技术栈 | ComfyUI本地部署，零API调用成本 |
| 预算上限 | 总美术预算约1.2万元人民币 |
| 输出规格 | 角色立绘 1024x1024 / 768x1024，场景 1024x576（16:9） |

### 1.3 设计决策摘要

四纪元的视觉差异主要通过Phaser.js的Fragment Shader在运行时处理，SD端只需生成光照和色彩相对中性的基础图，由Shader施加色板偏移、网点叠加、对比度调整等纪元特征。这一架构决策将四纪元的美术资源量从4倍压缩到约1.3倍（基础图共享 + 少量纪元专属overlay素材）。只有在Shader无法有效模拟的元素（如K纪元的拟声词气泡、C纪元的半网点纹理细节）时，才需要SD端或手动制作额外的overlay图层。

---

## 2 底模选型分析

### 2.1 选型标准

底模选择基于以下六个维度，权重根据项目需求调整：

| 维度 | 权重 | 评估要点 |
|------|------|----------|
| 画风匹配度 | 25% | 能否在LoRA辅助下输出韩漫风格的半写实线条与渲染 |
| 暗黑氛围表现 | 20% | 对低饱和度、硬光影、暗调场景的掌控力 |
| 角色一致性兼容 | 20% | 与IP-Adapter/ControlNet/LoRA的配合质量 |
| 硬件效率 | 15% | 本地推理速度与VRAM占用 |
| 线条质量 | 10% | 线稿清晰度，能否支撑webtoon的干净线条美学 |
| 社区生态 | 10% | 可用的LoRA/Embedding/Workflow丰富度 |

### 2.2 候选模型对比

以下五个候选模型覆盖了SDXL与SD1.5两大生态，风格从半写实到动漫向排列：

#### 2.2.1 候选模型概览

**候选A：DreamShaper XL（SDXL）**

Lykon开发的半写实风格SDXL模型，擅长在写实与插画之间切换。人物比例接近真人但保留插画质感，对暗调场景和戏剧光影有较好表现。Civitai下载量超过50万次，社区生态成熟，有大量配套LoRA可用。

核心优势在于"可塑性"——它不像纯动漫模型那样风格固化，通过prompt和LoRA可以将其推向韩漫方向，同时保留半写实的体积感和材质表现。对《甜蜜家园》《地狱公使》这类偏写实的韩漫风格，DreamShaper XL的底子比纯动漫模型更接近目标。

**候选B：CounterfeitXL（SDXL）**

EasyGodMode开发的动漫/插画风格SDXL模型，线条干净利落，色彩饱和度适中。在Civitai上长期占据动漫类模型前列，配套的LoRA和embedding生态极为丰富。

优势在于线条质量——webtoon的核心美学之一就是干净的线条和清晰的区域分色，CounterfeitXL在这方面的表现优于多数半写实模型。劣势是默认风格偏向日式动漫，需要通过LoRA将其拉向韩漫的半写实比例。

**候选C：Pony Diffusion V6 XL（SDXL）**

C-V-A-I开发的角色生成专用SDXL模型，以极强的prompt遵循能力和角色多样性著称。其独特的标签系统（score tags）对角色属性控制极为精细，在角色一致性方面有天然优势。

Pony的标签系统使得"同一角色不同服装/表情"的批量生成效率很高，对项目需要的10角色x3服装x6表情矩阵有直接帮助。但默认画风偏向欧美插画风格，需要较强的LoRA干预才能达到韩漫质感。

**候选D：Juggernaut XL（SDXL）**

RunDiffusion开发的写实风格SDXL模型，在Civitai上是最流行的写实类模型之一。照片级写实是其默认表现，但通过prompt和负面提示词可以将其拉向插画方向。

优势在于对光影、材质、氛围的精细表现——暗黑心理化韩漫需要的那种"电影感"光影，Juggernaut XL的底子最好。劣势是写实感过强，需要较大的风格偏移才能到达插画/韩漫领域，LoRA训练成本较高。

**候选E：MeinaMix v11（SD1.5）**

Meina开发的动漫风格SD1.5模型，是1.5生态中线条最干净的模型之一。VRAM占用低（6GB即可推理），在低端硬件上表现稳定。

优势在于硬件门槛低、推理速度快，适合作为快速迭代和测试的备选方案。劣势是SD1.5的分辨率上限（512x512原生，放大后细节不如SDXL），以及半写实表现力不足，更偏向纯动漫风格。

#### 2.2.2 对比矩阵

| 维度 | DreamShaper XL | CounterfeitXL | Pony V6 XL | Juggernaut XL | MeinaMix (SD1.5) |
|------|---------------|---------------|------------|---------------|-------------------|
| 画风匹配度 | 8.5 | 7.5 | 6.5 | 7.0 | 6.0 |
| 暗黑氛围 | 9.0 | 6.5 | 6.0 | 9.5 | 5.5 |
| 角色一致性兼容 | 8.0 | 8.5 | 9.5 | 7.5 | 7.0 |
| 硬件效率 | 6.5 | 6.5 | 6.0 | 6.0 | 9.0 |
| 线条质量 | 7.0 | 9.0 | 7.0 | 5.5 | 8.5 |
| 社区生态 | 8.5 | 9.0 | 8.5 | 8.0 | 7.5 |
| 加权总分 | **7.95** | 7.75 | 7.35 | 7.40 | 6.85 |
| VRAM需求(推理) | 8-12GB | 8-12GB | 10-12GB | 10-12GB | 4-6GB |
| VRAM需求(训练LoRA) | 16-24GB | 16-24GB | 16-24GB | 16-24GB | 8-12GB |

### 2.3 推荐方案

**主底模：DreamShaper XL**

推荐理由：DreamShaper XL在加权评分中得分最高，核心优势在于"半写实可塑性"与"暗黑氛围表现"的平衡。项目需要的韩漫风格介于写实与动漫之间——比日式动漫更写实（人物比例更接近真人），又比纯写实更有插画感（保留线条和区域分色）。DreamShaper XL的默认输出恰好落在这个区间，通过webtoon风格LoRA的微调即可达到目标画风。

在暗黑氛围方面，DreamShaper XL对低饱和度调色、硬阴影、戏剧性光源的表现力是五个候选中最强的之一，与C纪元（冷灰调硬光影）和T纪元（极端对比多光源）的需求高度契合。其半写实基底也为K纪元（微暖调柔光）和J纪元（中性偏冷留白）提供了足够的调色空间。

角色一致性方面，DreamShaper XL与IP-Adapter plus和ControlNet的兼容性经过社区大量验证，配合角色LoRA可以实现稳定的跨场景一致性。

**备选底模：CounterfeitXL**

当DreamShaper XL的线条感不足、需要更干净的webtoon线条时，切换到CounterfeitXL作为备选。两者可以使用相同的角色LoRA（同为SDXL架构），切换成本低。CounterfeitXL特别适合K纪元和J纪元这两个对线条清晰度要求较高的纪元。

**低端回退：MeinaMix v11**

在GPU资源紧张（如同时进行多任务）或需要快速迭代测试prompt时，使用MeinaMix在SD1.5下进行预览生成。最终生产图仍使用SDXL主底模。

### 2.4 部署架构

```
ComfyUI 本地部署架构
├── 基础环境
│   ├── Python 3.10.6 + PyTorch 2.1 + CUDA 12.1
│   ├── ComfyUI (最新版, git clone)
│   └── xformers 0.0.23 (注意力加速)
│
├── 模型存储 (models/)
│   ├── checkpoints/
│   │   ├── dreamshaperXL_v21.safetensors      (6.46GB, 主底模)
│   │   ├── counterfeitXL_v25.safetensors       (6.46GB, 备选)
│   │   └── meinamix_v11.safetensors            (2.13GB, 回退)
│   ├── loras/
│   │   ├── characters/                         (角色LoRA, 10个)
│   │   ├── styles/                             (画风LoRA, 2-3个)
│   │   └── effects/                            (效果LoRA: 网点/光影)
│   ├── controlnet/
│   │   ├── control_v11p_sd15_openpose.pth
│   │   ├── control_v11p_sd15_lineart.pth
│   │   ├── controlnet-openpose-sdxl-1.0.pth
│   │   └── controlnet-canny-sdxl-1.0.pth
│   ├── ipadapter/
│   │   ├── ip-adapter-plus_sdxl_vit-h.safetensors
│   │   └── ip-adapter-plus-face_sdxl_vit-h.safetensors
│   └── vae/
│       └── sdxl_vae.safetensors
│
├── 自定义工作流 (workflows/)
│   ├── character_base.json                     (角色基础立绘)
│   ├── character_expression.json               (微表情变体)
│   ├── scene_background.json                   (场景背景)
│   ├── batch_outfit.json                       (服装批量切换)
│   └── quality_upscale.json                    (高清放大)
│
└── 输出管理 (output/)
    ├── raw/                                     (SD原始输出)
    ├── processed/                               (后处理后)
    ├── approved/                                (抽检通过)
    └── rejected/                                (废图存档)
```

---

## 3 LoRA方案

### 3.1 LoRA架构总览

项目采用双层LoRA架构，将"角色身份"与"画风风格"解耦，实现灵活组合：

```
生成管线 = 底模(DreamShaper XL)
           + 画风LoRA (webtoon暗黑风格, 权重0.6-0.8)
           + 角色LoRA (特定角色面部/体型特征, 权重0.7-0.9)
           + IP-Adapter (参考图面部特征锁定)
           + ControlNet (姿态/构图控制)
```

这种架构的好处是：修改画风不需要重新训练所有角色LoRA，新增角色也不需要重新训练画风LoRA。两层LoRA独立迭代，互不干扰。

### 3.2 画风LoRA训练计划

#### 3.2.1 训练目标

训练一个统一的webtoon暗黑风格LoRA，使DreamShaper XL的输出从默认半写实风格偏移到目标韩漫风格。LoRA需要编码以下风格特征：

- 干净但有力的线条（非纯线稿，而是带有区域分色的半渲染线条）
- 半写实人物比例（眼鼻比例接近真人，但保留风格化处理）
- 韩漫式的体积渲染（柔和的渐变阴影，非日漫的硬色块）
- 暗黑心理化基调（偏向低饱和、高对比的调色倾向）
- 韩漫特有的细节处理（发丝分组、衣褶简化、面部阴影走向）

#### 3.2.2 数据集准备

| 项目 | 规格 |
|------|------|
| 图片数量 | 80-120张（精选，质量优先于数量） |
| 图片分辨率 | 统一裁剪至1024x1024或768x1024 |
| 来源 | 韩漫截图（《甜蜜家园》《地狱公使》《神之塔》《 pijama 》等）+ 风格匹配的画师作品 |
| 标注方式 | WD14 tagger自动打标 + 人工修正 |
| 标注格式 | Booru标签（danbooru tags），逗号分隔 |
| 标注内容 | 画风标签 + 内容描述，画风标签统一使用 `korean webtoon style, dark psychological, clean linework, semi-realistic` |
| 数据增强 | 轻度水平翻转（50%概率），不使用旋转/缩放（避免线条变形） |

数据筛选要点：剔除含明显日漫特征（大眼、夸张表情）的样本，剔除过度写实的样本。保留的样本应具有统一的"韩漫感"——可以简单概括为"介于日漫与美漫之间、偏向写实比例的干净线条风格"。

#### 3.2.3 训练参数配置（kohya_ss / SDXL）

```
# 画风LoRA训练参数 — kohya_ss GUI配置
[Model]
base_model: dreamshaperXL_v21.safetensors
model_type: sdxl
conv_dim: 32
conv_alpha: 16
network_dim: 64
network_alpha: 32

[Dataset]
resolution: 1024,1024
batch_size: 2
min_bucket_reso: 768
max_bucket_reso: 1280
enable_bucket: true

[Training]
learning_rate: 1e-4
unet_lr: 1e-4
text_encoder_lr: 5e-5
lr_scheduler: cosine_with_restarts
lr_scheduler_power: 0.5
lr_scheduler_num_cycles: 3
optimizer: AdamW8bit
max_train_epochs: 15
save_every_n_epochs: 3
gradient_accumulation_steps: 4
mixed_precision: bf16
save_precision: bf16
xformers: true
gradient_checkpointing: true

[Regularization]
正则化图片: 500张通用SDXL生成图
正则化频率: 每5步1张
```

预期产出：5个epoch存档（epoch 3/6/9/12/15），每个约150-200MB。通过人工对比选出风格偏移最自然、不过拟合的版本（通常是epoch 9-12之间的存档）。

训练耗时估计：在RTX 4070 Ti Super 16GB上，100张图片、15个epoch，约需4-6小时。

### 3.3 角色一致性LoRA训练计划

#### 3.3.1 训练目标

为10个核心角色各训练一个独立的角色LoRA，编码角色的固定特征（面部结构、发型、体型、标志性细节），使得在不同服装、表情、场景下都能保持角色辨识度。

#### 3.3.2 单角色数据集准备

| 项目 | 规格 |
|------|------|
| 图片数量 | 30-50张/角色 |
| 图片构成 | 正面照10-15张，侧面照5-8张，半身/全身5-8张，不同表情5-8张，不同服装5-8张 |
| 图片来源 | 手绘角色设定图 + DreamShaper XL初步生成的精选图 |
| 标注方式 | 每张图标注角色触发词 + 具体描述 |
| 角色触发词 | 统一格式 `kof_char_XX`（XX为角色编号01-10），不使用常见词汇避免冲突 |
| 标注示例 | `kof_char_01, 1girl, short black hair, sharp eyes, slim build, white practice uniform, neutral expression, looking at viewer` |

数据准备策略：先用DreamShaper XL + 画风LoRA生成一批候选图，人工筛选出最符合角色设定的20张作为"种子集"，基于种子集训练第一版LoRA（v0.1），再用v0.1 LoRA生成更多变体图，人工筛选补充到50张，训练正式版LoRA（v1.0）。这种"自举"策略可以在没有手绘参考图的情况下逐步建立高质量训练集。

#### 3.3.3 训练参数配置（kohya_ss / SDXL）

```
# 角色LoRA训练参数 — kohya_ss GUI配置
[Model]
base_model: dreamshaperXL_v21.safetensors
model_type: sdxl
conv_dim: 16
conv_alpha: 8
network_dim: 32
network_alpha: 16

[Dataset]
resolution: 1024,1024
batch_size: 2
min_bucket_reso: 768
max_bucket_reso: 1280
enable_bucket: true

[Training]
learning_rate: 5e-5
unet_lr: 5e-5
text_encoder_lr: 2.5e-5
lr_scheduler: cosine
lr_scheduler_power: 1.0
optimizer: AdamW8bit
max_train_epochs: 20
save_every_n_epochs: 4
gradient_accumulation_steps: 4
mixed_precision: bf16
save_precision: bf16
xformers: true
gradient_checkpointing: true

[Regularization]
正则化图片: 使用画风LoRA生成的通用角色图, 300张
正则化频率: 每3步1张
```

角色LoRA的dim设为32（低于画风LoRA的64），因为角色特征的信息量小于画风特征，过高的dim容易导致过拟合——表现为角色LoRA"污染"画风，使输出变成训练图的样子而非保持画风。

预期产出：5个epoch存档（epoch 4/8/12/16/20），每个约80-100MB。角色LoRA的理想状态是"面部一致但服装/表情/场景可自由变化"，通常epoch 12-16之间的存档能达到这个平衡。

#### 3.3.4 LoRA训练时间表

| 阶段 | 内容 | 预计耗时 | 依赖 |
|------|------|----------|------|
| 阶段0 | 画风LoRA数据集准备 | 2天 | 无 |
| 阶段1 | 画风LoRA v1.0训练 + 测试 | 1天 | 阶段0 |
| 阶段2 | 10角色种子集生成 + 筛选 | 3天 | 阶段1 |
| 阶段3 | 10角色LoRA v0.1训练 | 2天 | 阶段2 |
| 阶段4 | 角色变体图批量生成 + 筛选 | 3天 | 阶段3 |
| 阶段5 | 10角色LoRA v1.0训练 | 2天 | 阶段4 |
| 阶段6 | 画风LoRA + 角色LoRA联合测试 | 2天 | 阶段5 |
| 阶段7 | 参数微调与LoRA版本定稿 | 2天 | 阶段6 |
| **合计** | | **约17个工作日** | |

### 3.4 角色一致性保障策略

LoRA单独无法保证100%的角色一致性，需要多层策略叠加：

**第一层：角色LoRA（身份锁定）**

角色LoRA通过训练掌握了角色的固定面部特征和体型比例。生成时以0.7-0.9的权重加载，确保面部结构一致。

**第二层：IP-Adapter plus face（面部锁定）**

在生成变体图时，使用IP-Adapter plus face模型输入一张该角色的"标准面孔图"作为参考。IP-Adapter从参考图中提取面部特征向量，注入到生成过程中，权重设为0.4-0.6（与角色LoRA互补，不喧宾夺主）。

**第三层：ControlNet OpenPose（姿态控制）**

使用OpenPose控制角色的肢体姿态，确保不同服装/场景下的角色姿势可控。对于微表情关键帧，使用面部关键点（facial keypoints）模式控制五官位置。

**第四层：种子复用 + 局部重绘**

同一角色的不同服装/表情变体使用相同的基础种子（base seed），仅通过prompt修改服装/表情描述。对于面部不一致的输出，使用Inpainting进行局部重绘修复，而不是整图重新生成。

**一致性验证流程**：

```
1. 使用角色LoRA + 画风LoRA生成4张测试图（不同prompt/不同种子）
2. 将4张图的面部裁剪，拼接成对比图
3. 人工评分（1-5分制）：
   - 面部结构一致性（五官比例、脸型）
   - 发型一致性（长度、颜色、造型）
   - 体型一致性（身高比例、体型特征）
4. 评分≥4分视为通过；≤3分需调整LoRA权重或重新训练
5. 每个角色在不同纪元场景下各测试一次（共4次验证）
```

---

## 4 四纪元画风测试方案

### 4.1 架构前提

四纪元的视觉差异主要通过Phaser.js Fragment Shader在运行时实现，SD端生成的 base image 遵循以下原则：

- **光照中性化**：避免极端色温的光源（如纯暖光/纯冷光），使用接近白光的光照设置，为Shader色板偏移留出空间
- **饱和度适中**：SD输出的饱和度控制在30-40%左右，Shader再根据纪元需求压低或调整
- **网点/纹理**：C纪元的半网点纹理和J纪元的极轻网点通过Shader的半色调算法实现，SD端不生成网点
- **overlay素材**：K纪元的拟声词气泡、blush标记等作为独立的overlay图层制作，不依赖SD生成

以下方案中，每个纪元给出两套配置：**SD端生成参数**（生成基础图）和**Shader端处理参数**（运行时施加纪元效果）。

### 4.2 C纪元 — 冷灰硬光

#### 4.2.1 视觉规格

| 参数 | 目标值 | 实现方式 |
|------|--------|----------|
| 饱和度 | <15% | Shader: HSL饱和度乘以0.15 |
| 色板 | 钢灰/冷白/炭黑 | Shader: 三色LUT映射 |
| 网点纹理 | 半网点，覆盖率40-50% | Shader: blue noise halftone, cell size 4px |
| 光影风格 | 硬光影，锐利阴影边缘 | SD端: 高对比度光照 + Shader: 阴影区域强化 |
| 整体氛围 | 冰冷、压迫、审讯感 | 多层叠加 |

#### 4.2.2 SD端生成参数

```yaml
# C纪元基础图生成参数 — ComfyUI
prompt: >
  korean webtoon style, dark psychological,
  1girl, kof_char_01, short black hair, sharp eyes,
  wearing white practice uniform,
  standing in concrete corridor, harsh overhead fluorescent light,
  strong shadows on face, high contrast lighting,
  desaturated color palette, cold atmosphere,
  looking at viewer with cold expression,
  semi-realistic, clean linework, detailed shading
negative_prompt: >
  warm colors, bright saturation, soft lighting, cute, anime style,
  big eyes, colorful background, sunny, cheerful, blush, moe
cfg_scale: 7.5
steps: 30
sampler: dpmpp_2m
scheduler: karras
denoise: 1.0
width: 832
height: 1216
seed: [固定基础种子]
loras:
  - path: styles/webtoon_dark_v1.safetensors
    weight: 0.7
  - path: characters/kof_char_01_v1.safetensors
    weight: 0.85
controlnet:
  type: openpose
  weight: 0.5
  model: controlnet-openpose-sdxl-1.0.pth
```

#### 4.2.3 Shader端处理参数（GLSL伪代码）

```glsl
// C纪元 Fragment Shader — Phaser.js
uniform sampler2D uTexture;
uniform float uTime;
varying vec2 vTexCoord;

// 半色调网点
float halftone(vec2 uv, float cellSize, float threshold) {
    vec2 cell = floor(uv / cellSize) * cellSize;
    float dotSize = step(threshold, fract(uv.x / cellSize) + fract(uv.y / cellSize));
    return dotSize;
}

void main() {
    vec4 color = texture2D(uTexture, vTexCoord);
    
    // 1. 降饱和度至<15%
    float gray = dot(color.rgb, vec3(0.299, 0.587, 0.114));
    color.rgb = mix(vec3(gray), color.rgb, 0.15);
    
    // 2. 三色LUT映射 (钢灰/冷白/炭黑)
    color.rgb = mix(vec3(0.12, 0.14, 0.16),    // 炭黑
                    mix(vec3(0.55, 0.58, 0.62), // 钢灰
                        vec3(0.85, 0.87, 0.90), // 冷白
                        smoothstep(0.4, 0.8, gray)),
                    smoothstep(0.1, 0.5, gray));
    
    // 3. 半色调网点叠加 (暗部)
    float halftoneMask = halftone(vTexCoord * vec2(768.0, 1024.0), 4.0, 1.0 - gray * 0.8);
    color.rgb *= mix(1.0, 0.7, halftoneMask * (1.0 - gray));
    
    // 4. 硬光影强化
    color.rgb = smoothstep(0.2, 0.8, color.rgb);
    
    gl_FragColor = color;
}
```

#### 4.2.4 预期效果

角色面部呈现冷白色调，阴影区域压至炭黑色，中间调仅有钢灰过渡。半网点纹理在暗部区域明显可见，模拟传统印刷网屏效果。整体画面如同在审讯室的荧光灯下拍摄——冰冷、无感情、压迫感强。线条在网点叠加下仍保持可辨识性。

### 4.3 K纪元 — 微暖柔光

#### 4.3.1 视觉规格

| 参数 | 目标值 | 实现方式 |
|------|--------|----------|
| 饱和度 | 25-35% | Shader: HSL饱和度乘以0.30 |
| 色板 | 米色/暗金/暖灰 | Shader: 暖色LUT映射 |
| 光影风格 | 柔光侧光，柔焦感 | SD端: 软光设置 + Shader: 轻微bloom |
| 特殊元素 | blush/泪滴/拟声词气泡 | overlay图层独立制作，非SD生成 |
| 整体氛围 | 温暖、脆弱、情感流动 | 暖色调 + 柔焦 |

#### 4.3.2 SD端生成参数

```yaml
# K纪元基础图生成参数 — ComfyUI
prompt: >
  korean webtoon style, dark psychological,
  1girl, kof_char_01, short black hair, sharp eyes,
  wearing casual clothes, cream colored sweater,
  sitting by window, soft side light from left,
  warm ambient lighting, gentle shadows,
  looking slightly down, melancholic expression,
  tear drop on cheek, slight blush on cheeks,
  semi-realistic, clean linework, emotional atmosphere
negative_prompt: >
  cold colors, blue tint, harsh lighting, high contrast,
  flat shading,硬阴影, cheerful, bright, saturated
cfg_scale: 6.5
steps: 28
sampler: dpmpp_2m
scheduler: karras
denoise: 1.0
width: 832
height: 1216
seed: [固定基础种子 + 偏移量]
loras:
  - path: styles/webtoon_dark_v1.safetensors
    weight: 0.6
  - path: characters/kof_char_01_v1.safetensors
    weight: 0.85
```

K纪元的SD端生成相对其他纪元最接近最终效果，因为暖色调和柔光难以完全通过Shader从冷色基础图转换。SD端在生成时就应偏向暖调，Shader端做精细微调。

#### 4.3.3 overlay素材制作

K纪元需要以下overlay素材，在Phaser.js中作为独立图层叠加：

| 素材类型 | 制作方式 | 数量 | 规格 |
|----------|----------|------|------|
| 拟声词气泡 | 手绘/PS制作, 透明PNG | 15-20个 | 512x512, 透明背景 |
| 泪滴效果 | 手绘/PS制作, 透明PNG | 5-8种 | 256x256, 透明背景 |
| Blush标记 | 手绘/PS制作, 透明PNG | 3-5种 | 256x256, 透明背景 |
| 柔光粒子 | 程序化生成(Shader) | - | 运行时生成 |

这些overlay素材不依赖SD生成，一次性制作后可在所有K纪元场景中复用。

#### 4.3.4 预期效果

画面整体笼罩在米黄色的暖光中，角色面部有柔和的侧光过渡，阴影边缘模糊。脸颊泛起淡淡的红色blush（overlay），眼角有泪滴反光（overlay）。暗部呈现暗金色而非纯黑，亮部为温暖的米色。整体如同在夕阳下的咖啡馆角落——温暖但带着无法言说的忧伤。

### 4.4 J纪元 — 留白雾灰

#### 4.4.1 视觉规格

| 参数 | 目标值 | 实现方式 |
|------|--------|----------|
| 饱和度 | <20% | Shader: HSL饱和度乘以0.20 |
| 色板 | 雾灰/旧白/墨绿 | Shader: 偏绿LUT映射 |
| 网点纹理 | 极轻网点, 覆盖率<10% | Shader: blue noise, cell size 2px, 仅暗部 |
| 留白 | 30-40%空白区域 | SD端: 构图控制 + Shader: 边缘渐隐 |
| 光影风格 | 窗帘柔光, 漫射 | SD端: 漫射光设置 |
| 整体氛围 | 寂静、疏离、文学感 | 大面积留白 + 低对比 |

#### 4.4.2 SD端生成参数

```yaml
# J纪元基础图生成参数 — ComfyUI
prompt: >
  korean webtoon style, dark psychological,
  1girl, kof_char_01, short black hair, sharp eyes,
  wearing practice uniform, standing near window,
  sheer curtain diffused light, misty atmosphere,
  minimal composition, lots of negative space,
  foggy gray tones, old white walls, ink green accents,
  distant gaze, contemplative, serene but lonely,
  semi-realistic, clean linework, minimal shading
negative_prompt: >
  busy background, detailed background, high contrast,
  strong shadows, warm colors, vibrant, crowded,
  complex composition, heavy textures
cfg_scale: 5.5
steps: 25
sampler: dpmpp_2m
scheduler: karras
denoise: 1.0
width: 832
height: 1216
seed: [固定基础种子]
loras:
  - path: styles/webtoon_dark_v1.safetensors
    weight: 0.5
  - path: characters/kof_char_01_v1.safetensors
    weight: 0.85
```

J纪元的CFG scale降至5.5（其他纪元6.5-7.5），因为低CFG值会产生更柔和、更模糊的过渡，符合"留白雾灰"的视觉需求。画风LoRA权重降至0.5，让底模的半写实感更多保留，营造文学化的疏离感。

#### 4.4.3 预期效果

画面30-40%为空白（白色或极浅灰），角色偏居一侧，周围大量留白。窗帘柔光从一侧漫射进入，在角色身上形成柔和的明暗过渡，无明显阴影边界。整体色调偏冷绿（雾灰+墨绿），有如水彩般的透明感。极轻的网点仅在最暗部隐约可见。视觉感受如同翻开一本安静的文学小说——克制、疏离、有余韵。

### 4.5 T纪元 — 极端对比

#### 4.5.1 视觉规格

| 参数 | 目标值 | 实现方式 |
|------|--------|----------|
| 对比度 | 极端, 无中间灰 | Shader: posterize 2-level + 阈值 |
| 色板 | 纯黑/纯白/高饱和点缀 | Shader: 二值化 + 局部高饱和色注入 |
| 网点纹理 | 无 | 不施加网点 |
| 光影风格 | 多光源混用, 冲突感 | SD端: 多光源prompt + Shader: 色彩分离 |
| 整体氛围 | 暴力、断裂、精神崩溃 | 极端视觉冲击 |

#### 4.5.2 SD端生成参数

```yaml
# T纪元基础图生成参数 — ComfyUI
prompt: >
  korean webtoon style, dark psychological,
  1girl, kof_char_01, short black hair, sharp eyes,
  wearing stage costume,
  multiple conflicting light sources, red and blue rim light,
  extreme high contrast, dramatic split lighting,
  chaotic energy, intense expression, screaming,
  pure black background, spotlight from below,
  neon accent colors, electric atmosphere,
  semi-realistic, clean linework, extreme dramatic
negative_prompt: >
  soft lighting, gradient shadows, midtones, gray,
  natural lighting, calm, serene, pastel, muted
cfg_scale: 8.5
steps: 32
sampler: dpmpp_2m
scheduler: karras
denoise: 1.0
width: 832
height: 1216
seed: [固定基础种子]
loras:
  - path: styles/webtoon_dark_v1.safetensors
    weight: 0.8
  - path: characters/kof_char_01_v1.safetensors
    weight: 0.85
```

T纪元的CFG scale升至8.5，高CFG值会增强对比度和色彩分离，生成更"极端"的图像。画风LoRA权重升至0.8，强化风格的戏剧性。

#### 4.5.3 预期效果

画面被压缩为近乎黑白二值，角色面部被多方向光源切割成碎片化的明暗区域。红色和蓝色边缘光在轮廓处碰撞，高饱和的点缀色（如血红色的嘴唇、电蓝色的眼瞳）在黑白画面中刺眼地跳出。无任何网点或中间灰过渡，视觉冲击极强。整体感受如同精神崩溃瞬间的闪光灯记忆——破碎、刺眼、无法直视但又无法移开视线。

### 4.6 四纪元对比测试矩阵

为确保同一角色在四纪元中的视觉一致性（身份一致但氛围不同），每个角色需完成以下测试矩阵：

| 测试项 | C纪元 | K纪元 | J纪元 | T纪元 |
|--------|-------|-------|-------|-------|
| 练习服正面立绘 | 1张 | 1张 | 1张 | 1张 |
| 舞台服正面立绘 | 1张 | 1张 | 1张 | 1张 |
| 便装正面立绘 | 1张 | 1张 | 1张 | 1张 |
| 中性表情特写 | 1张 | 1张 | 1张 | 1张 |

每角色16张测试图，10角色共160张。这批测试图同时用于验证角色LoRA在四纪元下的一致性表现。测试图全部使用相同的角色LoRA权重和IP-Adapter参考图，仅改变prompt中的场景/服装/光照描述和Shader处理参数。

---

## 5 微表情系统生成方案

### 5.1 六种关键帧定义

| 关键帧编号 | 名称 | 描述 | 用途 |
|-----------|------|------|------|
| F01 | 中性 | 面部放松，嘴唇微闭，眼神平静 | 默认状态/待机 |
| F02 | 微笑 | 嘴角轻微上扬，眼角微皱（Duchenne smile） | 开心/掩饰 |
| F03 | 愤怒 | 眉头紧锁，嘴唇抿紧，鼻翼扩张 | 冲突/对峙 |
| F04 | 悲伤 | 眉尾下垂，眼睑微垂，嘴角轻微下拉 | 失落/绝望 |
| F05 | 惊讶 | 眉毛上扬，眼睛微睁，嘴唇微张 | 突发事件 |
| F06 | 恐惧 | 瞳孔收缩，眉毛上扬且靠拢，嘴唇颤抖 | 恐惧/崩溃 |

### 5.2 生成策略

微表情的生成难点在于：表情变化幅度小（"微"表情），需要精确控制五官的微小位移，同时保持角色身份不变。采用以下三层控制策略：

**第一层：ControlNet面部关键点**

使用OpenPose的face landmark模式，为每种表情预制一套面部关键点模板。6种表情对应6套关键点坐标文件（JSON格式），在ComfyUI中作为ControlNet输入。关键点模板控制眉毛、眼睛、嘴唇的大致位置和形状，是表情的骨架。

面部关键点模板制作方法：用DreamShaper XL生成一张每种表情的"标准图"，使用OpenPose提取器从中提取关键点坐标，人工微调后保存为模板。这6套模板可被所有10个角色复用。

**第二层：prompt描述精确化**

在prompt中用精确的描述词指定微表情的细节：

```
# 六种微表情的prompt片段
F01 中性: "neutral expression, relaxed face, closed lips, calm eyes, looking at viewer"
F02 微笑: "slight smile, corners of mouth slightly raised, subtle crow's feet, gentle eyes"
F03 愤怒: "suppressed anger, furrowed brows, pressed lips, flared nostrils, intense gaze"
F04 悲伤: "subtle sadness, drooping outer eyebrows, lowered eyelids, corners of mouth slightly down"
F05 惊讶: "mild surprise, raised eyebrows, slightly widened eyes, parted lips"
F06 恐惧: "fear, constricted pupils, eyebrows raised and drawn together, trembling lips"
```

**第三层：IP-Adapter面部锁定**

使用IP-Adapter plus face输入角色的"标准面孔图"（F01中性表情生成的最佳图），权重0.3-0.4。低权重确保面部身份一致但允许表情变化——权重过高会把表情也锁定为中性。

### 5.3 批量生成流程

```yaml
# 微表情批量生成工作流 — ComfyUI JSON结构概要
workflow:
  name: "character_expression_batch"
  
  inputs:
    character_lora: "characters/kof_char_XX_v1.safetensors"
    style_lora: "styles/webtoon_dark_v1.safetensors"
    reference_face: "approved/kof_char_XX_f01_best.png"  # F01最佳图作为面部参考
    expression_templates:  # 6套面部关键点
      - "expressions/neutral.json"
      - "expressions/smile.json"
      - "expressions/anger.json"
      - "expressions/sadness.json"
      - "expressions/surprise.json"
      - "expressions/fear.json"
  
  base_config:
    model: "dreamshaperXL_v21.safetensors"
    width: 512
    height: 512       # 微表情只需面部特写, 降低分辨率提速
    steps: 25
    cfg_scale: 7.0
    sampler: dpmpp_2m
    scheduler: karras
  
  lora_weights:
    style: 0.6
    character: 0.80    # 角色LoRA略降, 给表情更多变化空间
  
  ipadapter:
    model: "ip-adapter-plus-face_sdxl_vit-h.safetensors"
    weight: 0.35
    image: "$reference_face"
  
  controlnet:
    model: "controlnet-openpose-sdxl-1.0.pth"
    weight: 0.6
    # 依次加载6个表情模板
    
  batch:
    expressions: [F01, F02, F03, F04, F05, F06]
    samples_per_expression: 4  # 每种表情生成4张候选
    total_output: 24           # 每角色24张, 人工筛选每表情1张
```

### 5.4 产出规格与验收标准

每个角色的微表情系统包含6张定稿图：

| 验收项 | 标准 |
|--------|------|
| 角色一致性 | 6张图的面部可辨识为同一角色（评分≥4/5） |
| 表情辨识度 | 6种表情可被3人以上独立正确识别（盲测） |
| 表情幅度 | "微"表情——变化幅度不超过中性状态的30%，避免夸张 |
| 分辨率 | 512x512（面部特写），后期可放大至1024x1024 |
| 透明背景 | 使用rembg或人工抠图，输出PNG透明背景 |
| 命名规范 | `kof_char_XX_F0Y.png`（XX=角色编号, Y=表情编号） |

10角色 x 6表情 = 60张微表情图，每张需4张候选 = 240张SD生成。在RTX 4070 Ti Super上，512x512分辨率单张约8-10秒，240张约需30-40分钟。

---

## 6 周更内容生产管线

### 6.1 管线概述

游戏采用周更模式，每周需要产出新的角色立绘变体、场景背景、事件CG等内容。生产管线设计为"模板驱动 + AI生成 + 人工抽检"的三段式流程，将单次周更的美术制作时间压缩到1-2个工作日。

### 6.2 周更内容类型与数量

| 内容类型 | 每周数量 | 分辨率 | 说明 |
|----------|----------|--------|------|
| 角色立绘变体 | 3-5张 | 832x1216 | 新服装/新姿态/新场景中的角色 |
| 场景背景 | 2-3张 | 1024x576 | 新场景/新时段的环境图 |
| 事件CG | 1-2张 | 1024x576 | 关键剧情节点的插画 |
| 微表情补充 | 0-2张 | 512x512 | 新增表情或修正旧表情 |
| **合计** | **6-12张** | | |

### 6.3 三段式生产流程

#### 6.3.1 段一：模板准备（周一，0.5天）

每周更新前，美术负责人根据剧情需求填写"周更美术需求表"：

```markdown
## 第XX周美术需求表

### 角色立绘变体
| 序号 | 角色 | 服装 | 场景 | 纪元 | 姿态 | 备注 |
|------|------|------|------|------|------|------|
| 1 | kof_char_03 | 舞台服 | 舞台后台 | K | 坐姿, 手持麦克风 | 新增剧情: 首次登台 |
| 2 | kof_char_07 | 便装 | 天台 | C | 背靠栏杆, 仰头 | 新增剧情: 天台对话 |

### 场景背景
| 序号 | 场景描述 | 纪元 | 时段 | 参考风格 |
|------|----------|------|------|----------|
| 1 | 舞台后台走廊 | K | 傍晚 | 参考webtoon第47话走廊场景 |
| 2 | 天台夜景 | C | 深夜 | 冷灰, 远处城市灯光 |

### 事件CG
| 序号 | 场景描述 | 出场角色 | 构图要求 |
|------|----------|----------|----------|
| 1 | 角色对峙 | kof_char_03, kof_char_07 | 双人对峙, 正反打 |
```

根据需求表，从prompt模板库中选取对应模板，填入具体参数。prompt模板库按内容类型分类存储，每个模板包含基础prompt框架、LoRA配置、ControlNet配置等预设值。

#### 6.3.2 段二：AI批量生成（周一下午-周二，0.5-1天）

使用ComfyUI的批量生成功能，按照需求表逐项生成：

```yaml
# 周更批量生成工作流配置
batch_config:
  tasks:
    - name: "kof_char_03_stage_backstage_K"
      template: "character_base"
      overrides:
        prompt_add: "sitting on chair, holding microphone, stage backstage"
        character_lora: "characters/kof_char_03_v1.safetensors"
        outfit: "stage costume, sequined dress"
        controlnet_pose: "poses/sitting_chair.json"
        era: "K"  # 触发K纪元SD端参数
      samples: 8  # 生成8张候选
      
    - name: "kof_char_07_rooftop_C"
      template: "character_base"
      overrides:
        prompt_add: "leaning on railing, looking up at sky, rooftop night"
        character_lora: "characters/kof_char_07_v1.safetensors"
        outfit: "casual clothes, hoodie"
        controlnet_pose: "poses/leaning_railing.json"
        era: "C"
      samples: 8
    
    # ... 更多任务
  
  post_process:
    - upscale: true      # 对候选图进行2x放大
    - face_fix: true     # 使用CodeFormer进行面部修复
    - auto_tag: true     # 自动标记生成参数到文件名
```

生成耗时估计：每张832x1216图约15-20秒（含LoRA加载），8张候选约2-3分钟。每周6-12项任务，每项8张候选 = 48-96张生成，总耗时约15-30分钟。加上后处理（放大+面部修复），总AI生成时间约1-2小时。

#### 6.3.3 段三：人工抽检与定稿（周二下午-周三，0.5-1天）

**抽检流程**：

```
步骤1: 快速浏览 (10分钟)
├── 从8张候选中快速排除明显异常图（面部崩坏/手指畸形/构图错误）
├── 保留3-4张进入精细筛选
└── 废图移入rejected/目录

步骤2: 精细对比 (20-30分钟/角色)
├── 将保留的3-4张与该角色的"标准参考图"并排对比
├── 检查维度:
│   ├── 面部一致性 (五官比例/脸型/发型)
│   ├── 服装准确性 (是否匹配需求表描述)
│   ├── 姿态自然度 (关节/肢体是否合理)
│   ├── 画风一致性 (是否保持webtoon风格)
│   └── 情绪表达 (表情/肢体语言是否传达正确情绪)
├── 选出1张最佳图
└── 如全部不达标, 标记"需重新生成", 调整prompt后回到段二

步骤3: 后处理 (15-20分钟/张)
├── 人工修图: 修复小瑕疵 (PS/Photopea)
│   ├── 手指修复
│   ├── 发丝整理
│   ├── 服装细节修正
│   └── 背景杂物清除
├── 抠图/分图层 (如需)
│   ├── 角色层 (透明背景PNG)
│   └── 背景层 (单独PNG)
├── 导出最终规格:
│   ├── 角色立绘: 832x1216 PNG, 透明背景
│   ├── 场景背景: 1024x576 PNG
│   └── 事件CG: 1024x576 PNG
└── 移入approved/目录, 更新资产清单

步骤4: 资产入库 (10分钟)
├── 按命名规范重命名
├── 填写资产元数据 (角色/纪元/服装/场景/版本)
├── 上传至项目资产库
└── 通知Phaser.js前端团队新资产可用
```

**抽检质量标准**：

| 检查项 | 通过标准 | 不通过处理 |
|--------|----------|------------|
| 面部一致性 | 与参考图相似度≥85%（人工判断） | 重新生成, 提高角色LoRA权重+0.05 |
| 手部完整度 | 无多余/缺失手指, 无畸形 | 重新生成或Inpainting修复手部 |
| 画风一致性 | 符合webtoon暗黑风格, 无风格漂移 | 重新生成, 提高画风LoRA权重+0.05 |
| 服装匹配 | 服装类型/颜色/细节与需求表一致 | 重新生成, 细化prompt中的服装描述 |
| 整体完成度 | 无明显AI生成痕迹(水印/重复纹理/不合理光影) | 重新生成或人工修图 |

### 6.4 prompt模板库管理

建立结构化的prompt模板库，按内容类型分类：

```
prompt_templates/
├── character/
│   ├── base_standing.json          # 站立基础模板
│   ├── base_sitting.json           # 坐姿基础模板
│   ├── base_action.json            # 动作模板
│   └── base_closeup.json           # 面部特写模板
├── scene/
│   ├── indoor_corridor.json        # 室内走廊
│   ├── indoor_room.json            # 室内房间
│   ├── outdoor_street.json         # 室外街道
│   ├── outdoor_rooftop.json        # 室外天台
│   └── outdoor_stage.json          # 室外舞台
├── event/
│   ├── confrontation.json          # 对峙场景
│   ├── group_gathering.json        # 群像场景
│   └── solo_dramatic.json          # 单人戏剧场景
└── era_modifiers/
    ├── era_C.json                  # C纪元修饰词
    ├── era_K.json                  # K纪元修饰词
    ├── era_J.json                  # J纪元修饰词
    └── era_T.json                  # T纪元修饰词
```

每个模板文件包含：基础prompt、负面prompt、推荐CFG/steps/sampler、LoRA配置建议、ControlNet配置建议。周更时只需在模板基础上添加具体描述词和角色/场景参数。

---

## 7 成本估算

### 7.1 成本结构

项目美术总预算约1.2万元人民币，全部用于本地部署的硬件与运营成本，零API调用费用。成本分为一次性投入和持续运营两部分。

### 7.2 一次性硬件投入

| 项目 | 型号/规格 | 预估价格(元) | 说明 |
|------|-----------|-------------|------|
| GPU | RTX 4070 Ti Super 16GB | 6,500-7,000 | 推理+训练兼顾, 16GB VRAM可训练SDXL LoRA |
| 内存 | 32GB DDR5 (如不足则升级) | 400-600 | SDXL推理+训练需大内存 |
| 存储 | 2TB NVMe SSD | 500-700 | 模型文件+训练数据+输出管理 |
| 电源 | 750W 80+Gold (如不足则升级) | 300-400 | GPU满载功耗约285W |
| **小计** | | **7,700-8,700** | |

如果开发机已有合适的GPU（如RTX 4070 Ti Super 16GB或更高），则此项成本可大幅降低，仅计算增量升级费用。

### 7.3 模型与软件成本

| 项目 | 来源 | 费用 |
|------|------|------|
| DreamShaper XL | Civitai免费下载 | 0 |
| CounterfeitXL | Civitai免费下载 | 0 |
| MeinaMix v11 | Civitai免费下载 | 0 |
| ComfyUI | 开源(GitHub) | 0 |
| kohya_ss | 开源(GitHub) | 0 |
| ControlNet模型 | Civitai/HuggingFace免费下载 | 0 |
| IP-Adapter模型 | HuggingFace免费下载 | 0 |
| xformers | 开源 | 0 |
| Photoshop/Photopea | Photopea免费 / PS订阅 | 0 / 详见下方 |
| **小计** | | **0** |

图像后处理使用Photopea（免费在线版PS替代）即可满足需求。如团队已有PS许可证则直接使用，不额外计入预算。

### 7.4 持续运营成本（月度）

| 项目 | 计算方式 | 月费用(元) |
|------|----------|-----------|
| 电费 | GPU满载300W x 4h/天 x 30天 x 0.6元/kWh | 约22 |
| 电费(训练月) | GPU满载300W x 8h/天 x 30天 x 0.6元/kWh | 约43 |
| 网络/存储 | 现有宽带, 无增量 | 0 |
| **小计(常规月)** | | **约22** |
| **小计(训练月)** | | **约43** |

### 7.5 预算分配总表

| 类别 | 金额(元) | 占比 | 说明 |
|------|----------|------|------|
| GPU硬件 | 7,000 | 58.3% | RTX 4070 Ti Super 16GB |
| 其他硬件升级 | 1,200 | 10.0% | 内存/SSD/电源按需升级 |
| 模型/软件 | 0 | 0% | 全部使用开源/免费资源 |
| 运营电费(6个月) | 200 | 1.7% | 月均约33元 |
| overlay素材制作 | 500 | 4.2% | K纪元拟声词气泡/泪滴等手绘素材 |
| 应急/微调储备 | 3,100 | 25.8% | 预留给LoRA训练失败的重训/硬件故障维修/额外SSD扩容 |
| **总计** | **12,000** | **100%** | |

### 7.6 零API成本验证

| 验证项 | 状态 | 说明 |
|--------|------|------|
| 图像生成 | 本地ComfyUI | 全部SD推理在本地GPU完成, 无API调用 |
| LoRA训练 | 本地kohya_ss | 全部训练在本地GPU完成, 无云端租用 |
| 图像后处理 | 本地Photopea/PS | 无在线API |
| 超分辨率 | 本地ComfyUI (latent upscale) | 无在线API |
| 面部修复 | 本地CodeFormer | 无在线API |
| 抠图 | 本地rembg | 无在线API |

全部流程在本地完成，无任何按次/按月付费的API调用，满足零API成本约束。

### 7.7 产能估算

在RTX 4070 Ti Super 16GB本地环境下，各类操作的预估耗时：

| 操作 | 单张耗时 | 日产能(4小时GPU时间) |
|------|----------|---------------------|
| 角色立绘生成(832x1216, 30步) | 15-20秒 | 约720-960张 |
| 场景背景生成(1024x576, 30步) | 10-15秒 | 约960-1440张 |
| 微表情生成(512x512, 25步) | 8-10秒 | 约1440-1800张 |
| 超分辨率放大(2x) | 20-30秒 | 约480-720张 |
| LoRA训练(100张, 15epoch) | 4-6小时 | 1次训练/天 |
| 批量生成(8候选/任务) | 2-3分钟/任务 | 约80-120任务 |

周更需求6-12张定稿图（每张8张候选 = 48-96张生成），在30分钟内可完成AI生成部分。瓶颈在人工抽检和后处理，这也是将周更周期设为2天的原因——第1天AI生成+初筛，第2天精修+入库。

---

## 8 附录：ComfyUI工作流速查

### 8.1 角色立绘标准工作流节点链

```
[Load Checkpoint: DreamShaper XL]
    ├── [Load LoRA: webtoon_dark_v1] (weight: 0.6-0.8)
    │       └── [Load LoRA: kof_char_XX_v1] (weight: 0.7-0.9)
    │               ├── → CLIP Text Encode (Positive)
    │               └── → CLIP Text Encode (Negative)
    ├── [Empty Latent: 832x1216]
    │       └── [KSampler: dpmpp_2m, karras, 30 steps, cfg 7.0]
    │               └── [VAE Decode]
    │                       └── [Save Image]
    ├── [Load IPAdapter Model: plus-face_sdxl]
    │       └── [Load Image: reference_face.png]
    │               └── [Apply IPAdapter] → 注入KSampler
    └── [Load ControlNet: openpose-sdxl]
            └── [Load Image: pose_reference.png]
                    └── [Apply ControlNet] → 注入KSampler
```

### 8.2 关键参数速查表

| 参数 | 角色立绘 | 场景背景 | 微表情 | 事件CG |
|------|----------|----------|--------|--------|
| 分辨率 | 832x1216 | 1024x576 | 512x512 | 1024x576 |
| Steps | 30 | 30 | 25 | 32 |
| CFG | 7.0 | 7.5 | 7.0 | 8.0 |
| Sampler | dpmpp_2m | dpmpp_2m | dpmpp_2m | dpmpp_2m |
| Scheduler | karras | karras | karras | karras |
| 画风LoRA权重 | 0.6-0.8 | 0.7-0.8 | 0.6 | 0.7-0.8 |
| 角色LoRA权重 | 0.7-0.9 | N/A | 0.75-0.85 | 0.7-0.9 |
| IP-Adapter权重 | 0.35-0.45 | N/A | 0.30-0.40 | 0.35-0.45 |
| ControlNet权重 | 0.5-0.6 | 0.3-0.5 | 0.5-0.7 | 0.5-0.6 |

### 8.3 四纪元SD端参数差异速查

| 参数 | C纪元 | K纪元 | J纪元 | T纪元 |
|------|-------|-------|-------|-------|
| CFG | 7.5 | 6.5 | 5.5 | 8.5 |
| Steps | 30 | 28 | 25 | 32 |
| 画风LoRA权重 | 0.7 | 0.6 | 0.5 | 0.8 |
| prompt核心方向 | cold, desaturated, harsh light | warm, soft, emotional | minimal, misty, negative space | extreme contrast, multi-light, chaotic |
| negative核心方向 | warm, bright, cute | cold, harsh, contrast | busy, detailed, contrast | soft, gradient, calm |
