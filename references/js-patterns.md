# JS 交互配方

全部 jQuery（1.9.1 API），所有逻辑包在 `$(function(){ ... })` 内。无模块化、无 ES6+（保持 var / function，兼容老浏览器习惯）。

分工：**public.js** = 跨页公共（表单/弹窗/悬浮件/FAQ/scroll-spy）；**style.js** = 首页与头部（吸顶/汉堡/Tab/全部 Swiper 实例）。

## 目录

1. [吸顶导航](#吸顶导航)
2. [移动端汉堡菜单](#移动端汉堡菜单)
3. [Tab 切换（签名手法）](#tab-切换签名手法)
4. [手风琴 / FAQ](#手风琴--faq)
5. [弹窗（询盘/视频）](#弹窗询盘视频)
6. [表单校验 + 防重复提交](#表单校验--防重复提交)
7. [返回顶部 + 悬浮条显隐](#返回顶部--悬浮条显隐)
8. [Scroll-spy 锚点导航](#scroll-spy-锚点导航)
9. [Swiper 配方](#swiper-配方)
10. [WOW 入场动画](#wow-入场动画)
11. [GSAP 重型滚动（新项目增强）](#gsap-重型滚动新项目增强)

## 吸顶导航

```js
var headerTop = $('.header_bottom_box').offset().top;
$(window).scroll(function(){
	if($(this).scrollTop() > headerTop){
		$('.header_bottom_box').addClass("fixed");
	}else{
		$('.header_bottom_box').removeClass('fixed');
	}
});
```

进阶版（下滚隐藏、上滚显示）：用数组存历史 scrollTop 判断方向，向下滚给 `.header` 加 `.on` 隐藏：

```js
var scrolltop = [];
$(window).scroll(function(){
	scrolltop.push($(this).scrollTop());
	var len = scrolltop.length;
	if(len > 1 && scrolltop[len-1] > scrolltop[len-2] && scrolltop[len-1] > 300){
		$('.header').addClass('on');   // 下滚隐藏
	}else{
		$('.header').removeClass('on');
	}
});
```

## 移动端汉堡菜单

```js
$('.hd_btn').click(function(){
	$(this).toggleClass('on');
	if($(this).hasClass('on')){
		$('.nav_box').slideDown();
		$('html,body').css('overflow','hidden');   // 锁滚动
	}else{
		$('.nav_box').slideUp();
		$('html,body').css('overflow','auto');
	}
});
```

## header 搜索框开合（click）

搜索图标点击 toggle `.on`（配 CSS `.header_search_box{max-height:0;overflow:hidden;transition}` → `.on{max-height:...}` 下滑展开）；点框内 `stopPropagation`，点页面其它地方关闭。导航下拉、小语种下拉都是纯 CSS `:hover`，不用 JS。

```js
$('.header_search_btn').click(function(e){
	e.stopPropagation();
	$(this).toggleClass('on');
	$('.header_search_box').toggleClass('on');
});
$('.header_search_box').click(function(e){ e.stopPropagation(); });
$(document).click(function(){
	$('.header_search_btn,.header_search_box').removeClass('on');
});
```

## 状态切换：hover 还是 click？（先判断再动手）

- **有链接跳转性质的卡片/项**（本身是 `<a>`、会跳详情/列表/解决方案页，如首页解决方案钻石格、产品卡、案例卡）：激活态（换底色/图标染白/箭头变白等）一律用 **CSS `:hover`**，**不要**用 JS `.click()` 切换 `.on`。理由：这些是导航入口，鼠标经过预览、点击即跳走，用 click 切 `.on` 反而错位。
- **默认不给任何一项预设激活态**——包括第一项。设计稿常把第一项画成"经过态"示意效果，但那只是稿子在演示 hover，落地时第一项默认也是**未 hover 的常态**，鼠标移上去才变。别照着稿子给第一项写死 `class="... on"`。
- hover 态里子元素跟着变色：单色图标/箭头用 `filter:brightness(0) invert(1)` 染白，文字 `color:#fff`，都在 `:hover` 选择器下写，配 `transition:all .3s` 过渡。
- **click 只留给真正的"控件"**（就地改视图、不跳转的）：Tab 过滤、手风琴展开、弹窗开关、汉堡菜单、轮播分页——这些才用下面的 click 手法。

## Tab 切换（签名手法）

一句式：`addClass('on').siblings().removeClass('on')` + `.eq(index)` 显隐对应内容。所有 tab/分类切换/大菜单联动都用这套：

```js
$(".about_certified_btn li").click(function(){
	$(this).addClass("on").siblings().removeClass("on");
	$(".about_certified_content_list").eq($(this).index()).show().siblings().hide();
});

// mega-menu 左列 hover 联动右面板
$(".header_product_list_content_dl dd").hover(function(){
	$(this).addClass('on').siblings().removeClass('on');
	$(".header_product_list_content_item_right").eq($(this).index()).show().siblings().hide();
});
```

### Tab 里每个面板各有一个 Swiper（两个坑）

产品区那种「tab 切换 + 每个 tab 一个轮播」，两点务必注意：

1. **切换 list 绝不用 `display:none`**——Swiper 在 `display:none` 容器里初始化会测不到宽度，slide 变 0 宽、轮播失效。改用 **`height:0` + `overflow:hidden`（藏）/ `height:auto`（显）**，宽度始终可测。每个 tab 一个独立 Swiper 实例，切到某 tab 后对它 `.update()` 重算。

```js
var productSwipers = [
	new Swiper('.index_product_sw1', opt(1)),
	new Swiper('.index_product_sw2', opt(2)),
	new Swiper('.index_product_sw3', opt(3)),
];
$('.index_product_tab li').click(function(){
	var i = $(this).index();
	$(this).addClass('on').siblings().removeClass('on');
	$('.index_product_content_list').eq(i).addClass('on').css('height','auto')
		.siblings().removeClass('on').css('height',0);          // 不是 display:none
	$('.index_product_sw_nav').eq(i).addClass('on').siblings('.index_product_sw_nav').removeClass('on');
	if(productSwipers[i]) productSwipers[i].update();
});
```
CSS：`.index_product_content_list{height:0;overflow:hidden}` / `.on{height:auto}`。

2. **左右翻页按钮别放进这个带 `overflow:hidden` 的 list**——按钮常定位在容器外沿（`left:-24px`/`right:-24px`），会被 `overflow:hidden` 裁掉一半。两个解法（真实项目常用第 1 个）：**① 把翻页按钮写在 swiper 盒子外面**（移到 `.index_product_sw_box` 里，无 overflow 裁切；每个 tab 一组 `.index_product_sw_nav`，随 tab `.on` 显隐）；**② 用 `padding` 撑开**这个盒子把按钮容纳进来，但要算好宽度使加 padding 后内容仍与两侧对齐。

## 手风琴 / FAQ

固定逻辑：判断 `:visible`，先收起所有再展开当前，`+`/`-` 切换。产品分类树、导航下拉、ESG 折叠同套路：

```js
$('.faq_top').on('click', function() {
	var $faqItem = $(this).next('.faq_bottom');
	if ($faqItem.is(':visible')) {
		$(this).removeClass("on");
		$faqItem.slideUp();
		$(this).find('.faq_top_btn').html("+");
	} else {
		$('.faq_bottom:visible').slideUp();
		$('.faq_top').removeClass("on").find('.faq_top_btn').html("+");
		$(this).addClass("on");
		$faqItem.slideDown();
		$(this).find('.faq_top_btn').html("-");
	}
});
```

## 弹窗（询盘/视频）

统一动效：开 = `mask.slideDown(400, ()=> box.fadeIn(400))`，关 = 反向。email/product/download/video 四套同构：

```js
// 打开（product/download 弹窗先把 data-url 写入隐藏域）
$(".email_click").click(function(){
	$(".email_inquire_mask").slideDown(400, function(){
		$(".email_inquire_box").fadeIn(400);
	});
});
$(".inquire").click(function(){
	$(".product_inquire_box").find('.inquire_url').val($(this).attr('data-url'));
	$(".product_inquire_mask").slideDown(400, function(){
		$(".product_inquire_box").fadeIn(400);
	});
});
// 关闭
$(".email_inquire_close,.email_inquire_mask").click(function(){
	$(".email_inquire_box").fadeOut(400, function(){
		$(".email_inquire_mask").slideUp(400);
	});
});
// 视频灯箱：开时塞 src，关时销毁重建 iframe 停止播放
$(".video_click").click(function(){
	$(".video_lightbox iframe").attr('src', $(this).attr('data-url'));
	$(".video_lightbox_mask").slideDown(400, function(){
		$(".video_lightbox").fadeIn(400);
	});
});
$(".video_lightbox_close,.video_lightbox_mask").click(function(){
	$(".video_lightbox").fadeOut(400, function(){
		$(".video_lightbox_mask").slideUp(400);
		var content = $('.video_lightbox_content');
		content.find('iframe').remove();
		content.append('<iframe src="" frameborder="0" allowfullscreen></iframe>');
	});
});
```

## 表单校验 + 防重复提交

约定：`.required` 必填、`.email` 邮箱格式，错误提示写进紧邻 `<label class="tips">`；`window.canSend` + 按钮置灰 2 秒防重复：

```js
var emailReg = /^[-._A-Za-z0-9]+@([-._A-Za-z0-9]+\.)+[A-Za-z0-9]{2,3}$/;
window.canSend = true;

// 失焦即时校验
$(".required").blur(function(){
	if($(this).val() == ''){
		$(this).next("label").text("This field is required");
	}else if($(this).hasClass('email') && !emailReg.test($(this).val())){
		$(this).next("label").text("Please enter a valid email");
	}else{
		$(this).next("label").text("");
	}
});

// 聚焦浮动标签
$("input,textarea").focus(function(){ $(this).parent().addClass('inputon'); })
	.blur(function(){ if(!$(this).val()) $(this).parent().removeClass('inputon'); });

// 提交
$("form").submit(function(event){
	event.preventDefault();
	var pass = true;
	$(this).find(".required").each(function(){
		if($(this).val() == '' || ($(this).hasClass('email') && !emailReg.test($(this).val()))){
			$(this).trigger('blur');
			pass = false;
		}
	});
	if(pass && window.canSend){
		window.canSend = false;
		var btn = $(this).find('button[type=submit]');
		btn.css('filter','grayscale(1)');
		setTimeout(function(){
			window.canSend = true;
			btn.css('filter','none');
		}, 2000);
		// …实际提交交给 CMS，静态稿 action 留空…
	}
});
```

## 返回顶部 + 悬浮条显隐

```js
$(window).scroll(function(){
	if($(this).scrollTop() > 400){
		$('.right_online_box,.back_top').slideDown();
	}else{
		$('.right_online_box,.back_top').slideUp();
	}
});
$('.back_top').click(function(){
	$("html,body").animate({scrollTop: 0});
});
```

## Scroll-spy 锚点导航

产品详情页吸附导航（Overview/Video/Documents…）随滚动高亮 + 点击平滑滚动：

```js
var height = $('.public_product_detial_nav_box').height() + $('.header').height();
$(window).scroll(function(){
	$('.public_product_detial_content_list').each(function(key, item){
		if($(window).scrollTop() > $(item).offset().top - $(window).height() + height){
			$('.public_product_detial_nav_ul li').eq(key).addClass('on').siblings().removeClass('on');
		}
	});
});
$('.public_product_detial_nav_ul li').click(function(){
	var index = $(this).index();
	$('html,body').animate({
		scrollTop: $('.public_product_detial_content_list').eq(index).offset().top - height
	});
});
```

## Swiper 配方

Swiper 4.x API（`new Swiper('.swiper-container', …)`）。固定默认参数：`speed:900`、`autoplay:{delay:5000,disableOnInteraction:false}`。

**首页 banner**（`loop` + 切换时文字重新飞入 + 悬停停/启 autoplay）。文字容器类固定 `.index_banner_swiper_content`（与 [html-skeleton.md](html-skeleton.md) 首页 banner 骨架一致）：

```js
var indexBannerSwiper = new Swiper('.index_banner_swiper', {
	loop: true,
	speed: 900,
	autoplay: { delay: 5000, disableOnInteraction: false },
	pagination: { el: '.index_banner_swiper_page', clickable: true },
	on: {
		init: function(){
			$('.index_banner_swiper_content').eq(0).addClass('animated fadeInUp');
		},
		slideChangeTransitionStart: function(){
			$('.index_banner_swiper_content').removeClass('animated fadeInUp');
			$('.index_banner_swiper_content').eq(this.activeIndex).addClass('animated fadeInUp');
		}
	}
});
// 悬停暂停，离开续播
$('.index_banner_swiper').mouseenter(function(){ indexBannerSwiper.autoplay.stop(); });
$('.index_banner_swiper').mouseleave(function(){ indexBannerSwiper.autoplay.start(); });
```

> `loop:true` 时 `slideChangeTransitionStart` 里的 `activeIndex` 会因克隆 slide 偏移；文案重放动画通常仍可接受，若需精确对应真实索引改用 `realIndex`。自定义数字分页用 `renderBullet` 返回 `01/02…` 结构（见真实项目 `H-互旦官网`）。

**列表轮播**（breakpoints 降列，注意 4.x 的 breakpoints 是 max-width 语义）：

```js
var productSwiper = new Swiper('.index_product_sw', {
	slidesPerView: 4,
	spaceBetween: 20,
	speed: 900,
	autoplay: { delay: 5000, disableOnInteraction: false },
	navigation: { nextEl: '.index_product_next', prevEl: '.index_product_prev' },
	breakpoints: {
		1200: { slidesPerView: 3 },
		990: { slidesPerView: 2 },
		768: { slidesPerView: 1 }
	}
});
```

**中间大卡 + 两侧小预览卡**（案例区那种：居中大图，两侧露出更小的模糊预览，大卡压在小卡内侧）。这是模板 about 页「证书 swiper」的同一套逻辑——**核心：所有 slide 用同一个「最大卡」宽度（CSS 定，如 59%/953px），两侧的缩小 + 平移 + 层级全部在 JS 的 `on:progress` 回调里算**。**别用「负 spaceBetween」或「非激活 `scale()`」去凑**（那样定位飘、易出屏或只露细条）。

```css
.index_case_sw{max-width:1600px;margin:0 auto;overflow:hidden;}
.index_case_sw .swiper-slide{width:59%;}                       /* 所有 slide 同宽 = 最大卡（约 953） */
.index_case_card{width:100%;aspect-ratio:953/443;border-radius:8px;overflow:hidden;}
.index_case_sw .swiper-slide:not(.swiper-slide-active) .index_case_card{filter:blur(1px) brightness(.85);} /* 侧卡模糊 */
.index_case_sw .swiper-slide:not(.swiper-slide-active) .index_case_card_name,
.index_case_sw .swiper-slide:not(.swiper-slide-active) .index_case_card_more{display:none;}                /* 侧卡不显文字 */
```
```js
new Swiper('.index_case_sw', {
	watchSlidesProgress:true, slidesPerView:'auto', centeredSlides:true,
	loop:true, loopedSlides:5, speed:700,
	autoplay:{delay:5000,disableOnInteraction:false},
	pagination:{el:'.index_case_pagination',clickable:true},
	navigation:{nextEl:'.index_case_sw_next',prevEl:'.index_case_sw_prev'},
	on:{
		progress:function(){
			var sw=this.slides[0].offsetWidth;                 // ★ slide 实际宽（随视口变），位移按它比例算
			for(var i=0;i<this.slides.length;i++){
				var slide=this.slides.eq(i), sp=this.slides[i].progress, a=Math.abs(sp);
				slide.transform('translateX('+(sp*sw*0.21)+'px) scale('+(1-a*0.35)+')'); // 关键：按 slide 宽比例平移 + 缩小
				slide.css('zIndex',999-Math.round(a*10));                                 // 大卡在最上层
				slide.css('opacity',a>1.4?0:1);                                           // 只留中间 + 两侧各一
			}
		},
		setTransition:function(t){ for(var i=0;i<this.slides.length;i++) this.slides.eq(i).transition(t); }, // 拖动时同步过渡
	},
});
```
调参靠**实测 progress**：`console` 里看每个 slide 的 `.progress`（居中=0，两侧=±1，整数步长=slide宽）。位移量 **必须按 slide 实际宽度比例算**（`sp * slide.offsetWidth * 系数`），**别写死 px**——否则一到窄屏 slide 变小、固定 px 相对过大就整个错位（这是响应式必翻车点）。系数（≈0.21）与 `scale`（`1-a*0.35`，0.65≈设计 290/443）用实测微调。**平移符号别搞反**：相邻卡基础位置在大卡外侧、只有内缘露出，`sp` 为正的那侧要往**中心方向**拉才露得出来（写反/过大会把整卡推出屏）。`opacity` 藏掉更外层。≤768 单卡：CSS `width:86vw`、去 blur、显示文字（progress 变换在窄 slide 上会变成小 coverflow，可接受或按需清 transform）。

**主从缩略图联动**（产品详情主图 + 缩略图）：

```js
var detialBottomSw = new Swiper('.product_detial_sw_bottom', {
	slidesPerView: 4, spaceBetween: 10, watchSlidesProgress: true
});
var detialTopSw = new Swiper('.product_detial_sw_top', {
	thumbs: { swiper: detialBottomSw }
});
```

**合作伙伴无缝跑马灯**：

```js
new Swiper('.index_partner_sw', {
	slidesPerView: 5,
	spaceBetween: 30,
	loop: true,
	speed: 4000,
	autoplay: { delay: 0, disableOnInteraction: false },
	freeMode: true
});
```
配套 CSS：`.index_partner_sw .swiper-wrapper{transition-timing-function:linear;}`

**证书 3D coverflow**（`on.progress` 手写变换）：

```js
on: {
	progress: function(){
		for (var i = 0; i < this.slides.length; i++) {
			var slide = this.slides.eq(i);
			var progress = this.slides[i].progress;
			slide.transform('translateX(' + (progress * -100) + 'px) scale(' + (1 - Math.abs(progress) * 0.2) + ')');
			slide.css('zIndex', 10 - Math.abs(Math.round(progress)));
			slide.css('opacity', 1 - Math.abs(progress) * 0.33);
		}
	},
	setTransition: function(transition){
		for (var i = 0; i < this.slides.length; i++) {
			this.slides.eq(i).transition(transition);
		}
	}
}
```

## WOW 入场动画

- 只用三个类：`wow slideInUp`（主力）、`wow slideInLeft`、`wow slideInRight`；同级多元素用 `data-wow-delay="0.15s"` 递增错峰。
- 布局习惯：标题/左侧图文 `slideInLeft`，右侧 `slideInRight`，卡片列表 `slideInUp`。
- 初始化固定 `mobile:false`（见 html-skeleton.md 页尾脚本），移动端不跑入场动画。
- 数字计数：`<span class="counter">1993</span>` + `$('.counter').countUp({time:1000})`。

## GSAP 重型滚动（新项目增强）

默认动画走 WOW；只有设计要求滚动叙事/pin 钉住/序列帧/SVG 描边时才引 `gsap.min.js + ScrollTrigger.min.js`，并遵守：

- 只在大屏跑：`gsap.matchMedia().add("(min-width: 1367px)", () => { ... })`
- 尊重系统减弱动画：包 `prefers-reduced-motion` 判断降级。
- 数字滚动（GSAP 版，替代 countUp）：

```js
gsap.fromTo(el, { innerText: 0 }, {
	innerText: target, duration: 1.2, snap: { innerText: 1 },
	scrollTrigger: { trigger: el, start: "top 92%", once: true }
});
```

- 图片序列帧滚动（pin + scrub + snap 取整，onUpdate 切帧）：

```js
gsap.registerPlugin(ScrollTrigger);
var frameData = { currentFrame: 0 };
var tl = gsap.timeline({
	scrollTrigger: { trigger: '#box', start: 'top top', end: '+=4000', pin: true, scrub: 0.8 }
});
tl.to(frameData, {
	currentFrame: totalFrames - 1,
	snap: "currentFrame",
	ease: 'none',
	onUpdate: function(){
		images.css({opacity: 0, visibility: 'hidden'});
		images.eq(frameData.currentFrame).css({opacity: 1, visibility: 'visible'});
	}
});
```

要点：不 tween DOM 图片本身，只 tween 一个 `{currentFrame}` 数值；帧图片预加载（`padStart(4,'0')` 拼文件名循环 append）。

## GSAP 实战避坑（Z-中亿丰 项目验证过的教训）

1. **时间线里后段位置的 `.from()` 会捕获被污染的终点值**（终点被记录成起始值，动画空转、元素卡在隐藏态）。开场时间线一律用确定性写法：先 `gsap.set(el, 隐藏态)`，时间线里用 `.to(el, 自然态)` 回来，最后一个补 `clearProps`。带 ScrollTrigger 的 `.from()`（immediateRender 为 false）不受影响，可放心用于滚动入场。
2. **scrub 动画 + Chrome 滚动锚定会形成反馈回路**：transform 由滚动驱动的大元素（视差光斑/背景）会被浏览器选作滚动锚点，页面自己漂移乱滚。必须加 `html,body{overflow-anchor:none;}`。
3. **模板 reset.css 的公共组件样式会和深色/新版式冲突**，新项目必查并覆盖：`.back_top`（fixed 圆形白底）、`.email_inquire_close`（白圆）、`.email_inquire_box`（白底圆角）、banner 内 `h1{text-align:center}`。
4. **public.js 表单校验绑定的是 `$('input:submit')` 的 click**——提交按钮必须写 `<input type="submit"/>`，写 `<button>` 会跳过校验直接刷新页面。
5. **右侧悬浮条如果自定义容器类**（如 .index_online_box 常显设计），`.back_top` 仍会被 public.js slideDown 成 display:block——悬浮项样式用 block + `img{margin:auto}` 布局，别依赖 flex 居中。
6. **Figma 导出的 SVG 两个坑**：`preserveAspectRatio="none"` 会把非正方形图标拉伸成色块（改 `xMidYMid meet`）；镂空轮廓路径缺 `fill-rule="evenodd"` 时浏览器按 nonzero 填成实心（Figma 渲染器不同）。
7. **Figma 导出图片体积巨大**（单张可达 19MB），交付前用 PowerShell System.Drawing 批量压到 ≤1920 宽、质量 78 左右。
8. 区块自动轮播（tab 自动切换）要用 ScrollTrigger 的 onEnter/onLeave 门控，只在区块可见时运行，避免离屏空转导致用户到达时状态错乱。
