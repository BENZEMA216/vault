# Creative CoWork — 设计方案

> 设计源文件：`/Users/dongzhe/Downloads/untitled.pen`

---

## V6 — 绿色创意工作台

以 **绿色主题** 为核心视觉语言，通过顶部 Tab 栏组织完整的创作流程。以下通过一个真实案例 —— 伊利牛奶「回家的路」30秒广告 —— 展示完整用户旅程。

---

### 用户故事 1：创建脚本与分镜

> 作为一个广告创意人，我希望在一个界面里同时看到脚本文案和对应的分镜画面，这样我可以直观地检查文字和画面是否匹配。

用户在「脚本」Tab 下工作。左侧是分场景的完整脚本（Scene 1~4），每个场景包含标题、时长和画面描述文案。右侧紧挨着对应场景的 **AI 生成分镜图**，每个场景 2 张参考图。

右侧面板是 **Story Agent**，用户可以针对具体场景对话修改 —— 比如"帮我把 Scene 2 '工厂内部' 的画面改一下，加入流水线特写"。Agent 理解上下文，直接定位到对应场景。

![脚本+分镜一体视图](./sidebar-v6-script-storyboard.png)

---

### 用户故事 2：管理分镜进度

> 作为一个项目负责人，我希望一眼看到所有场景的视觉生成进度，快速找到还没完成的场景并触发生成。

切换到「分镜」Tab，所有场景以网格形式平铺展示。已生成的场景显示 AI 图片缩略图，未完成的显示占位符和「生成视觉」按钮。顶部有进度统计（如 6 scenes, 4 已完成），一目了然。

![分镜网格视图](./sidebar-v6-storyboard-grid.png)

---

### 用户故事 3：输出与交付

> 作为一个广告创意人，我完成了所有场景的制作，现在需要预览最终合成的视频并分享给客户审阅。

切换到「Outputs」Tab，展示最终合成的视频文件。每个视频有缩略图、播放按钮和文件信息（时长、分辨率、大小）。右侧 Agent 面板提供本次 Session 的总结 —— 包括制作了什么、文件已保存的路径、后续可以做的操作建议。右上角 Share 按钮一键分享。

![输出与交付](./sidebar-v6-output-video.png)

---

## 附录：历史版本

<details>
<summary>V4 — 自适应协作布局</summary>

V4 采用 VS Code 风格的 file tree 侧边栏（180px），后被 Tab 切换 + 增强型 File Tree 替代。

![V4 主布局](./sidebar-v4-main-layout.png)

![文件 Tab](./sidebar-tab-file-browser.png)

![工作区 Tab](./sidebar-tab-workspace.png)

![文件树增强对比](./sidebar-filetree-before-after.png)

![增强型文件树](./sidebar-enhanced-filetree.png)

V4 用户故事：

| 阶段 | 截图 |
|------|------|
| S1 分析 Brief | ![S1](./sidebar-s1-brief-analysis.png) |
| S2 脚本创作 | ![S2](./sidebar-s2-script-creation.png) |
| S3 分镜设计 | ![S3](./sidebar-s3-storyboard-design.png) |
| S4 视觉生成 | ![S4](./sidebar-s4-visual-generation.png) |
| S5 音频制作 | ![S5](./sidebar-s5-audio-production.png) |
| S6 合成交付 | ![S6](./sidebar-s6-composition-delivery.png) |

| 帧名 | Node ID |
|------|---------|
| V4 主布局 | `f6xsO` |
| S1~S6 | `5HFNY` `unxMx` `bxgZK` `sk6Oa` `bcMj4` `lKdEy` |
| FileTree 对比 | `ZKnkx` |
| Tab·文件/工作区 | `taW8v` `BWMeD` |
| 增强型 File Tree | `ELBVL` |

</details>

<details>
<summary>V5 — 进度状态探索</summary>

浅色进度流程 + 蓝白分镜布局：

![V5 项目初始化](./sidebar-v5-project-setup.png)

![V5 脚本与分镜](./sidebar-v5-script-storyboard.png)

![V5 视觉生成高亮](./sidebar-v5-visual-gen-highlight.png)

![V5 蓝白分镜布局](./sidebar-v5-blue-storyboard.png)

| 帧名 | Node ID |
|------|---------|
| 项目初始化 | `oV2bI` |
| 脚本与分镜 | `XGC4T` |
| 视觉生成高亮 | `locj7` |
| 蓝白分镜 | `wDAag` |

</details>
