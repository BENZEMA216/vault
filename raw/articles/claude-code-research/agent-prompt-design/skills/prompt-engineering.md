# Skill: prompt-engineering

## SKILL.md 模板

```yaml
---
name: prompt-engineering
description: |
  AI 绘图 Prompt 编写技能。当用户需要生成 Midjourney、Stable Diffusion、
  DALL-E、即梦等 AI 绘图工具的 Prompt 时激活此技能。
---
```

```markdown
# AI 绘图 Prompt 技能

## Prompt 结构

### 通用公式

```
[主体] + [场景/环境] + [风格] + [技术参数]
```

### 详细模板

```
[主体描述], [动作/姿态], [表情/情绪],
[场景/背景], [光线], [氛围],
[艺术风格], [参考艺术家],
[画面构图], [色彩方案],
[渲染质量], [技术参数]
```

## 元素详解

### 1. 主体描述

**人物**：
```
a young woman, long black hair, wearing a white dress
一个年轻女性，黑色长发，穿着白色连衣裙
```

**物体**：
```
an ancient mechanical clock, brass gears, weathered patina
一个古老的机械钟，黄铜齿轮，斑驳的铜绿
```

**场景**：
```
a cyberpunk street market, neon signs, rain-slicked pavement
赛博朋克街头市场，霓虹招牌，被雨淋湿的路面
```

### 2. 风格关键词

| 类型 | 关键词 |
|------|--------|
| 写实 | photorealistic, hyperrealistic, photography |
| 插画 | illustration, digital art, concept art |
| 动漫 | anime style, manga, cel shading |
| 油画 | oil painting, impasto, brushstrokes |
| 水彩 | watercolor, soft edges, color bleeding |
| 3D | 3D render, octane render, unreal engine |
| 复古 | vintage, retro, 80s aesthetic |
| 极简 | minimalist, clean, simple |

### 3. 光线氛围

```
golden hour lighting     黄金时段光线
dramatic rim lighting    戏剧性轮廓光
soft diffused light      柔和散射光
neon glow               霓虹辉光
volumetric fog          体积雾
moody atmosphere        情绪化氛围
high contrast           高对比度
low key lighting        低调照明
```

### 4. 构图参考

```
close-up portrait       特写肖像
full body shot          全身照
bird's eye view         鸟瞰视角
low angle shot          低角度
dutch angle             倾斜构图
symmetrical composition 对称构图
rule of thirds          三分法构图
negative space          留白
```

### 5. 质量增强

```
highly detailed         高度细节
8k resolution           8K分辨率
masterpiece            杰作
best quality           最佳质量
sharp focus            清晰对焦
intricate details      精细细节
professional           专业级
award winning          获奖作品
```

## 负面提示 (Negative Prompt)

### 通用排除

```
blurry, low quality, distorted, deformed,
bad anatomy, extra limbs, missing fingers,
watermark, text, signature, logo,
oversaturated, underexposed
```

### 人物专用排除

```
bad hands, malformed hands, fused fingers,
too many fingers, long neck, cropped head,
duplicate, clone, disfigured face
```

## 平台特化

### Midjourney

```
/imagine prompt: [描述] --ar 16:9 --v 6 --style raw --q 2
```

参数：
- `--ar` 宽高比 (16:9, 9:16, 1:1, 4:3)
- `--v` 版本 (5, 5.2, 6)
- `--style` 风格 (raw=写实)
- `--q` 质量 (0.25, 0.5, 1, 2)
- `--s` 风格化程度 (0-1000)

### Stable Diffusion

```
Positive: [正面描述]
Negative: [负面提示]
Steps: 30-50
CFG Scale: 7-12
Sampler: DPM++ 2M Karras
```

### 即梦 (Dreamina)

```
主体描述，场景描述，风格描述
--比例 16:9
--风格 [写实/动漫/插画]
```

## 示例

### 电影感人像

```
cinematic portrait of a detective in a noir setting,
wearing a fedora and trench coat, cigarette smoke,
rain-streaked window, dramatic side lighting,
film grain, 35mm photography, moody atmosphere,
dark color palette, shallow depth of field
--ar 2:3 --v 6 --style raw
```

### 概念艺术场景

```
abandoned space station interior, overgrown with alien flora,
bioluminescent plants, broken windows showing distant nebula,
concept art style, Greg Rutkowski, environmental storytelling,
volumetric lighting, cinematic composition, highly detailed
--ar 21:9 --v 6
```

### 角色设计

```
character design sheet, female cyberpunk hacker,
multiple poses and expressions, neon blue hair,
tech wear outfit, holographic accessories,
clean white background, anime style,
full color, professional concept art
--ar 3:2 --v 6
```

## 迭代优化

1. **从简单开始**：先用核心描述生成
2. **逐步添加**：根据结果添加细节
3. **调整权重**：重要元素前置或重复
4. **排除干扰**：用负面提示去除不想要的
5. **参数微调**：调整 CFG、steps 等
```
