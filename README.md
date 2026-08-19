<p align="center">
  <img src="./assets/svg-maker图标_260817.svg" alt="Logo" width="145"><br>
  <h1 align="center">SVG Maker</h1>
</p>

<p align="center">
  <a href="./LICENSE"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Release-v0.2.0-green.svg" alt="Release">
  <img src="https://img.shields.io/badge/Platform-Trae%2FCodex-lightgrey.svg" alt="Platform">
</p>

<p align="center"><em>"A line is a dot that went for a walk."<br>—— Paul Klee</em></p>

## 简介

**⭐️功能介绍**：专业 SVG 矢量图生成 Skill，支持两类输入：

- **文字 → SVG**：模糊需求先联网检索视觉参考并与用户确认；再经"场景扩展 → 对象分解 → 布局规划 → 技术规范"四层结构化提示词驱动生成
- **图片 → SVG**：针对视觉模型"单遍扫读丢细节、绝对坐标不准"的弱点，采用多遍分区读取、文字化转写、相对坐标推算与渲染对照修正，实现高质量复刻

两条工作流均以基础几何形状组合优先，产出简洁、可读、可编辑、可在主流浏览器直接渲染的 SVG。

**⭐️如何开始**

- 下载仓库根目录（或 Release）中的 `svg-maker.skill` 文件，安装后即可使用
- Skill 本体为单一 `SKILL.md`，按需求类型路由至 `references/` 下的工作流文档，无需额外配置

**⭐️Trigger**

- "帮我生成 SVG" / "画一个 SVG" / "制作矢量图" / "制作svg"
- "create an SVG" / "draw SVG" 或类似表达
- 直接发送图片并要求转为 SVG

## 工作流程

| 阶段 | 说明 |
|------|------|
| **需求分类** | 区分文字输入需求与图片输入需求，路由至对应工作流 |
| **文字工作流** | 需求分类（模糊/明确）→ 模糊需求五维关键词 WebSearch 并与用户确认 → 四层结构化提示词 → 代码生成 |
| **图片工作流** | 多遍分区读取转写元素清单（文字与公式优先）→ 四层提示词（相对坐标推算，禁止像素直觉）→ 代码生成 |
| **强制检查** | 图意与流程 / 线条与箭头（正交连线）/ 公式（tspan 上下标，禁 LaTeX 原文）/ 中文乱码与跑偏 |
| **输出与迭代** | 保存为 `<主体描述>_<日期>.svg`；用户反馈时针对性修改单一问题，不整体重绘 |

## 其他信息

- 方法参考：
  - [Chat2SVG](https://github.com/kingnobro/Chat2SVG/tree/main)：三阶段生成（提示词扩展 → 代码生成 → 视觉修正），提示词参考见 `assets/prompts.yaml`
  - [SVGThinker](https://arxiv.org/abs/2509.24299)：推理过程与 SVG 顺序渲染对齐、紧凑基元使用，论文笔记见 `assets/SVGthinker.md`
- 目录说明：`references/InputWoImg.md` 文字输入工作流、`references/InputWithImg.md` 图片输入工作流、`references/SearchPrompts.md` 检索关键词与返回内容规范；`assets/` 存放方法论参考资料
- 未来更新计划详见 [ROADMAP](./ROADMAP.md)
