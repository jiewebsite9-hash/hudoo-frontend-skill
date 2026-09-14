# HTML 骨架模板

所有片段直接可用，类名、结构、注释均为跨项目固定约定。公共组件靠**复制粘贴**进每个页面（无 include/无 JS 加载），保持逐字一致。

## 目录

1. [页面整体骨架](#页面整体骨架)
2. [head 模板](#head-模板)
3. [Header 两层结构](#header-两层结构)
4. [内页 Banner + 面包屑](#内页-banner--面包屑)
5. [Footer 四列](#footer-四列)
6. [右侧悬浮客服 + 返回顶部](#右侧悬浮客服--返回顶部)
7. [询盘弹窗（三种）+ 视频灯箱](#询盘弹窗--视频灯箱)
8. [分页组件](#分页组件)
9. [页尾固定初始化脚本](#页尾固定初始化脚本)

## 页面整体骨架

```html
<!DOCTYPE html>
<html>
<head> …见 head 模板… </head>
<body>
<!--header start-->  …  <!--header end-->
<!--banner start-->  …  <!--banner end-->
<!--content start-->
  <div class="content"> …各区块… </div>
<!--content end-->
<!--footer start-->  …  <!--footer end-->
<!--right online start-->  …  <!--right online end-->
<!--public inquire lightbox start-->  …三弹窗+视频灯箱…  <!--public inquire lightbox end-->
<script> …页尾初始化… </script>
</body>
</html>
```

## head 模板

普通页（title 留空，SEO 交给 CMS）：

```html
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
	<title></title>
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
</head>
```

广告落地页（`xxx_ads.html`，SEO 写满）在上面基础上追加：

```html
<title>页面标题 | 品牌名</title>
<meta name="description" content="…">
<meta name="keywords" content="…">
<link rel="canonical" href="https://域名/xxx_ads.html">
<meta property="og:title" content="…">
<meta property="og:description" content="…">
<meta property="og:type" content="website">
<meta property="og:url" content="…">
<script type="application/ld+json">{ "@context":"https://schema.org", "@type":"Service", … }</script>
```

## Header 两层结构

```html
<!--header start-->
<div class="header">
	<!-- 顶栏：社媒 + 联系方式 -->
	<div class="header_top_box">
		<div class="contentWH1400">
			<div class="header_top_content_left">
				<span>Follows us</span>
				<a href="javascript:;"><img src="images/header_top_ico1.png" alt=""/></a>
			</div>
			<div class="header_top_content_right">
				<a href="tel:+8613333333333"><img src="images/header_top_tel.png" alt=""/>+86 13333333333</a>
				<a href="mailto:info@123.com"><img src="images/header_top_email.png" alt=""/>info@123.com</a>
			</div>
		</div>
	</div>
	<!-- 主栏：logo + 导航 + 语言/搜索 + 汉堡 -->
	<div class="header_bottom_box">
		<div class="contentWH1400">
			<a href="index.html" class="logo"><img src="images/logo.png" alt=""/></a>
			<ul class="nav_box">
				<li>
					<a href="index.html" class="nav_frist_link active">Home</a>
				</li>
				<li>
					<a href="product_list.html" class="nav_frist_link">Products</a>
					<div class="nav_list_box">
						<div class="nav_list_item">
							<a href="javascript:;" class="nav_two_link">Category A</a>
							<div class="nav_list_content">
								<a href="product_detial.html">Item 1</a>
							</div>
						</div>
					</div>
				</li>
			</ul>
			<div class="header_content_right_language">
				<div class="header_language_content">
					<a href="index.html">English</a>
					<a href="index-ar.html">العربية</a>
				</div>
			</div>
			<div class="hd_btn"><span class="s1"></span><span class="s2"></span><span class="s3"></span></div>
		</div>
	</div>
</div>
<!--header end-->
```

要点：当前页一级链接加 `.active`；下拉靠 CSS hover + JS `.eq(index)` 联动；产品 mega-menu 用 `<dl><dd class="secondary">` 左列 + 右侧三级面板。

**header「三件套」是标配，设计稿静态图看不出交互也必须补齐**（Figma 只画顶级导航 + 搜索/地球图标，下拉/搜索框都得自己按套路建）：

1. **导航下拉菜单**（hover 展开）：有子页的一级项（如 Industry Solutions / Products / Support）挂 `.nav_list_box`（内 `.nav_list_item > .nav_two_link`，需三级再套 `.nav_list_content`）。CSS 里 `.nav_box li:hover .nav_list_box{display:block}`——**hover 展开，不是 click**。注意首级样式与"hover 变主色"只限定到 `.nav_frist_link`，别用 `.header_nav li a` 波及下拉里的 `.nav_two_link`（否则子项被一起染色/串行高）。
2. **小语种下拉**（hover 展开）：地球图标外面裹 `.header_language`（`position:relative;height:` 与 header 等高，让下拉 `top:100%` 落在 header 底部），内含 `.header_language_list`（English / Deutsch / Français / Español / العربية 硬链接）。`.header_language:hover .header_language_list{display:block}`。
3. **搜索框**（click 展开）：搜索图标 `.header_search_btn`，下方放 `.header_search_box`（`position:absolute;top:100%;max-height:0;overflow:hidden;transition`，含 `<form>` input + submit）。JS 点击 toggle `.on`（`max-height` 展开）、`$(document).click` 关闭、框内 `stopPropagation`。JS 见 js-patterns.md。

```html
<!-- 搜索框（放在 .header 内、header_bottom_box 之后，绝对定位下拉） -->
<div class="header_search_box">
	<div class="contentWH1600">
		<form action="" method="post">
			<input type="text" placeholder="Please enter a keyword" />
			<input type="submit" value="Search" />
		</form>
	</div>
</div>
```

## 首页 Banner（`index_banner_swiper` 轮播）

**首页 banner 一律用 Swiper 轮播骨架，不写死单张静态图**——哪怕设计稿只画了一屏 hero，也要按可切换多屏来搭（至少复制出 2 个 `swiper-slide` 占位，方便 CMS 后续填多张图文）。每屏结构固定：`index_banner_swiper_bg`（背景大图，绝对定位铺满）+ 可选 `index_banner_swiper_mask`（深色/渐变遮罩，设计稿深底 hero 才加）+ `index_banner_swiper_content_box`（绝对覆盖，flex 垂直居中）内套容器 + `index_banner_swiper_content`（h2 + p + 按钮）。分页放 `swiper-pagination index_banner_swiper_page`。

```html
<!--banner start-->
<div class="banner">
	<div class="swiper-container index_banner_swiper">
		<div class="swiper-wrapper">
			<div class="swiper-slide">
				<div class="index_banner_swiper_bg"><img src="images/index_banner.jpg" alt="" /></div>
				<div class="index_banner_swiper_mask"></div>
				<div class="index_banner_swiper_content_box">
					<div class="contentWH1600">
						<div class="index_banner_swiper_content">
							<h2>大标题<br />第二行</h2>
							<p>副文案……</p>
							<div class="index_banner_swiper_btns">
								<a href="javascript:;" class="index_banner_swiper_btn1">Explore Products</a>
								<a href="javascript:;" class="index_banner_swiper_btn2">View Solutions</a>
							</div>
						</div>
					</div>
				</div>
			</div>
			<!-- 复制若干 swiper-slide（CMS 多图占位） -->
		</div>
		<div class="swiper-pagination index_banner_swiper_page"></div>
	</div>
	<!-- 设计稿有底部装饰线时保留，如 index_banner_line -->
</div>
<!--banner end-->
```

要点：
- `index_banner_swiper_bg img` 用 `object-fit:cover` + Swiper 固定高度（如设计稿 800px；`height:auto` 只在图本身就是裁好的宽幅 hero 时用）；≤768 把 `content_box` 改 `position:static` 由内容撑高，避免绝对定位塌陷。
- JS 初始化见 [js-patterns.md](js-patterns.md) 的「首页 banner」配方（`loop + autoplay + 切换重放 fadeInUp + 悬停停/启`）。
- 内页 banner 才用下面的静态 `.banner > .banner_bg` 版本；两者不要混用。

## 内页 Banner + 面包屑

每个内页开头固定这两块：

```html
<!--banner start-->
<div class="banner">
	<div class="banner_bg"><img src="images/about_banner.jpg" alt="" /></div>
	<div class="banner_content">
		<div class="contentWH1400"><h1>About Us</h1></div>
	</div>
</div>
<!--banner end-->
<div class="crumbs_box">
	<div class="contentWH1400">
		<div class="crumbs_content">
			<a href="index.html"><img src="images/crumbs_home.png" alt=""/>Home</a>
			<i>></i>
			<a href="about.html">About Us</a>
		</div>
	</div>
</div>
```

带右侧 tab 的面包屑：左 `crumbs_content_left` + 右 `crumbs_content_right > li.on`。

## Footer 四列

```html
<!--footer start-->
<div class="footer">
	<div class="footer_top_box">
		<div class="contentWH1400">
			<div class="footer_logo">…logo + 简介 + 社媒图标…</div>
			<div class="footer_nav_box"><h3>Quick Links</h3><a href="about.html">About Us</a>…</div>
			<div class="footer_nav_box"><h3>Products</h3>…</div>
			<div class="footer_contact"><h3>Contact Us</h3>
				<li><img src="images/contact_info_ico1.png" alt=""/>+86 13333333333</li>…
			</div>
		</div>
	</div>
	<div class="footer_last_box">
		<div class="contentWH1400">
			<p>© 2026 公司名. All Rights Reserved.</p>
			<a href="priacy-policy.html">Privacy Policy</a>
		</div>
	</div>
</div>
<!--footer end-->
```

## 右侧悬浮客服 + 返回顶部

```html
<!--right online start-->
<div class="right_online_box">
	<a href="mailto:info@123.com" class="email_click_online"><img src="images/online_right_ico1.png" alt=""/></a>
	<a href="tel:+8613333333333"><img src="images/online_right_ico2.png" alt=""/></a>
	<a href="https://api.whatsapp.com/send?phone=8613333333333" target="_blank"><img src="images/online_right_ico3.png" alt=""/></a>
</div>
<div class="back_top"><img src="images/back_top.png" alt=""/></div>
<!--right online end-->
```

滚动超 400px 才显示（JS 控制）。占位数据固定用 `+86 13333333333` / `info@123.com`。

## 询盘弹窗 + 视频灯箱

三种询盘弹窗（email/product/download）同构，每个 = `_mask` 遮罩 + `_box` 表单 + `_close` 关闭。表单字段固定：Name / Email / Phone / Company / Message / Verify Code：

```html
<div class="email_inquire_mask"></div>
<div class="email_inquire_box">
	<div class="email_inquire_close"><img src="images/close.png" alt=""/></div>
	<h2>Get A Quote</h2>
	<form action="">
		<ul class="email_inquire_ul">
			<li class="WH48"><input type="text" placeholder="Name*" class="required" /><label class="tips"></label></li>
			<li class="WH48"><input type="text" placeholder="E-mail*" class="required email" /><label class="tips"></label></li>
			<li class="WH48"><input type="text" placeholder="Phone" /><label class="tips"></label></li>
			<li class="WH48"><input type="text" placeholder="Company" /><label class="tips"></label></li>
			<li><textarea placeholder="Massage*" class="required"></textarea><label class="tips"></label></li>
			<li class="code"><input type="text" placeholder="Verify Code*" class="required" /><label class="tips"></label><img src="images/code.png" alt=""/></li>
			<li><button type="submit">Submit</button></li>
		</ul>
		<input type="hidden" class="inquire_url" value=""/>
	</form>
</div>
```

要点：`.WH48` = 48% 半宽；`placeholder="Massage*"` 为既定拼写；产品/下载弹窗由触发元素 `data-url` 写入 `.inquire_url`/`.download_url` 隐藏域。

视频灯箱：

```html
<div class="video_lightbox_mask"></div>
<div class="video_lightbox">
	<div class="video_lightbox_close"><img src="images/close.png" alt=""/></div>
	<div class="video_lightbox_content"><iframe src="" frameborder="0" allowfullscreen></iframe></div>
</div>
```

触发元素带 `class="video_click" data-url="https://www.youtube.com/embed/XXXX"`。关闭时销毁并重建 iframe 停止播放（见 js-patterns.md）。

## 分页组件

```html
<ul class="news_page wow slideInUp">
	<li class="prev"><a href="javascript:;"><img src="images/news_page_prev.png" alt=""/></a></li>
	<li class="on"><a href="javascript:;">1</a></li>
	<li><a href="javascript:;">2</a></li>
	<li class="next"><a href="javascript:;"><img src="images/news_page_next.png" alt=""/></a></li>
</ul>
```

## 页尾固定初始化脚本

每页 `</body>` 前逐字复制：

```html
<script type="text/javascript">
	$(function(){
		$('.counter').countUp({ time: 1000 });
		var wow = new WOW({
			boxClass: 'wow',
			animateClass: 'animated',
			offset: 0,
			mobile: false,
			live: true
		});
		wow.init();
	})
</script>
```

`mobile:false` 是固定选择——移动端不跑入场动画。
