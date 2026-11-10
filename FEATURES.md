<p align="center">
  <img src="assets/features-header.svg" width="100%" alt="Antra 功能一览"/>
</p>

<p align="center">
  <a href="README.md">← 返回 README</a> &nbsp;·&nbsp;
  <a href="https://github.com/anandprtp/Antra/releases">下载</a> &nbsp;·&nbsp;
  <a href="https://patreon.com/AntraVerse">赞助开发</a>
</p>

<br/>

---

## 多来源音频引擎

Antra 可以解析来自 Spotify、Apple Music、Amazon Music、Tidal、Qobuz 和 Deezer 的链接。在无损模式下，它会并行查询所有可用的无损来源，并挑选位深与采样率最高的结果——而不是「第一个找到的」。有损格式（AAC、MP3）会优先使用专门的有损来源，只有在万不得已时才回落到无损适配器。

```
每首歌的来源链：

  Antra 镜像服务器  →  Tidal · Qobuz · Amazon Music · Deezer · Apple Music
  内置回退          →  有损适配器与按来源划分的回退适配器
  Soulseek P2P      →  社区里有的任何东西，包括冷门与绝版发行
```

Antra 还会记住各个来源在多次重启之间的可靠程度（持久化的来源可靠性记录），并据此重排同优先级的来源——但绝不会让低质量来源越过更高质量来源。

免费版开箱即用，下载有频率限制。在 Patreon 上赞助即可获得 30 天赞助者密钥，解锁无限下载与 2-3 个并发下载。

---

## 下载来源选择器

可以把下载限制在你选定的服务上，而不是让解析器用尽全部来源。**自动**（默认）走完整解析链；也可以任意勾选 **Tidal**、**Qobuz**、**Apple Music**、**Amazon Music** 和 **Deezer**，把解析限制在这些服务内——未勾选的服务会被完全排除。只选一个服务即强制使用它；选多个（例如 Tidal + Qobuz）则只在你选中的来源之间回退。

在主界面的胶囊形选择器里就能切换，无需打开设置。

---

## 基于 ISRC 的精确匹配

大多数工具靠标题和艺人匹配，经常会抓到错的版本：重制版、电台剪辑版、地区限定版。Antra 使用 **ISRC 码**（每一段录音的唯一标识）来保证你拿到的是指定发行里的那一首。

有 ISRC 时，Antra 直接用它与来源 API 匹配；没有 ISRC 时，则回落到带标题-艺人权重的相似度评分搜索。

---

## 显式版本优先

如果你请求的是显式（未删改）版本，Antra 会优先选择它。电台剪辑版和删改版会在匹配评分中被扣分，当唯一可选项是净化版时会被跳过，让队列继续搜索直到找到显式来源。

可在设置中配置：**优先显式版本**（默认开启）。

---

## 严格匹配模式

面向小众音乐的可选安全模式。开启后，Antra 对非 ISRC 匹配要求更高的置信度，并在下载后执行更严格的时长校验，宁可干净地失败，也不冒险保存错的音频。默认关闭，因此不开启时的行为与以往一致。

---

## 高解析（Hi-Res）感知

Tidal 和 Qobuz 会在搜索结果里给出每首歌的位深与采样率。当某首歌存在高解析母带（例如 Tidal 的 24-bit/96kHz，Qobuz 最高 24-bit/192kHz）时，Antra 会继续搜索所有可用的无损来源，挑选分辨率最高的结果——先比位深，再比采样率。只有在找不到高解析来源时，才会使用 CD 音质（16-bit/44.1kHz）。

Qobuz 链接还支持**严格 24-bit 模式**：如果没有任何 Qobuz 账号能提供真正的 24-bit 流，请求会干净地失败，而不是在你要 24-bit 的时候悄悄保存 16-bit 音频。

---

## 自动打标签

每个下载的文件都会自动写入标签。无需手动编辑，不会缺封面，也不会剩下一堆「Track 01」。

| 标签 | 来源 |
|---|---|
| 标题、艺人、专辑、曲目号 | Spotify / Apple Music / Amazon 的元数据 |
| 专辑封面 | 流媒体曲库中的全分辨率封面 |
| 发行日期 | 有完整 ISO 日期时用完整日期，否则退化为年份 |
| 流派 | 通过 ISRC 取得 Deezer 专辑级流派，失败时回退 MusicBrainz |
| 作曲 | 来自 Qobuz、Tidal 或 Deezer 的元数据 |
| 碟号 | 多碟专辑也能正确标注碟号 |
| 歌词 | LRCLIB 同步歌词，回退 Genius / Musixmatch |
| ISRC | 写入文件，便于将来匹配 |

