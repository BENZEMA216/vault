---
name: prompt-engineering
description: AI 绘图 Prompt 编写技能。当用户需要生成 Midjourney、SD、DALL-E、即梦等 AI 绘图 Prompt 时激活。
tokens: ~700
---

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

## 关键词速查

### 风格

| 类型 | 关键词 |
|------|--------|
| 写实 | photorealistic, hyperrealistic, photography |
| 插画 | illustration, digital art, concept art |
| 动漫 | anime style, manga, cel shading |
| 油画 | oil painting, impasto, brushstrokes |
| 3D | 3D render, octane render, unreal engine |

### 光线

```
golden hour lighting     黄金时段
dramatic rim lighting    轮廓光
soft diffused light      柔和散射
volumetric fog          体积雾
high contrast           高对比
```

### 构图

```
close-up portrait       特写
full body shot          全身
bird's eye view         鸟瞰
low angle               低角度
rule of thirds          三分法
```

### 质量

```
highly detailed, 8k resolution, masterpiece,
best quality, sharp focus, professional
```

## 负面提示

### 通用排除

```
blurry, low quality, distorted, deformed,
bad anatomy, extra limbs, watermark, text
```

### 人物专用

```
bad hands, malformed hands, fused fingers,
too many fingers, disfigured face
```

## 平台参数

### Midjourney

```
--ar 16:9    宽高比
--v 6        版本
--style raw  写实风格
--q 2        质量
--s 500      风格化程度
```

### Stable Diffusion

```
Steps: 30-50
CFG Scale: 7-12
Sampler: DPM++ 2M Karras
```

## 示例

### 电影感人像

```
cinematic portrait of a detective in noir setting,
fedora and trench coat, cigarette smoke,
dramatic side lighting, film grain, 35mm,
moody atmosphere, shallow depth of field
--ar 2:3 --v 6 --style raw
```

### 概念艺术

```
abandoned space station, overgrown with alien flora,
bioluminescent plants, distant nebula through window,
concept art, Greg Rutkowski, volumetric lighting
--ar 21:9 --v 6
```
