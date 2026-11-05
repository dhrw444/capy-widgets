# CapyPlay 视频源组件

把爱奇艺 / 芒果TV / 优酷 / 腾讯视频的**官方片库**接进 [CapyPlayer](https://capyplayer.feifeiduck.com)。

全部为 TMDB 条目（`type: "tmdb"`），点击进入 App 内置 TMDB 详情页 —— 只做榜单发现，不提供片源直链。

## 组件列表

| 组件 | 文件 | 上游接口 | 频道 |
|---|---|---|---|
| 爱奇艺片库 | `widgets/iqiyi.js` | `pub.m.iqiyi.com/h5/main/recVideos/lib/` | 电视剧/电影/综艺/动漫/纪录片/少儿 |
| 芒果TV片库 | `widgets/mgtv.js` | `pianku.api.mgtv.com/rider/list/pcweb/v3` | 电视剧/电影/综艺/动漫/纪录片/少儿 |
| 优酷片库 | `widgets/youku.js` | `youku.518518.qzz.io`（第三方 Worker） | 电视剧/电影/综艺/动漫/纪录片/少儿 |
| 腾讯视频片库 | `widgets/tencent.js` | `pbaccess.video.qq.com/.../getMVLPage` | 电视剧/电影/综艺/动漫/纪录片/少儿 |

## 安装

App 内进入「组件」页 → 点 `+` → 粘贴下面的 URL → 预览 → 安装。

```
https://cdn.jsdelivr.net/gh/dhrw444/capy-widgets@main/widgets/iqiyi.js
https://cdn.jsdelivr.net/gh/dhrw444/capy-widgets@main/widgets/mgtv.js
https://cdn.jsdelivr.net/gh/dhrw444/capy-widgets@main/widgets/youku.js
https://cdn.jsdelivr.net/gh/dhrw444/capy-widgets@main/widgets/tencent.js
```

备用端点（jsDelivr 不通时换）：

```
https://fastly.jsdelivr.net/gh/dhrw444/capy-widgets@main/widgets/iqiyi.js
https://raw.githubusercontent.com/dhrw444/capy-widgets/main/widgets/iqiyi.js
```

## 相对上游的改动

源文件来自 `MakkaPakka518/Rex_Widgets`，是 **Rex 播放器**的模块。为 CapyPlay 契约做了两处改动：

| 改动 | 原因 | 影响 |
|---|---|---|
| `default:` → `defaultValue:` | CapyPlay 参数默认值只认 `defaultValue` / `value` | 修复前所有下拉框打开时不预选；改后正常 |
| 每个模块补 `type: "media_list"` | 文档建议显式声明模块类型 | 无行为变化 |

其余逻辑一字未动：上游接口、筛选参数、TMDB 匹配算法、缓存策略全部原样保留。

**未改动、已知无害**：顶层 `requiredVersion` 是 Rex 专有字段，CapyPlay 忽略未知字段。
**不需要 `loadDetail`**：所有条目都是 `type: "tmdb"`，点击走 App 内置 TMDB 详情页。

## 依赖 TMDB Key

这四个组件全部依赖 `Widget.tmdb.get()` 做中文标题匹配。**没有配 TMDB API Key 时，上游能抓到片名但匹配不到 TMDB，返回空数组。**

## 验证记录

### 官方预检（文档 §11.1 的 `validateCurrentWidget()`）

```
iqiyi.js    ok: true
mgtv.js     ok: true
youku.js    ok: true
tencent.js  ok: true
```

### 上游真抓取（TMDB 打桩成必命中，验证卡牌层）

| 文件 | 频道 | 抓到 | 条目字段 |
|---|---|---|---|
| iqiyi.js | 电视剧 | 小城良方 | `id,type,title,mediaType,posterPath,backdropPath,coverUrl,releaseDate,rating,genreTitle` |
| mgtv.js | 电视剧 | 玉簟秋 | 同上 |
| youku.js | 电视剧 | 嫁金钗 | 同上 |
| tencent.js | 电视剧 | 尚公主 | 同上 |

条目示例：

```json
{
  "id": 999001,
  "type": "tmdb",
  "mediaType": "tv",
  "title": "小城良方",
  "posterPath": "https://image.tmdb.org/t/p/w500/xxx.jpg",
  "backdropPath": "https://image.tmdb.org/t/p/w780/xxx.jpg",
  "releaseDate": "2025-01-01",
  "rating": 7.5
}
```

`posterPath` 已是完整 URL（上游自己拼了 `image.tmdb.org`），不是相对路径。

### 接口可达性

```
pub.m.iqiyi.com            200  0.44s
pianku.api.mgtv.com        200  0.34s
youku.518518.qzz.io        200  1.20s
pbaccess.video.qq.com      200  0.21s
```

## 不包含 wetv

`Rex_Widgets` 仓库里只有 `加密/wetv.js`，是 Rex 私有加密格式（`REXENC` 头 + AES-256-GCM），CapyPlay 无法解析。仓库内**没有 wetv 明文源码**。

如需腾讯系海外站，`widgets/tencent.js` 走的是腾讯视频主站接口。

## 上游致谢

组件逻辑来自 [MakkaPakka518/Rex_Widgets](https://github.com/MakkaPakka518/Rex_Widgets)（作者 𝙈𝙖𝙠𝙠𝙖𝙋𝙖𝙠𝙠𝙖）。本仓库仅做 CapyPlay 契约适配。
