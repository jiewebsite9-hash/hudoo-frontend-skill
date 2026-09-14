---
name: hudoo-frontend
description: 互旦（HUDOO）外贸企业官网固定开发手法，提炼自 E:\网站前端项目 下 20+ 个真实项目。只要用户要新建/修改/仿写企业官网、外贸站、营销落地页的静态 HTML 页面，或提到"按我的开发习惯/套路/模板来做"、加页面区块、做轮播/弹窗/询盘表单/滚动动画、给页面做响应式，甚至只是让你在这些项目目录里改代码，都必须先使用本 skill。核心约束：与设计稿 1:1 还原、纯静态 HTML+CSS+jQuery、禁止任何前端框架（Vue/React/Bootstrap/Tailwind 一律不允许）、库全部本地引入。
---

# 互旦外贸官网固定开发手法

这套手法服务于一个明确场景：**为 dolphingoo-cms 套版的纯静态多页企业官网/外贸站**。前端交付的是静态 HTML 模板稿，后续由 CMS 接管内容填充。所有约定都围绕"几十个站共用一套骨架、换皮即成新站"这个目标，因此**一致性比"更现代的写法"更重要**——不要擅自"优化"掉任何约定。

## 硬性规则（任何时候不可违背）

1. **与设计稿 1:1 还原**，尺寸、间距、字号照稿写。
2. **响应式**：桌面优先，固定 5 断点（见下），≤768px 移动端单位整体切 `vw`。
3. **只用原生 HTML + CSS + JS + jQuery**。
4. **禁止任何前端框架和构建工具**：不用 Vue/React/Bootstrap/Tailwind/Sass/npm/webpack。
5. **所有库本地引入，零 CDN**（例外：Google Fonts、YouTube embed、ShareThis、WhatsApp 链接）。

## 技术栈（锁定版本，不升级）

| 库 | 版本/文件 | 用途 |
|---|---|---|
| jQuery | `js/jquery-1.9.1.min.js` | 所有交互的基础 |
| Swiper | 4.4.2 `js/swiper.min.js` + `css/swiper.min.css` | 所有轮播（注意是 4.x API：`.swiper-container`） |
| WOW.js + animate.css | `js/wow.min.js` + `css/animate.min.css` | 滚动入场动画 |
| CountUp + Waypoints | `js/jquery.countup.min.js` + `js/jquery.waypoints.min.js` | 数字滚动计数 |
| GSAP + ScrollTrigger | `js/gsap.min.js` + `js/ScrollTrigger.min.js` | 仅重型滚动叙事（pin/scrub/序列帧/SVG 描边）时加入 |

`<head>` 固定引入顺序（CSS 先第三方后自研，JS 全放 `<head>`，靠 `$(function(){})` 兜 DOM ready）：

```html
<link rel="stylesheet" href="css/animate.min.css"/>
<link rel="stylesheet" href="css/swiper.min.css"/>
<link rel="stylesheet" href="css/reset.css"/>
<link rel="stylesheet" href="css/style.css"/>
<script src="js/jquery-1.9.1.min.js"></script>
<script src="js/jquery.countup.min.js"></script>
<script src="js/jquery.waypoints.min.js"></script>
<script src="js/swiper.min.js"></script>
<script src="js/wow.min.js"></script>
<script src="js/public.js"></script>
<script src="js/style.js"></script>
```

## 目录与文件约定

```
项目根/
├── index.html、about.html、product_list.html、product_detial.html、
│   news_list.html、news_detial.html、contact.html、faq.html、
│   download.html、video.html、search.html、priacy-policy.html、
│   404.html、coming.html
├── css/   reset.css + style.css + animate.min.css + swiper.min.css + fonts/(本地ttf)
├── js/    public.js(跨页公共交互) + style.js(首页/头部+Swiper初始化) + 各库
└── images/  命名 = 页面_区块_元素+序号，如 about_culture_ico1.png、crumbs_home.png
```

**⚠ 固定拼写，不许"纠正"**：`detial`（不是 detail）、`priacy-policy`（不是 privacy）、`nav_frist_link`（不是 first）。这些是贯穿全部项目与 CMS 套版的既定约定，改了会导致新旧页面、CSS 类、图片名互不兼容。