标签会按各容器要求的格式写入：MP3 用 ID3v2，FLAC 用 Vorbis comments，M4A 用 MP4 atoms——Windows Media Player、VLC、foobar2000 以及各大媒体服务器都能正常读取。

---

## 智能音乐库整理

输出结构正是各类媒体服务器期望的样子：

```
~/Music/
  Artist Name/
    Album Name (Year)/
      101 - Track Title.flac
      102 - Track Title.flac
      cover.jpg
```

下载内容直接落在音乐库根目录下——没有多余的 `Albums/` 或 `Playlists/` 中间层。所有曲目都使用带碟号前缀的编号（`101`、`102`……`201`、`202`……），因此 Plex、Navidrome 和 Jellyfin 永远知道一首歌属于哪张碟。

### 基于模板的文件名与文件夹

用占位符自定义命名规则。文件夹设置里有三个模板字段：

| 模板 | 默认值 | 输出示例 |
|---|---|---|
| 单曲文件名 | `{artist} - {title}` | `The Beatles - Come Together.flac` |
| 专辑内曲目文件名 | `{track} - {title}` | `07 - Come Together.flac` |
| 文件夹结构 | `{album_artist}/{year} - {album}` | `The Beatles/1969 - Abbey Road/` |

可用占位符：`{title}` `{artist}` `{album_artist}` `{album}` `{year}` `{track}` `{disc}` `{genre}` `{composer}` `{isrc}` `{codec}` `{bitrate}` `{quality}`

每个模板字段都提供实时预览。点击任意占位符标签，即可插入到光标处。

### 多碟处理

| 模式 | 示例 |
|---|---|
| **碟号前缀**（默认） | `2-05 - Track.flac` |
| **101/201 偏移** | `205 - Track.flac` |
| **仅曲目号** | `05 - Track.flac` |

---

## 智能去重

Antra 会用 ISRC、曲目 ID 以及规范化后的「标题 + 艺人」键，为你的音乐库建立身份索引。下载前它会先检查这首歌是否已存在，即使当初是存在了另一个艺人文件夹或另一张专辑版本里。

### 音乐库模式选项

| 模式 | 行为 |
|---|---|
| **智能去重**（默认） | 只要库里任意位置已存在相同 ISRC 就跳过。节省空间。 |
| **完整专辑** | 仅当目标文件夹里已有该文件时才跳过。允许同一首歌存在于多张专辑语境中。 |

---

## 主题

内置 11 套主题，可在全屏主题选择器（顶栏的 🎨 按钮）中切换。

**Antra 原创：** Antra（默认深青）、Ember（暖琥珀）、Ocean（深藏蓝）、Graphite（单色）、Sunset（洋红与金）、Linen（浅色模式）

**流媒体风格：** Spotify、TIDAL、Qobuz、Deezer、Apple Music、Amazon Music

每张卡片展示三个色块、名称和描述。选中即刻生效，并写入配置，下次启动自动恢复。

---

## 专辑地区可用性

粘贴一个 Spotify 或 Deezer 专辑链接，即可在实时世界地图上查看逐个国家/地区的可用情况。会显示完全开放的市场、部分开放、地区锁定，以及厂牌、UPC 和已确认的曲目数。下载前想确认某张发行在特定地区是否可听，用它最方便。

入口在顶栏的 🌍 按钮。

---

## 我的音乐库

连接你的 Spotify 和 Apple Music 账号，即可在 Antra 里直接浏览和下载自己的音乐库——不用再复制粘贴链接。

**一键连接。** 点击「连接 Spotify 账号」，系统浏览器会打开，你登录后 Antra 会自动捕获会话（无需手动提取 Cookie）。手动粘贴的方式仍然保留，作为备用。

**Spotify 音乐库**会显示你的**喜欢的歌曲**、算法 Mix（Discover Weekly、每日 Mix、Release Radar、On Repeat……）、全部个人**播放列表**、**收藏的专辑**以及**关注的艺人**。每个分区都可折叠。点击任意卡片即可下载；点击关注的艺人可打开其作品列表。

**Apple Music 音乐库**会显示收藏的歌曲和库内所有播放列表，用标签页顶部的 Spotify / Apple Music 切换器切换。

启动后默认停在「我的音乐库」。

---

## 自动同步 / 定时下载

