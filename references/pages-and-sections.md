# 页面类型与区块套路

## 目录

1. [页面类型清单](#页面类型清单)
2. [首页固定纵向流](#首页固定纵向流)
3. [内页统一骨架](#内页统一骨架)
4. [各页面区块构成](#各页面区块构成)
5. [行业适配三旋钮](#行业适配三旋钮)
6. [量产落地页模式](#量产落地页模式)
7. [多语言处理](#多语言处理)
8. [图片规范](#图片规范)
9. [部署配套文件](#部署配套文件)
10. [交付自查清单](#交付自查清单)

## 页面类型清单

| 页面 | 说明 |
|---|---|
| `index.html` | 首页（M-模板 不含，按设计稿新建；样式写 style.css、交互写 style.js） |
| `about.html` | 关于我们 |
| `product_list.html` + `product_detial.html` | 产品列表/详情（列表-详情配对命名） |
| `news_list.html` + `news_detial.html` | 新闻列表/详情 |
| `contact.html` | 联系我们 |
| `faq.html` | 常见问题（手风琴） |
| `download.html` | 下载中心（下载询盘弹窗） |
| `video.html` | 视频中心（YouTube iframe 三栏卡片） |
| `search.html` | 搜索结果页 |
| `priacy-policy.html` | 隐私政策（既定拼写） |
| `404.html` | 404（打字机效果：递归 setTimeout 80ms 逐字 + `@keyframes blink` 光标） |
| `coming.html` | "敬请期待"占位页，未完成栏目链接统一指向它 |

按需增页：`application.html`（应用领域）、`solution.html`、`esg.html`、`join.html`（招聘）、`case.html` 等，命名沿用小写下划线。

## 首页固定纵向流

```
header（两层）
→ banner（index_banner_swiper 全屏轮播：banner_bg 图/视频 + 文案 h2+p+按钮，切换重放 fadeInUp）
→ 产品区（index_title_box 标题 + 产品 Swiper 或多列卡片/tab 切换）
→ 解决方案/应用区（多宫格，hover 切背景大图 + 文字上浮）
→ About 区（左图/视频钮 + 右文 + 底部 .counter 数字统计）
→ Why Choose Us（4 优势图标，2×2 或一行四列）
→ 案例/合作伙伴（缩略图联动 Swiper 或无缝跑马灯）
→ News（三列卡片：图 + 日期 + h3 + p + Learn More）
→ 联系表单区（public_contact_form：左图右表单）
→ footer
→ 悬浮客服 + 返回顶部 + 三询盘弹窗 + 视频灯箱 + 页尾初始化脚本
```

每个区块三层结构：`.index_xxx_box（padding:80px 0，交替灰白底）> .contentWH1400 > 标题 .index_title_box + 内容`。内容元素挂 `wow slideInUp/Left/Right`。

## 内页统一骨架

```
banner（banner_bg 大图 + contentWH1400 内 h1）
→ crumbs_box 面包屑
→ div.content 内若干区块（.xxx_box > .contentWH1400 > .xxx_title h2 + 内容）
→ 与首页相同的公共尾部（悬浮件 + 弹窗 + 初始化脚本）
```

## 各页面区块构成

- **about**：公司简介（左文右图）→ `.counter` 数字统计条 → 企业文化（图标卡片）→ 发展历程（主从 Swiper 时间轴）→ 资质证书（3D coverflow Swiper）→ 合作伙伴（双向跑马灯）。品牌故事区可用 `:after` 巨型描边水印文字。
- **product_list**：左侧多级分类折叠树（`dl/dd` + `.on`，手风琴逻辑）+ 右侧三栏卡片（图 + h2 + p + Inquiry`.inquire`/More 双按钮）→ `.news_page` 分页。
- **product_detial**：上部左主从图 Swiper（thumbs 联动）+ 右信息（h1 品名 + 简介 + `.inquire` 询盘按钮 + ShareThis 分享）→ 吸附锚点导航条（scroll-spy）→ Description / Specifications（table）/ Features / FAQ → Related Products Swiper。
- **news_list**：可带顶部推荐 Swiper → 分类 tab（Product/Company News）→ 列表（图 + 日期 `2025.05.16` + h2 + p + Learn More）→ 分页。
- **news_detial**：正文 + sticky 侧栏（分享 / 留言表单 / 相关新闻）。
- **contact**：三卡片信息（地址/电话/邮箱，图标 + 文字）→ 左图右表单（每项 = label 带红 `*` + 图标 span + input.required + label.tips）。
- **faq**：手风琴列表（`faq_top` h3 + `+/-` 按钮，`faq_bottom` 答案）。
- **download**：两栏卡片（文件名 + 下载按钮 `.download_click` data-url 触发下载询盘弹窗）。
- **video**：三栏卡片，封面 + 播放钮 `.video_click` data-url 打开视频灯箱。

## 行业适配三旋钮

骨架与交互一律不动，换行业只调三处：

1. **主色变量**：机械/科技蓝绿系（`#0a72ff`/`#008438`），中药/文化暖棕金（`#9c6c24`），物流深藏青 + 橙辅色，招聘清爽青蓝 + 渐变。
2. **字体**：英文站 Inter / Plus Jakarta Sans / Lato / Roboto；繁中站 NotoSansTC。
3. **区块取舍**：招聘站删产品询盘弹窗、加 find-job；机械站可加前后对比滑块（twentytwenty/BeerSlider）、产品放大镜（exzoom）；参数大表用 datatables。按需引对应库，其余不动。

## 量产落地页模式

同类落地页（如 google_ads / meta_ads / bing_ads…）复用方式 = **整页复制 → 换文案/图标/强调色**：

- 共用同一套区块前缀骨架（如 `ads_banner_box → ads_value_box → ads_product_box → ads_phase_box → ads_whyus_box`），DOM 结构不变。
- 平台强调色用变体类（`.c2/.c3/.c4`）或内联 style 覆盖，不动主样式。
- 每页 SEO 独立写满：title / description / keywords / canonical / OG / JSON-LD。
- 图标可用 FontAwesome（本地 all.min.css + webfonts/）。

`_seo` 类内容页则各用自己的页面名前缀（`google_seo_`）另写区块，公共件（header/footer/弹窗/悬浮条）照旧复用。

## 多语言处理

- 每种语言一份独立 HTML（`index.html` / `index-ar.html`），header 语言下拉硬链接切换，每页复制一份切换块。不做 i18n 框架。
- 阿语等 RTL 语言按现有惯例只换文字不换方向（如客户明确要求 RTL 镜像，再加 `dir="rtl"` 与覆盖样式，属于超出既有手法的增强）。

## 图片规范

- 命名 = `页面_区块_元素+序号`，与 CSS 类前缀一一对应：`index_why_us_ico1.png`、`about_banner.jpg`、`crumbs_home.png`。
- 格式：照片 `.jpg`，图标/透明 `.png`，可用 `.webp`（若用需确认页面真正引用）。
- 尺寸由 CSS `aspect-ratio + object-fit:cover` 控制，`<img>` 不写 width/height；`alt` 通常留空（CMS 填充）。
- 占位联系数据固定：`+86 13333333333`、`info@123.com`。

## 从 Figma 取图 / 图标（1:1 还原关键，多次翻车后的血泪总结）

### 翻车根因（务必先读）

同一个错误犯过 3 次：分类图标（导出脏 → 改手画 SVG 近似）、顶栏第 3 个（号码旁想当然放电话，实为 **WhatsApp**）、header 搜索/地球（想当然放深灰，实为**红色 `#CB2633`**）。**共同病根：用"推断 / 默认 / 复用"代替了"逐个核对真实节点"**——按语义猜（号码→电话）、按惯例猜（工具图标→深灰）、按相似复用（第 3 个＝第 1 个），而不去读那个节点到底画了什么、什么颜色；导出一脏就降级成手画。

### 箭头专项（最高频、最容易翻车）

箭头满页都是，最容易偷懒统一画成一种 `→`——但设计稿里箭头往往有**好几种样式且各处不同**，必须逐处对着节点导出、别拿一个通用 `→` 糊全站：

- 常见样式：**填充式 `→`**（实心三角头，如钻石格）、**chevron `›`/`‹`**（只有折角，如"了解更多"按钮、轮播翻页）、**圆圈包箭头**（圆环+`›`，如卡片 Explore More）、**细线 `→`**（如 View all）。看着都是"箭头"，形状差很多。
- 颜色随所在底色变：同一个 chevron 常有**白（深底按钮）/ 红（浅底/主色按钮）/ 灰（轮播 `#4B5563`）** 三种——存多份不同色 svg，或红版 svg 在深底上 `filter:brightness(0) invert(1)` 染白；轮播按钮 hover 变红底时箭头同样 filter 染白。
- 别把 `→` 和 `›` 搞混：设计写"更多/下一步"多用 `›`（`M9 18L15 12L9 6`），真正的位移箭头才用 `→`。拿不准就 `get_screenshot` 那个箭头节点放大看。

### 图标铁律（逐条执行，别偷懒）

1. **每个图标都是独立资产，逐个从它自己的设计节点导出并核对**。绝不靠相邻文字/语义推断、绝不因"长得像/同类位置"复用、绝不假设默认颜色。号码旁可能是 WhatsApp；工具图标可能是品牌红。
2. **形状和颜色以导出 SVG 里的 `stroke="…"` / `fill="…"` 为准**，不以"看起来该是什么"为准。拿不准就单独 `get_screenshot` 那个节点看一眼。
3. **绝不手画内联 SVG 去"近似"设计稿图标**。导出脏（带底色）就把导出修干净，不许降级成手画。交付形态用**导出的 PNG**（首选）或**清理干净的 `.svg` 文件**经 `<img src>` 引用（你们项目本就有 `download_ico.svg` 这类 svg 文件）；"少用原生 svg"指的是**别在 HTML 里手写 svg 代码**，不是禁止 svg 文件。
4. 每个图标接入后**对着该节点截图肉眼核对**（形状 + 颜色 + 尺寸）再算完成。

### 具体导出方法

- **位图照片**（banner、产品图、案例图、背景大图、视频缩略图、logo）：`get_design_context` 的 asset URL 直接 curl（全出血照片无透明，渲染版＝源图），或 `download_assets`。
- **图标 = 位图填充**（`download_assets` 返回里有 `rawImages[]`）：取 `rawImages[].url`——带 alpha 的原始源图。**别**用 `get_design_context` 的渲染 URL：它把节点背景烘进去，常得白底白图/带色块废图（三梯分类图标就是位图，踩过）。
- **图标 = 纯矢量**（`rawImages` 为空）：**导 SVG**（`defaultFormat:svg`）最稳。`download_assets` 导出的 SVG 会把祖先背景 rect 一起烘进去——固定是这三个：`fill="#F5F5F5"` 画布底、`1920×N` 页面底、`1920×42`(或区块尺寸) 的区块底色。**只保留真正的图标路径**（Figma 会给 `id="Vector…"`），脚本 `grep -oE '<path id="Vector[^>]*/>'` 抽出来，套上正确 `viewBox` 重组成干净 svg 即可（社媒等要框住字形可用紧凑 viewBox）。别选带圆圈/条底的容器节点。
- **导出后必在灰底 + 深底肉眼验证**（临时 HTML 预览页 + 浏览器截图）。

### 尺寸与换色

- **同等级列表里每个图标宽高必须一致**：等大盒子 `.xxx_ico{width:54px;height:54px}` + `img{width:100%;height:100%;object-fit:contain}`，无论源图长宽比。
- **换色**：单色图标按状态 `filter:brightness(0)`（黑）/ `filter:brightness(0) invert(1)`（白）切换（如钻石激活态染白）；同一图标多色场景（顶栏白 / 页脚红）直接**存两份不同色的 svg 文件**（把路径 `fill/stroke` 改成目标色，如白版 `white`、红版 `#CB2633`）；带品牌色点缀（红十字/红脉冲）的图标**保留原色**，别 filter 掉。

## 部署配套文件

项目根固定携带：`favicon.ico`、`404.html`、`coming.html`、`.htaccess`（可为空占位）、`.user.ini`（宝塔 open_basedir，形如 `/www/wwwroot/hXXXX.dolphingoo.com/`）。部署环境为宝塔 + nginx，测试子域 `hXXXX.dolphingoo.com`。

## 交付自查清单

新站完成前逐项检查（都是历史项目踩过的坑）：

- [ ] `:root` 里上一站残留的旧色值/旧渐变/用不到的变量已清理
- [ ] 上一站复制残留已清理：公司名、地址、ICP 号、行业占位文案（如轴承数字出现在污水站）、footer 信息
- [ ] 所有页面引库顺序一致，无重复引入（如 ScrollTrigger 引两次）
- [ ] 每页都带：悬浮客服 + 返回顶部 + 所需弹窗 + 页尾 WOW/countUp 初始化
- [ ] 未完成栏目链接指向 `coming.html`，占位链接用 `href="javascript:;"`
- [ ] 5 断点逐档过一遍：1768 / 1400 / 1200 / 990 / 768，768 以下 vw 等比正常
- [ ] 移动端：汉堡菜单开合正常且锁滚动、header_top_box 隐藏、WOW 不触发
- [ ] 表单：必填/邮箱校验、tips 报错、2 秒防重复生效
- [ ] 视频灯箱关闭后停止播放（iframe 已重建）
