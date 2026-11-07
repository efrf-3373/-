### efrf_eggynotice_board

 <img src="https://aka.doubaocdn.com/s/emmdfXUkxv" width = "100" height = "100" alt="Jsoft_logo" align=center />

###### ©2024-2026 efrf

> 原项目：Jsoft_eggynotice_board　原作者：kamcdev（Jsoft Studio）
> 原仓库：https://github.com/kamcdev/Jsoft_eggynotice_board
> 本仓库为二次修改版本 取消了静态部署 合并多文件

------

<img src="https://img.shields.io/badge/HTML-5-orange">

<img src="https://img.shields.io/badge/CSS-3-blue">

<img src="https://img.shields.io/badge/JavaScript-ES5-yellow">

------

目录
* [介绍](#介绍)
* [使用方法](#使用方法)
    * [直接打开](#直接打开)
    * [打开公告板](#打开公告板)
    * [集成到你的页面](#集成到你的页面)
* [文件结构](#文件结构)
* [数据格式](#数据格式)
* [API参考](#api参考)
* [结语](#结语)

<p id="介绍"></p>

------

# 介绍

这是还原《蛋仔派对》游戏公告板的组件

纯前端实现，无任何第三方依赖，还原游戏内公告板界面与交互

支持多分类页签、滚动联动、自定义滚动条、富文本颜色与链接、屏幕缩放适配

公告板由 JS 动态注入页面 DOM，页面无需预留任何 HTML 结构；关闭时自动从 DOM 移除公告板元素并释放事件监听，可反复打开关闭而不产生残留

本版本已将样式与脚本内联合并进 HTML，无需再引入独立的 css / js 文件

<p id="使用方法"></p>

------

# 使用方法

<p id="直接打开"></p>

1.直接打开

所有代码已合并进同一个 HTML 文件，**直接用浏览器打开即可**，不需要再起静态服务器

需要注意：公告板图片资源仍在 `css/egnotice/` 目录内，请保持该目录与 HTML 同级，不要移动或删除，否则公告内的图标、标题背景图会显示不出来

如果用 `file://` 协议打开时公告数据走 `fetch` 读取本地 JSON，部分浏览器会拦截本地请求，此时把数据直接写进 HTML 内传入 `EgNotice.render(data)`，或改用在线 JSON 地址即可

<p id="打开公告板"></p>

2.打开公告板

调用全局对象 `EgNotice` 打开公告板：

```js
// 方式一：直接传入数据数组渲染（合并版推荐）
EgNotice.render([
  {
    title: '维护公告',
    content: ['这是第一条公告内容']
  }
]);

// 方式二：从公告数据 JSON 文件加载并弹出（返回 Promise）
EgNotice.open('js/egnotice/sample-data.json');

// 方式三：初始化并配置 url，页面加载后自动弹出
EgNotice.init({ url: 'js/egnotice/sample-data.json' });

// 方式四：加载网易原版线上公告（转换后以 blob URL 显示）
EgNoticeEgg.load().then(function (blobUrl) {
  return EgNotice.open(blobUrl);
});
```

<p id="集成到你的页面"></p>

3.集成到你的页面

**无需在页面中书写任何公告板 HTML 结构**，公告板 DOM 会在调用打开方法时由 JS 动态创建并注入到 `document.body`

<p id="文件结构"></p>

------

# 文件结构

```
项目根目录
├── index.html          # 已合并 CSS 与 JS 的主文件
├── css/
│   └── egnotice/       # 公告板图片资源（图标、标题背景图等
├── appConfig.xlt
└── appIcon.png
```

`index.html` 已包含完整样式与脚本，无需额外引入其他文件；`css/egnotice/` 内图片不可删除或改名，否则公告板内的贴图会丢失

<p id="数据格式"></p>

------

# 数据格式

公告数据为一个 JSON 数组，每个元素是一个公告分类：

```json
[
  {
    "title": "维护公告",
    "title_bg_path": "img_notice_title1.png",
    "content": [
      "普通段落文字",
      "带颜色文字 #cff8914橙色",
      "查看详情 #f(c:00aaff)https://example.com#l",
      "发光文字 #f(c:ffffff|g:ff8914|G:5)发光效果#l",
      "描边文字 #f(o:ffffff|O:2)描边效果#l",
      "投影文字 #f(y:000000|Y:3)投影效果#l",
      "中划线文字 #f(h:ff0000)删除线#l",
      "下划线文字 #f(u:00aaff)下划线#l",
      "组合样式 #f(c:ff8914|s:18|o:ffffff|O:2|g:ff8800|G:4)组合效果#l",
      "内联图片 文字#p(f:img_egg.png|s:30)图片后文字",
      "子标题文字#image#img_play_pic.png"
    ]
  }
]
```

字段说明：

| 字段 | 说明 |
|------|------|
| `title` | 分类标题，同时作为左侧页签文字 |
| `title_bg_path` | 分类标题背景图片路径（可省略，省略时显示纯文字标题） |
| `content` | 内容块数组，每个元素为一个段落或子标题块 |

content 中的富文本标记：

| 标记 | 说明 |
|------|------|
| `#cRRGGBB` | 将其后的文字渲染为该颜色，如 `#cff8914橙色` |
| `#f(参数)文字#l` | 富文本样式包裹，`#l` 为结束标记；支持多参数以 `\|` 分隔，详见下表 |
| `#p(f:路径\|s:大小)` | 内联图片，`f` 为图片路径，`s` 为显示宽度（像素） |
| `文字#image#图片路径` | 带背景图片的子标题块，`#image#` 前为子标题文字，后为图片路径 |

`#f(...)` 支持的样式参数（以 `key:value` 格式，`\|` 分隔）：

| 参数 | 说明 | 示例 |
|------|------|------|
| `c:RRGGBB` | 文字颜色 | `c:ff8914` |
| `s:N` | 字号（像素） | `s:18` |
| `o:RRGGBB` | 描边颜色（与 `O` 配合） | `o:ffffff` |
| `O:N` | 描边大小（像素） | `O:2` |
| `g:RRGGBB` | 外发光颜色（与 `G` 配合，基于 text-shadow 叠加模糊层实现） | `g:ff8914` |
| `G:N` | 外发光大小（像素，控制模糊半径） | `G:5` |
| `y:RRGGBB` | 投影颜色（与 `Y` 配合，使用 drop-shadow 滤镜实现） | `y:000000` |
| `Y:N` | 投影偏移（像素） | `Y:3` |
| `h:RRGGBB` | 中划线颜色 | `h:ff0000` |
| `u:RRGGBB` | 下划线颜色 | `u:00aaff` |
| `e:url_id` | 超链接标识，存在时渲染为可点击链接，链接地址为 `#f(...)` 后的文字内容 | `e:url_0` |
| `i:link_id` | 链接显示文本标识（游戏内用于索引显示文本，Web 端直接显示链接地址） | `i:link_inner` |

当 `#f(...)` 参数中包含 `e:` 时，渲染为可点击链接，`#f(...)` 后的文字内容即为链接地址，例如 `#f(c:5A89EF|u:5A89EF|e:url_0|i:link_inner)https://party.163.com#l`

图片路径默认相对于 `css/egnotice/`（可通过配置项 `assetsBase` 修改），也支持绝对路径（以 `/` 开头、`http(s)://`、`blob:`、`data:` 开头）

若数据首条无 `title_bg_path`，组件会将其视为引导块，自动合并到第二条数据前并移除（用于适配网易原版公告格式）

<p id="api参考"></p>

------

# API参考

`EgNotice` 全局对象提供以下方法：

| 方法 | 说明 |
|------|------|
| `EgNotice.open(url, options?)` | 从 JSON 文件路径加载公告并弹出，返回 Promise |
| `EgNotice.render(data, options?)` | 直接传入公告数据数组渲染并弹出 |
| `EgNotice.init(options?)` | 初始化配置，设置 `url` 且 `autoOpen` 不为 false 时自动弹出 |
| `EgNotice.close()` | 关闭公告板，并从 DOM 中移除公告板元素、释放事件监听 |

配置项（options）：

| 配置 | 默认值 | 说明 |
|------|--------|------|
| `assetsBase` | `'css/egnotice/'` | 图片等资源的相对路径基准 |
| `firstTabTitle` | `'维护公告'` | 首个页签无标题时的兜底文字 |
| `mainTitle` | `''` | 顶部主标题，为空时使用首条数据标题 |
| `autoOpen` | `true` | init 配置了 url 时是否自动打开 |
| `onClose` | `null` | 关闭公告板时的回调函数 |

`EgNoticeEgg` 全局对象（网易数据转换器，可选引入）：

| 成员 | 说明 |
|------|------|
| `EgNoticeEgg.load(url?)` | 拉取网易原版公告并转换为 blob URL，返回 Promise；不传 url 时使用默认地址 |
| `EgNoticeEgg.DEFAULT_URL` | 默认数据地址（`https://u5.update.netease.com/game_notice/android.txt`） |

<p id="结语"></p>

------

# 结语

本readme基于原项目 Jsoft_eggynotice_board 修改，版权归原作者 kamcdev（Jsoft Studio）所有

使用与分发请遵守原项目相关版权约束