让播放列表自动保持与音乐库同步。在任意播放列表卡片上开启自动同步（↺ 按钮），然后在设置里安排时间（时刻 + 星期）。Antra 会按计划检查被跟踪的播放列表，与上次下载的内容做差异比对，**只拉取新增的歌曲**。也可以点「立即同步」手动执行。

---

## YouTube Music 链接

粘贴一个 `music.youtube.com` 链接——单曲、播放列表或专辑都行——Antra 会像处理 Spotify 或 Apple Music 链接那样，用同一条无损来源链解析它。yt-dlp 负责提取元数据（有 ISRC 时也一并取回）；真正的音频来自 Tidal / Qobuz / Amazon / Deezer，因此各档音质（FLAC、ALAC、AAC、MP3）都支持。

---

## 艺人作品批量下载

用 Spotify 或 Apple Music 链接搜索任意艺人，Antra 会获取其完整作品列表，并按发行类型分组展示。

- 分别浏览**专辑**、**单曲**、**EP 与合辑**
- 一键全选或取消全选整组
- 把单张专辑或整个作品集一次性加入队列

---

## 并行下载引擎

Antra 默认同时下载 2 首歌。串行要花上几分钟的播放列表和专辑，如今只需一小部分时间。

```
串行：  第 1 首 → 第 2 首 → 第 3 首 → ...
并行：  第 1 首 ↘
        第 2 首 → 完成
        第 3 首 ↗
```

---

## 丰富的曲目列表界面

粘贴链接后，完整曲目列表会在下载开始前立即出现。对于 1000 首以上的播放列表，列表会随分页加载逐段出现——不必等整批抓取完成。

每一行显示曲目标题、艺人、时长，以及下载时的实时进度条。播放列表头部会展示封面、类型（专辑 / 播放列表 / 单曲）、艺人、曲目数、总时长和发行日期，布局与流媒体应用一致。

一次会话里排入多个链接时，每一批之间会用带专辑封面和标题的分隔条隔开，方便你分辨哪些曲目属于哪一批。

专用的日志面板（📋 按钮）会显示详细的下载输出，而不会打断曲目列表视图。

---

## 失败下载查看器

播放列表或专辑跑完后，失败的曲目会集中到曲目列表下方的**失败（N）**面板里——不用再滚动列表去找红行。可以单曲重试、**全部重试**（逐首执行并显示实时进度），或者忽略不在意的条目。当所有条目都被重试或忽略后，面板会自动收起并隐藏。

---

## 播放器内的同步歌词

内置的已下载曲目播放器会读取文件内嵌的 LRC 歌词，并显示一个滚动面板，随播放自动高亮当前行。用 ♪ 按钮可开合；当曲目带有同步歌词时它会自动出现。普通（非同步）歌词也会显示。

---

## 来源健康检查

URL 输入栏下方的状态胶囊会从公开状态接口拉取各来源的实时状态。绿色表示在线可用，深红表示当前不可用。Antra 在启动时检查一次；即使接口不可达，也照常工作。

---

## 音乐库历史

每次完成的下载会话都会连同封面缩略图、专辑/播放列表标题、链接、曲目数和时间戳一起存入历史，方便你快速确认下过什么，而不用打开文件夹翻找。

---

## 内置音频分析器

拿不准某个文件是真无损，还是 FLAC 外壳里包着 MP3？把音频文件拖进分析器即可。

**频谱图：** 完整的频率谱视图。由无损母带录制的真 FLAC 与转码来的有损文件，在频谱上一眼就能看出区别。

**统计数据面板：**

| 指标 | 说明 |
|---|---|
| 峰值 dBFS | 文件中最响的采样点 |
| RMS dBFS | 平均响度 |
| 真峰值 dBTP | 采样间峰值（对流媒体分发很重要） |
| 整体响度 | 全文件的 LUFS |
| 响度范围 | LRA——动态范围 |
| 截止频率 | 仍有实际内容的最高频率 |

**质量标记**会自动给出：FLAC/ALAC 文件若截止频率低于 14 kHz，标记为「假无损」；低于 17 kHz 标记为「疑似转码」；低于 19.5 kHz 标记为「音质可疑」。

支持批量分析、图库视图、单条移除以及「全部清除」。

---

## Soulseek / P2P 集成

对于任何流媒体侧来源都拿不到的曲目（冷门专辑、限量压制、绝版发行），Antra 集成了 Soulseek P2P 网络。

**零配置。** Antra 会自动下载、配置和管理后端，你只需在首次运行时填一次 Soulseek 账号信息。

> Soulseek 网络建立在分享之上。如果你用它，请尽量回传分享，并在方便时保持客户端在线。