**文件分工**：
- `reset.css` = 压缩 reset + 原子工具类 + `@font-face` + `:root` 变量 + 容器类 + **全站公共组件样式**（banner/面包屑/弹窗/悬浮件/news/faq/download/contact 等 `public_` 区块）。跨项目基本不动。
- `style.css` = header + 本站业务区块样式，按 `/*header start*/ ... /*footer end*/` 注释分段。
- `public.js` = 表单校验、弹窗、悬浮件、FAQ、scroll-spy 等跨页逻辑；`style.js` = 吸顶导航、汉堡菜单、Tab、全部 Swiper 实例。

## 核心约定速查

- **主题色单变量换肤**：`:root{ --mainColor:#XXX; }`（可选 `--otherColor` 辅色），全站颜色引用变量，换项目只改这一行。
- **容器三件套**：`.contentWH1600{width:83.33%}`、`.contentWH1400{width:73.33%}`（最常用）、`.contentWH1200{width:1200px}`，均 `margin:0 auto`。
- **断点固定 5 档**（全部 max-width）：`1768 / 1400 / 1200 / 990 / 768`。990 导航切全屏抽屉；768 以下字号/间距/尺寸全换 `vw`（如 `font-size:3.8vw; padding:8vw 0`）。**不用 rem**。
- **类命名**：全小写下划线语义长链，按 DOM 层级逐级拼（`index_product_content_list_sw_img`）；首页区块 `index_` 前缀，跨页复用区块 `public_` 前缀；状态类固定 `.on / .active / .fixed / .inputon`；几乎不用 id。
- **HTML 注释分区**：`<!--header start-->…<!--header end-->`、banner、content、footer、right online、public inquire lightbox。
- **可点击条目用 `<a>` 包整卡，不用 `<div>`**：产品卡、案例卡、新闻卡、解决方案项这类"点一下要跳详情/列表"的整块，外层容器一律 `<a href=...>`（配 `display:block`），而不是只给里面的"Explore More"加链接、外层留 `<div>`。关系到可点击区域、SEO、语义。轮播 slide 里的卡同理（`.swiper-slide > a.xxx_card`）。
- **含 Swiper 的 Tab 切换**：每个 tab 一个独立 Swiper，切换 list 用 `height:0/auto` + `overflow:hidden`（**绝不 `display:none`**，否则 Swiper 测不到宽度失效），切后 `.update()`；左右翻页按钮别放进这个 `overflow:hidden` 的 list（会被裁一半），移到 swiper 盒子外（随 tab 显隐）或用 padding 撑开。详见 js-patterns.md。
- **header「三件套」必补齐**（设计稿静态图看不出，但是标配）：导航下拉菜单（hover）、小语种下拉（hover，英/德/法/西/阿硬链接）、搜索框（`.header_search_btn` click toggle `.header_search_box.on`）。首级样式/hover 变色只限定 `.nav_frist_link`，别波及下拉里的 `.nav_two_link`。详见 html-skeleton.md header 要点、js-patterns.md。
- **有链接跳转的卡片/项用 hover 切换状态，不用 click**：解决方案钻石格、产品卡、案例卡这类 `<a>` 导航入口，激活态（换底色/图标染白/箭头变白）一律 CSS `:hover`；且**默认不给任何一项(含第一项)预设 `.on` 激活态**（设计稿把第一项画成经过态只是演示 hover）。click 只留给 Tab/手风琴/弹窗/汉堡这类就地改视图的控件。详见 js-patterns.md「状态切换：hover 还是 click」。
- **首页 banner 一律用 Swiper 轮播**（`index_banner_swiper`），绝不写死单张静态图——即便设计稿只画一屏 hero，也按可切换多屏搭（≥2 个 `swiper-slide` 占位供 CMS 填多图），配 `loop + autoplay + 切换重放 fadeInUp + 悬停停/启`。只有**内页** banner 才用静态 `.banner > .banner_bg`。骨架见 html-skeleton.md、初始化见 js-patterns.md。
- **图标必须逐个核对真实节点，不臆断、不手画**（犯过 3 次的血泪教训）：绝不按语义猜（号码旁可能是 **WhatsApp** 不是电话）、不按惯例猜颜色（工具图标可能是**品牌红**不是深灰）、不因相似就复用；形状/颜色以导出 SVG 的 `stroke/fill` 为准，拿不准就 `get_screenshot` 那个节点。用 `download_assets` 导真图（位图取 `rawImages[]`；纯矢量导 SVG 后剥掉 `#F5F5F5`/`1920×N`/区块底色三种背景 rect、只留 `id="Vector"` 路径）。交付用 PNG 或清理干净的 `.svg` 文件经 `<img>` 引用（别在 HTML 手写 svg 代码）。同等级列表图标放**等大盒子** + `object-fit:contain`；多色场景存两份不同色 svg。导后必肉眼核对。详见 pages-and-sections.md「从 Figma 取图/图标」。
- **字体**：本地 `.ttf` 按字重拆 `@font-face`，family 名=文件名（`Inter-Bold`），CSS 直接 `font-family:"Inter-Bold"` 指定字重。英文站常用 Inter / Plus Jakarta Sans / Roboto，繁中站用 NotoSansTC。
- **惯用 CSS 值**：`transition:all 0.3s`（全局 `a` 兜底）、卡片阴影 `box-shadow:0 0 21px rgba(4,0,0,0.15)`、圆角 `10px`、多行截断 `-webkit-line-clamp`、图片 `object-fit:cover` + `aspect-ratio`、PNG 图标染白 `filter:brightness(0) invert(1)`。
- **文字换行少用 `<br>`，用宽度/padding 约束**：标题折行给容器或标题设 `max-width`（banner 大标题 `max-width:760px`、卡片小标题 `max-width:130px`）让其在词边界自然折；正文提前折行用 `padding-right:X%`。极少写死固定宽度；`<br>` 仅极个别大标题偶尔用。
- **高度默认 `auto`，不写死 px**：盒子/区块/内容容器一律靠内容或图片撑高。固定/比例高度只留给三类：薄条（顶栏/导航条）、小 UI 原子（按钮/徽标/图标/分隔线/圆点）、需固定裁切比例的图片容器与整幅大图区块（用 `aspect-ratio:W/H` + `object-fit:cover`，如 banner `1920/800`）。别给卡片图写 `height:330px`、别给文字条带写 `height:227px`（改 `padding`）。详见 css-conventions.md「高度」节。
- **SEO 分层**：普通页 `<title></title>` 留空、无 description（CMS 填）；**投广告的落地页**才写满 title/description/keywords/canonical/OG/JSON-LD。viewport 固定 `width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no`。
- **多语言**：多份 HTML + header 静态下拉硬链接（`index.html` / `index-ar.html`），不做 i18n。

