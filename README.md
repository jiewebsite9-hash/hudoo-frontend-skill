# hudoo-frontend

互旦（HUDOO）外贸企业官网的固定前端开发手法，封装成 Claude Code Skill。

提炼自 20+ 个真实上线项目，服务于一个明确场景：**为 dolphingoo-cms 套版的纯静态多页企业官网 / 外贸站**。前端交付静态 HTML 模板稿，后续由 CMS 接管内容填充。几十个站共用一套骨架、换皮即成新站，因此**一致性优先于「更现代的写法」**。

## 硬性规则

1. 与设计稿 1:1 还原，尺寸、间距、字号照稿写
2. 响应式桌面优先，固定 5 断点；≤768px 移动端单位整体切 `vw`
3. 只用原生 HTML + CSS + JS + jQuery
4. 禁止任何前端框架和构建工具（Vue / React / Bootstrap / Tailwind / Sass / npm / webpack）
5. 所有库本地引入，零 CDN（例外：Google Fonts、YouTube embed、ShareThis、WhatsApp 链接）

## 文件结构

| 文件 | 内容 |
| --- | --- |
| `SKILL.md` | 主文件：硬性规则、锁定版本的技术栈、目录与文件约定、核心约定速查、新项目启动流程 |
| `references/html-skeleton.md` | HTML 骨架模板：header / footer / banner / 面包屑 / 三弹窗 / 悬浮件等公共组件的逐字固定写法 |
| `references/css-conventions.md` | CSS 规范：文件分层、`:root` 变量、5 个断点的职责、`@font-face` 字体约定 |
| `references/js-patterns.md` | JS 交互配方：全部 jQuery 1.9.1 API，轮播 / 弹窗 / 灯箱 / 表单校验 / 滚动动画 |
| `references/pages-and-sections.md` | 页面类型与区块套路：各页面的标准结构与占位数据约定 |

## 安装

克隆到 Claude Code 的全局 skills 目录：

```bash
git clone git@github.com:jiewebsite9-hash/hudoo-frontend-skill.git ~/.claude/skills/hudoo-frontend
```

Windows（Git Bash）：

```bash
git clone git@github.com:jiewebsite9-hash/hudoo-frontend-skill.git "$USERPROFILE/.claude/skills/hudoo-frontend"
```

目录名必须是 `hudoo-frontend`（与 `SKILL.md` 的 `name` 字段一致）。装好后新开会话，Claude Code 会自动加载。

## 触发时机

新建 / 修改 / 仿写企业官网、外贸站、营销落地页的静态 HTML 页面；加页面区块；做轮播、弹窗、询盘表单、滚动动画；给页面做响应式；或在 `E:\网站前端项目` 下的项目目录里改代码。

## 相关

CMS 侧套版手法见 `xunrui-cms-dev` skill（迅睿 CMS / Dolphingoo Cms）。
