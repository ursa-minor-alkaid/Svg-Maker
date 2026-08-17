<p align="center">
  <img src="./assets/svg-maker图标_260817.svg" alt="Logo" width="145"><br>
  <h1 align="center">SVG Maker</h1>
</p>

<p align="center">
  <a href="./LICENSE"><img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Release-v0.1.0-green.svg" alt="Release">
  <img src="https://img.shields.io/badge/Platform-Trae%2FCodex-lightgrey.svg" alt="Platform">
</p>

<p align="center"><em>"A line is a dot that went for a walk."<br>—— Paul Klee</em></p>

## 简介

**⭐️功能介绍**：专业 SVG 矢量图生成 Skill。将用户需求转化为高质量、严谨、符合要求的 SVG 代码——模糊需求先联网检索视觉参考并与用户确认，再经"场景扩展 → 对象分解 → 布局规划 → 技术规范"四层结构化提示词驱动生成；以基础几何形状组合优先，产出简洁、可读、可编辑，可在主流浏览器直接渲染。

**⭐️如何开始**

- 下载仓库根目录（或 Release）中的 `svg-maker.skill` 文件，安装后即可使用
- Skill 本体为单一 `SKILL.md`，模糊需求检索所用的搜索提示词位于 `references/SearchPrompts.md`，无需额外配置

**⭐️Trigger**

- "帮我生成 SVG" / "画一个 SVG" / "制作矢量图" / "制作svg"
- "create an SVG" / "draw SVG" 或类似表达

## 工作流程

| 阶段 | 说明 |
|------|------|
| **需求分类** | 区分模糊需求（仅主体名称）与明确需求（含结构/风格/颜色描述） |
| **模糊需求处理** | 按五维关键词 WebSearch → 向用户展示绘制思路并确认 → 汇总进入规划 |
| **结构化提示词** | 场景级扩展 → 对象级分解（穷尽常识性部件）→ 布局级规划（坐标/绘制顺序/连接关系）→ 技术规范（viewBox/元素集/配色） |
| **代码生成** | 逐条翻译规划：基础形状优先，背景最先绘制，后定义元素位于上层 |
| **生成后自检** | 部件完整 / 连接对齐 / 比例合理 / 叠放正确 |
| **输出与迭代** | 保存为 `<主体描述>_<日期>.svg`；用户反馈时针对性修改单一问题，不整体重绘 |

## 其他信息

- 方法参考：
  - [Chat2SVG](https://github.com/kingnobro/Chat2SVG/tree/main)：三阶段生成（提示词扩展 → 代码生成 → 视觉修正），提示词参考见 `assets/prompts.yaml`
  - [SVGThinker](https://arxiv.org/abs/2509.24299)：推理过程与 SVG 顺序渲染对齐、紧凑基元使用，论文笔记见 `assets/SVGthinker.md`
- `assets/` 存放方法论参考资料，`references/SearchPrompts.md` 为检索阶段供 LLM 使用的搜索关键词生成与返回内容规范
