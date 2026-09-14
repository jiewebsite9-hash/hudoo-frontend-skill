# CSS 规范

## 目录

1. [文件分工](#文件分工)
2. [reset.css 结构](#resetcss-结构)
3. [CSS 变量](#css-变量)
4. [容器体系](#容器体系)
5. [类命名](#类命名)
6. [响应式断点与移动端 vw](#响应式断点与移动端-vw)
7. [字体](#字体)
8. [惯用片段](#惯用片段)
9. [布局套路](#布局套路)

## 文件分工

固定四件套，引入顺序：`animate.min.css → swiper.min.css → reset.css → style.css`。

- **reset.css**：reset + 原子工具类 + `@font-face` + `:root` 变量 + 容器类 + 全站公共组件样式（banner、crumbs、三弹窗、视频灯箱、right_online、back_top、news 列表/详情、faq、download、public_contact、product_detial 等 `public_` 区块）。这一层跨项目基本照搬。
- **style.css**：header + 本站所有页面业务样式，全塞一个文件（可到 100KB+），按 `/*header start*/ /*banner start*/ /*content start*/ /*footer start*/` 注释分段，样式写成单行（一条规则一行）。
- 不按页面拆分 CSS 文件；首页专属样式也在 style.css。

## reset.css 结构

开头第 1 行是一整段压缩的经典 reset + 原子工具类（跨项目不改），风格示意：

```css
body,h1,h2,h3,h4,h5,h6,p,ul,ol,li,dl,dt,dd{margin:0;padding:0;}ol,ul{list-style:none;}*{box-sizing:border-box;}
.fl{float:left;}.fr{float:right;}.tc{text-align:center;}.pr{position:relative;}.pa{position:absolute;}
.f12{font-size:12px;}.f14{font-size:14px;}.fc3{color:#333;}.mt10{margin-top:10px;}.ml10{margin-left:10px;}
.dn{display:none;}.dib{display:inline-block;}.ovh{overflow:hidden;}
a{text-decoration:none;transition:all 0.3s;}
```

之后依次：`@font-face` → `:root` 变量 → 容器类 → 公共组件样式 → 公共组件的媒体查询。

## CSS 变量

```css
:root{
	--mainColor: #038CFF;   /* 必有：品牌主色，换项目只改这一行 */
	--otherColor: #FF9600;  /* 可选：辅色 */
}
```

- 全站主色一律 `var(--mainColor)`，正文黑白灰直接写十六进制（`#222222`/`#333333`/`#666666`/`#FFFFFF`）。
- 部分项目会加 `--222/--333/--fff`（用色值命名）或 `--linear` 渐变，按设计稿需要增删。
- **新项目起步时清掉上一站残留的用不到的变量**。

## 容器体系

```css
.contentWH1600{width: 83.33%;height: auto;margin: 0px auto;}   /* 1600/1920 */
.contentWH1400{width: 73.33%;height: auto;margin: 0px auto;}   /* 1400/1920，最常用 */
.contentWH1200{width: 1200px;margin: 0px auto;}
```

区块结构固定三层：`区块.xxx_box（全宽，背景/padding）> 容器.contentWH1400 > 内容`。

## 类命名

- 全小写 + 下划线，按 DOM 层级从外到内逐级拼接语义长链：`index_product_content_list_sw_img`、`public_product_detial_info_content_left_bottom_sw`。不用 BEM、不用连字符、几乎不用 id。
- 作用域前缀：`index_` 仅首页；`public_` 跨页复用区块；`header_ / footer_ / nav_ / banner_ / crumbs_` 全局骨架；内页用页面名前缀（`about_ / contact_ / faq_`）。
- 状态类固定：`.on`（激活/展开/当前页码）、`.active`（当前导航）、`.fixed`（吸顶）、`.inputon`（输入聚焦）。
- 半宽表单项 `.WH48`（width:48%）。
- **既定拼写**（不许改成正确拼法）：`detial`、`priacy`、`frist`。
- **链接/标签选择器：会嵌套同名标签的结构，给标签加 class 按 class 选，别写 `.祖先 标签` 后代选择器**。典型是导航：一级 `<a>` 与下拉里的二级 `<a>` 在同一 `<li>` 下，若写 `.header_nav li a{…}` 或 `.header_nav li:hover a{color}`，字号/颜色/行高会**串到二级 `<a>`**（下拉子项被染色、行高错乱）。正确做法：一级用 `.nav_frist_link`、二级用 `.nav_two_link` 各自选，`hover` 变色也只写 `.header_nav li:hover .nav_frist_link`。**响应式断点里同样别偷偷写 `.header_nav li a{font-size:…}`**——这是最容易漏的地方。扁平、无嵌套同名标签的列表（footer 导航 `.footer_col ul li a`、语言 `.header_language_list a`、卡片单链接）用 `.xxx a` 后代选择器没问题，真实项目也这么用。

## 响应式断点与移动端 vw

固定 5 档 max-width，桌面优先，写在 style.css 尾部（公共组件的写在 reset.css 尾部）：

```css
@media (max-width: 1768px){ /* 大屏微调：容器放宽、字号微降 */ }
@media (max-width: 1400px){ /* 容器约 92%，导航字号降至 16px */ }
@media (max-width: 1200px){ /* 布局开始换行：flex-wrap、列数减少 */ }
@media (max-width: 990px){  /* 平板：导航切全屏抽屉，.hd_btn 显示 */
	.nav_box{position:absolute;top:80px;left:0;width:100%;height:calc(100vh - 80px);background:#fff;display:none;}
	.hd_btn{display:block;}
}
@media (max-width: 768px){  /* 手机：header_top_box 隐藏，单位全换 vw */
	.header_top_box{display:none;}
	.logo{width:14vw;}
	.nav_frist_link{font-size:3.8vw;line-height:10.8vw;}
	/* 字号 3.2~4vw，区块 padding:8vw 0，间距 2~6vw，标题 5.4~6vw */
}
```

**导航塞不下要实测定断点，别死守 990**：`nav_box` 默认应给 `white-space:nowrap` + 每项 `flex-shrink:0`（否则空间刚好卡死时双词项会竖直换行、溢出 header 上下——很难一眼看出）。导航项多 / 英文标签长（如本项目 8 项英文）时，990 根本放不下，990~1400 会重叠。做法：**量导航真实单行宽度**（`Σ 每个 li 的 offsetWidth`，别用 `nav.scrollWidth`——它把绝对定位的下拉也算进去了），算 `需要的 inner = 导航宽 + logo + 右侧 + 余量`，再按容器百分比反推能放下的最小视口（注意 `contentWH1600` 在 >1400 是 83.33%、≤1400 是 92%，所以 **1400~1500 区间容器反而更窄、最易挤爆**）。据此把**汉堡抽屉断点抬高**（本项目抬到 ≤1200，并把导航压到 font15/紧 padding、右侧 Get Quote/工具也缩一圈才在 1200~1536 塞下）。抽屉那套规则整体挪到新断点，布局类规则（about 堆叠、footer 换行）仍留在原 990/1200。

单位规则：
- PC：`px` 为主，宽度用百分比栅格；**不用 rem**。
- ≤768px：字号/间距/高度全部换 `vw`，实现等比缩放。经验换算：设计稿 375 宽时 `1px ≈ 0.2667vw`，常见值 `font-size:3.8vw ≈ 14px`、`padding:8vw 0 ≈ 30px`。
- 特例：404 等纯展示页整页用 vw 等比。

## 字体

本地 `.ttf` 放 `css/fonts/`，每个字重一个 `@font-face`，family 名 = 文件名：

```css
@font-face{font-family:"Inter-Regular";src:url("fonts/Inter-Regular.ttf");font-display:swap;}
@font-face{font-family:"Inter-Bold";src:url("fonts/Inter-Bold.ttf");font-display:swap;}
```

- CSS 里用 `font-family:"Inter-Bold"` 直接指定字重，不靠 `font-weight`。
- 全局兜底：`*{font-family:"Inter-Regular";}`（或站点主字体）。
- 选型习惯：英文外贸站 Inter / Plus Jakarta Sans / Roboto / Lato；繁体中文站 NotoSansTC。
- 行高用无单位小数：`line-height:1.6` / `1.8`。
- **文字换行少用 `<br>`，靠宽度/padding 约束折行**（真实项目首页普遍 0 个 `<br>`）。标题要折成设计稿那样的几行，给容器或标题本身设 `max-width`（如 banner 大标题 `max-width:760px`、钻石分类标题 `max-width:130px`），让它在词边界自然折；正文段落要提前折行用 `padding-right:X%`（如 `.xxx p{padding-right:18%}`、banner 大标题 `padding-right:10%`），造出设计稿那种右侧参差的收边。**极少写死实际固定宽度**。`<br>` 只在极个别硬要断行的大标题里偶尔用（真实项目也仅 banner 标题出现过几次）。

## 高度：默认 `auto`，不写死 px

**盒子/区块/内容容器默认不给固定高度，一律 `height:auto` 由内容或图片自然撑开。** 这是全站最高频的写法（真实项目里 `height:auto` 出现数百次）。想加固定 `height:XXXpx` 前先问：这是不是下面三类"必要情况"之一？不是就别加。

**只有这几类才用固定/比例高度：**
- **薄条/条带**：顶栏、主导航条（如 `.header_top_box{height:54px}`、`.header{height:88px}`）——外层 wrapper 仍 `height:auto`，只有条本身给高。
- **小型 UI 原子**：按钮/胶囊/徽标/分隔线/圆点/图标框（`height:48px`、`height:2px`…）；正方图标框用 `width:X;height:auto;aspect-ratio:1/1`。
- **需要固定裁切比例的图片容器与整幅大图区块**：用 `height:auto; aspect-ratio:W/H; overflow:hidden` + `img{object-fit:cover}`，**不要写死 px**。整幅背景区块照抄真实项目：`aspect-ratio:1920/860`、`1920/774`、`1920/640`；banner 用 `aspect-ratio:1920/800`（≤768 再 `aspect-ratio:auto` 让内容撑高）。

**常见错误改法：**
- `height:330px`（产品卡图）→ `height:auto;aspect-ratio:512/330`
- `height:800px`（banner）→ 见上，`aspect-ratio:1920/800`
- `height:227px`（CTA/文字条带区块）→ `height:auto;padding:72px 0`，内部 `align-items:center`，绝不写死高度
- 图片容器换比例后，覆盖其上的箭头/角标改用**百分比**定位（如 `top:27%`）跟随图片中心，别再用固定 px。

## 惯用片段

```css
/* 全局过渡（reset 已兜底 a 标签，其它元素单独加） */
transition: all 0.3s;

/* 卡片阴影（全站统一值） */
box-shadow: 0px 0px 21px 0px rgba(4,0,0,0.15);

/* 圆角 */
border-radius: 10px;

/* 多行截断 */
display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden;

/* 图片：容器控比例，img 填充 */
.xxx_img{aspect-ratio:16/9;overflow:hidden;}
.xxx_img img{width:100%;height:100%;object-fit:cover;display:block;}

/* PNG 图标染白（hover/深色背景） */
filter: brightness(0) invert(1);

/* 弹窗遮罩磨砂 */
background:rgba(0,0,0,0.35);backdrop-filter:blur(60px) saturate(1.2);

/* 块级元素常见起手 */
width:100%;height:auto;display:block;
```

Hover 三板斧：

```css
.card:hover img{transform:scale(1.1);}          /* 图片放大（容器 overflow:hidden） */
.ico:hover{transform:translateY(-10px);}         /* 图标上浮；或 rotate(360deg) */
.btn:hover{background:var(--mainColor);color:#fff;}  /* 按钮反色填充 */
```

## 布局套路

- 布局主用 flex（`display:flex;justify-content:space-between;align-items:center`），居中兜底 `position:absolute + transform:translate(-50%,-50%)`。
- 三栏卡片栅格：`li{width:32%;margin-left:2%;} li:nth-of-type(3n+1){margin-left:0;}`（两栏 48.5%、四栏 23.5% 同理）。
- 区块节奏：`.xxx_box{padding:80px 0;}`，相邻区块交替 `background:#F8F8F8` 与白色。
- 区块标题：`.xxx_title h2{font-size:48px;text-align:center;font-weight:600;}`，下方常配主色小横条 `span`（约 64×6px 圆角）或渐变下划线（120×3px）。
- 详情页小标题带竖条：`.xxx_title:before{content:"";width:5px;height:30px;background:var(--mainColor);}`