---

## Spotify 播客下载

Antra 可以用你自己的 Spotify 账号 Cookie 直接下载任意 Spotify 播客单集或整档节目——不经过外部服务器，也没有第三方代理。

### 账号要求

**免费的 Spotify 账号就够了。** 播客音频并不在 Spotify Premium 之后。所有登录用户都能使用 320 kbps 的 OGG Vorbis 格式。

> 唯一的例外是**仅订阅者可听的单集**——由播客创作者设置的付费墙（与 Spotify Premium 无关）。这类单集会以「no audio files available」失败。

### 支持的链接

| 链接类型 | 示例 |
|---|---|
| 单集 | `https://open.spotify.com/episode/4rOoJ6Egrf8K2IrywzwOMk` |
| 整档节目（全部单集） | `https://open.spotify.com/show/0ofXAdFIQQRsCYj9754UFx` |

### 准备：获取 sp_dc Cookie

1. 在已登录的浏览器中打开 **[open.spotify.com](https://open.spotify.com)**
2. 打开**开发者工具**（Chrome/Edge 按 F12，macOS 按 Cmd+Option+I）
3. 进入 **Application** 标签 → **Cookies** → `https://open.spotify.com`
4. 找到名为 **`sp_dc`** 的 Cookie，复制它的值（以 `AQ...` 开头）
5. 回到 Antra，打开**设置 → Spotify 账号**，粘贴到 **sp_dc Cookie** 字段

> **最省事的方式：** 直接用设置里的**连接 Spotify 账号**按钮——它会打开浏览器，你登录后 Antra 自动捕获 Cookie。上面的手动步骤只在你想自己粘贴时才需要。

该 Cookie 有效期约一年。同一条连接也会驱动**我的音乐库**标签页。

### 输出与标签

单集保存在你设定的音乐文件夹下：

```
~/Music/
  Podcasts/
    Show Name/
      2024-03-15 - Episode Title.ogg
      2024-03-22 - Another Episode.ogg
```

### 频率限制

为了保护你的 Spotify 账号不被风控，Antra 会自动限速：每集之间随机延迟 3-7 秒，并且每小时最多 50 集。

---

## 音频格式选项

| 模式 | 输出 |
|---|---|
| **FLAC 24-bit** | 可用的最高质量无损来源，有高解析时优先使用。 |
| **FLAC 16-bit** | CD 音质无损，条件允许时优先 16-bit 而非 24-bit。 |
| **ALAC** | 兼容 Apple 生态的无损输出。 |
| **AAC** | 优先原生 AAC 来源，必要时才回落到无损。 |
| **MP3** | 优先原生 MP3/有损来源，必要时才回落到无损。 |
| **自动** | 最佳可用来源，优先无损。 |

---

## 平台支持

所有构建都是**单一自包含可执行文件**，不需要 Python、不需要运行时、无需安装任何依赖。

| 平台 | 最低版本 | 文件 |
|---|---|---|
| Windows | 10+ | `Antra.exe` |
| macOS | 12+（Apple Silicon） | `Antra-macOS.dmg` |
| macOS | 12+（Intel） | `Antra-macOS-Intel.dmg` |
| Linux | 任意 | `Antra-Linux.AppImage` |

---

## 技术栈

```
桌面外壳     →  Go 1.23 · Wails v2
前端界面     →  Svelte · TypeScript · Vite
下载引擎     →  Python 3.11
进程通信     →  基于 stdout 的换行分隔 JSON
打包         →  PyInstaller · wails build · AppImage · create-dmg
CI/CD        →  GitHub Actions，推 tag 时四平台矩阵构建
```

---

<p align="center">
  <br/>
  <strong>Antra 完全免费，由一个人利用业余时间维护。</strong><br/>
  <em>如果它帮你省下了订阅费，不妨帮它续命。</em>
  <br/><br/>
  <a href="https://patreon.com/AntraVerse">
    <img src="https://img.shields.io/badge/Support_on_Patreon-F96854?style=for-the-badge&logo=patreon&logoColor=white"/>
  </a>
  &nbsp;
  <a href="https://github.com/anandprtp/Antra">
    <img src="https://img.shields.io/badge/⭐_Star_on_GitHub-FFD700?style=for-the-badge&logo=github&logoColor=black"/>
  </a>
  &nbsp;
  <a href="https://t.me/antraaverse">
    <img src="https://img.shields.io/badge/Community-Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white"/>
  </a>
  <br/><br/>
  <sub><a href="README.md">← 返回 README</a></sub>
</p>