## 新项目启动流程

1. 从 `E:\网站前端项目\M-模板` 整包复制 css/js/images 与所需页面（M-模板 无 index.html，首页需按设计稿新建，`style.css`/`style.js` 为首页预留）。
2. 改 `reset.css` 的 `:root{--mainColor}` 为品牌主色；清掉上一项目残留的旧变量/旧字体引用。
3. 换 `@font-face` 字体文件；按行业换 banner 图、图标、文案。
4. **行业适配只动三个旋钮**：主色变量、字体、区块取舍（如招聘站删产品询盘弹窗，机械站加对比滑块）。骨架与交互一律不动。
5. 检查并清理复制残留：上一站的公司名/地址/ICP 号/占位统计数字。

## 详细参考（按需读取）

- 写页面骨架（head/header/banner/面包屑/footer/弹窗/悬浮件/分页的完整 HTML）→ 读 [references/html-skeleton.md](references/html-skeleton.md)
- 写样式（reset.css 结构、变量、断点内具体写法、移动端 vw 换算、惯用片段）→ 读 [references/css-conventions.md](references/css-conventions.md)
- 写交互（吸顶/汉堡/Tab/手风琴/弹窗/表单校验/scroll-spy/Swiper 全部配方/WOW 初始化/GSAP 手法）→ 读 [references/js-patterns.md](references/js-patterns.md)
- 建页面/区块（每种页面的固定区块流、首页纵向流、区块 HTML 套路）→ 读 [references/pages-and-sections.md](references/pages-and-sections.md)
