# Issue: 内容与底部 foot 重叠 - Bug Report

## 问题描述

使用 Magazine Web PPT skill 生成包含密集内容的页面（如数据页、pipeline 页、对比页）时，页面底部内容会被版本信息、页码等 `.foot` 元素遮挡或重叠，导致显示不完整。

## 复现步骤

1. 使用 Layout 3（数据大字报）生成包含 6 个数据卡片的页面
2. 或使用 Layout 6（Pipeline）生成包含 2-3 个 pipeline-section 的页面
3. 或使用 Layout 9（对比页）生成左右两列列表的页面
4. 在浏览器中查看，底部内容被 foot 元素遮挡

## 影响范围

- Page 2: 数据页（grid-6, 3×2 布局）
- Page 3: 核心能力页（2 个 pipeline-section）
- Page 4: 工作流页（3 个 pipeline-section）
- Page 6: 对比页（左右两列列表）

## 根本原因

`template.html` 中的 `.frame` 样式缺少 `padding-bottom`，导致内容容器会一直延伸到页面底部。当 `.foot` 使用 `margin-top: auto` 推到底部时，已经和内容重叠了。

```css
/* 旧版本 - 有问题 */
.frame {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 0;
  /* 缺少 padding-bottom */
}
```

## 修复方案

### 方案 1: CSS 修改（推荐）

修改 `assets/template.html` 中的 `.frame` 样式：

```css
.frame {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 0;
  padding-bottom: 6vh;  /* 新增：预留底部空间给 .foot */
}
```

**优点**：
- 一劳永逸，所有页面自动生效
- 不需要每页手动添加 padding
- 符合"设计先行"原则

### 方案 2: 文档说明（备选）

在 `references/layouts.md` 和 `references/checklist.md` 中添加说明，提醒生成密集内容页时需要手动添加 `padding-bottom: 6vh`。

**缺点**：
- 容易遗漏
- 每次生成都要手动检查
- 不符合 skill "自动化" 的目标

## 已完成的修改

### 1. `assets/template.html`
```diff
.frame {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 0;
+ padding-bottom: 6vh;
}
```

### 2. `references/checklist.md`
- 更新"问题 1：内容与底部 foot 重叠"章节
- 标明 v0.11+ 已默认修复
- 说明特殊情况（内容特别密集时）可增加到 8vh

### 3. `SKILL.md`
- 添加 `version: 0.11` 元数据
- 新增"版本历史"章节
- 记录本次修复的详细信息

## 测试结果

已生成完整的 8 页 PPT，包含：
- 数据页（6 个卡片）
- 核心能力页（2 个 pipeline）
- 工作流页（2 个 pipeline）
- 对比页（左右列表）

所有页面内容完整显示，无重叠现象。

## 版本号建议

建议发布为 **v0.11**，符合 semver 规范（bug fix，无 breaking change）

## 附加信息

- 发现者：小云 (Hermes Agent)
- 发现日期：2026-04-24
- 修复方法：CSS 默认添加 padding-bottom
- 兼容性：向后兼容，无 breaking change
