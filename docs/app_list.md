# MobileWorld 模拟器应用列表

本文档整理了 MobileWorld 容器内 Android 模拟器预装的所有应用程序，包括任务中使用的核心应用和 COMMON_APP_MAPPER 中映射的第三方应用。

---

## 一、核心任务应用 (APP_DICT)

这些是在 **201 个任务** 中直接引用的应用，是模拟器评估的核心组件。

| # | 应用名称 | 应用包名 | 说明 | 使用频率 |
|---|---------|---------|------|---------|
| 1 | **Calendar** | `org.fossify.calendar` | 日历 (Fossify 开源版) | 高 |
| 2 | **Camera** | `com.android.camera2` | 相机 | 低 |
| 3 | **Chrome** | `com.android.chrome` | Chrome 浏览器 | 高 |
| 4 | **Clock** | `com.google.android.deskclock` | 时钟/闹钟 | 中 |
| 5 | **Contacts** | `com.google.android.contacts` | 通讯录 | 中 |
| 6 | **Docreader** | *(未映射)* | 文档阅读器 | 中 |
| 7 | **Files** | `com.google.android.documentsui` | 文件管理器 | 中 |
| 8 | **Gallery** | `gallery.photomanager.picturegalleryapp.imagegallery` | 相册/图库 | 中 |
| 9 | **Mail** | `com.gmailclone` | 邮件 (自托管) | 高 |
| 10 | **Maps** | `com.google.android.apps.maps` | Google 地图 | 高 |
| 11 | **Mastodon** | `org.joinmastodon.android.mastodon` | Mastodon 社交 | 中 |
| 12 | **Mattermost** | `com.mattermost.rnbeta` | Mattermost 通信 | 高 |
| 13 | **Messages** | `com.google.android.apps.messaging` | 短信/消息 | 高 |
| 14 | **Settings** | `com.android.settings` | 系统设置 | 中 |
| 15 | **Taodian** (淘店) | `com.testmall.app` | 电商应用 (自研) | 高 |

### MCP 工具 (非独立应用)

这些是 Model Context Protocol 工具服务，通过 API 调用而非打开应用界面：

| MCP 工具 | 用途 | 调用方式 |
|---------|------|---------|
| **MCP-Amap** | 高德地图路线/距离查询 | API 调用 |
| **MCP-arXiv** | 学术论文搜索 | API 调用 |
| **MCP-Github** | GitHub 仓库/Issues 查询 | API 调用 |
| **MCP-jina** | 网页内容提取 | API 调用 |
| **MCP-stockstar** | 股票信息查询 | API 调用 |

---

## 二、系统基础应用

模拟器 (Android API 34, Pixel 8) 自带的系统应用：

| 应用名称 | 包名 | 说明 |
|---------|------|------|
| 桌面 (Launcher) | `com.google.android.apps.nexuslauncher` | Pixel 启动器 |
| 设置 | `com.android.settings` | Android 系统设置 |
| 相机 | `com.android.camera2` | Android 相机 |
| 信息 (SMS) | `com.android.mms` | 系统短信 |
| 邮件 | `com.android.email` | 系统邮件客户端 |
| 录音机 | `com.android.soundrecorder` | 音频录制 |
| 时钟 | `com.android.deskclock` | 系统时钟 |
| 通讯录 | `com.android.contacts` | 系统通讯录 |
| 文件 | `com.android.fileexplorer` | 文件管理器 |

---

## 三、COMMON_APP_MAPPER 完整映射

> **⚠ 重要说明**：此映射表中的 **150+ 个应用并非全部预装** 在模拟器中。
>
> `COMMON_APP_MAPPER` 的作用是：当 Agent 读取 UI Hierarchy XML 时，将 `<node package="com.xxx.xxx">` 解析为人类可读的应用名称。它是一个 **通用的解析映射表**，覆盖了 Agent 可能遇到的各种第三方应用。
>
> 模拟器中实际预装的应用由 **AVD 快照** (`Pixel_8_API_34_x86_64.avd`) 决定。只有 **第一节的 15 个核心应用** (加上 Android 系统自带应用) 是确定预装的。
>
> 如果需要安装额外的应用，可以通过以下步骤自定义 AVD：
> ```bash
> # 进入开发模式容器
> mw env run --dev
> mw env exec mobile_world_env_0_dev
>
> # 安装 APK (需要先在宿主机下载)
> adb install /path/to/app.apk
>
> # 保存快照
> adb emu avd snapshot save my_custom_snapshot
> ```

以下是 `mobile_world/runtime/utils/models.py` 中定义的完整映射。

### 3.1 国际应用

| 包名 | 显示名称 |
|------|---------|
| `com.android.chrome` | Chrome |
| `com.google.android.gm` | Gmail |
| `com.google.android.apps.maps` | 地图 |
| `com.google.android.apps.authenticator2` | Authenticator |
| `com.google.android.apps.bard` | Gemini |
| `com.google.android.googlequicksearchbox` | Google |
| `com.google.android.webview` | Android System WebView |
| `com.google.android.inputmethod.latin` | Gboard |
| `com.google.android.calendar` | Google Calendar |
| `com.google.android.apps.dynamite` | Google Chat |
| `com.google.android.deskclock` | Google Clock |
| `com.google.android.contacts` | Google Contacts |
| `com.google.android.apps.docs.editors.docs` | Google Docs |
| `com.google.android.apps.docs` | Google Drive |
| `com.google.android.apps.fitness` | Google Fit |
| `com.google.android.keep` | Google Keep |
| `com.google.android.apps.books` | Google Play Books |
| `com.google.android.apps.docs.editors.slides` | Google Slides |
| `com.google.android.apps.tasks` | Google Tasks |
| `com.google.android.apps.nbu.files` | Google Files |
| `com.Slack` | Slack |
| `com.spotify.music` | Spotify |
| `com.twitter.android` | X |
| `com.whatsapp` | WhatsApp |
| `com.ubercab` | Uber |
| `com.booking` | Booking.com缤客 |
| `com.airbnb.android` | 爱彼迎 |
| `com.linkedin.android` | LinkedIn |
| `com.reddit.frontpage` | Reddit |
| `com.quora.android` | Quora |
| `com.zhiliaoapp.musically` | TikTok |
| `com.einnovation.temu` | Temu |
| `com.expedia.bookings` | Expedia |
| `net.osmand` | OsmAnd |
| `org.videolan.vlc` | VLC |
| `com.duolingo` | Duolingo |
| `net.cozic.joplin` | Joplin |
| `com.mcdonalds.app` | McDonald's |
| `com.x8bit.bitwarden` | Bitwarden |
| `de.danoeh.antennapod` | AntennaPod |
| `com.rammigsoftware.bluecoins` | Bluecoins |
| `com.flauschcode.broccoli` | Broccoli |
| `code.name.monkey.retromusic` | Retro Music |
| `com.Project100Pi.themusicplayer` | Pi Music Player |
| `au.com.amaysim.android` | amaysim |
| `au.com.commsec.android.CommSecPocket` | Pocket |
| `com.transferwise.android` | Wise |
| `com.teslamotors.tesla` | Tesla |
| `dji.go.v5` | DJI Fly |
| `ai.x.grok` | Grok |
| `ai.perplexity.app.android` | Perplexity |
| `com.openai.chatgpt` | ChatGPT |
| `com.github.android` | GitHub |
| `com.substack.app` | Substack |
| `com.getsurfboard` | Surfboard |
| `com.tailscale.ipn` | Tailscale |
| `com.zerotier.one` | ZeroTier One |
| `com.reqable.android` | Reqable |
| `com.server.auditor.ssh.client` | Termius |

### 3.2 中国应用 — 社交/通信

| 包名 | 显示名称 |
|------|---------|
| `com.tencent.mm` | 微信 |
| `com.tencent.mobileqq` | QQ |
| `com.xingin.xhs` | 小红书 |
| `com.zhihu.android` | 知乎 |
| `com.taou.maimai` | 脉脉 |
| `org.telegram.messenger` | Telegram |
| `com.alibaba.android.rimet` | 钉钉 |
| `com.ss.android.lark` | 飞书 |
| `com.tencent.wemeet.app` | 腾讯会议 |
| `com.tencent.wetype` | 微信输入法 |

### 3.3 中国应用 — 购物/电商

| 包名 | 显示名称 |
|------|---------|
| `com.taobao.taobao` | 淘宝 |
| `com.jingdong.app.mall` | 京东 |
| `com.xunmeng.pinduoduo` | 拼多多 |
| `com.taobao.idlefish` | 闲鱼 |
| `com.alibaba.wireless` | 阿里巴巴 |
| `com.smzdm.client.android` | 什么值得买 |
| `com.dianping.v1` | 大众点评 |
| `com.meituan.retail.v.android` | 小象超市 |
| `com.sankuai.meituan` | 美团 |
| `me.ele` | 淘宝闪购 |
| `com.wudaokou.hippo` | 盒马 |
| `com.cainiao.wireless` | 菜鸟 |
| `cn.samsclub.app` | 山姆会员商店 |
| `com.ncarzone.tmyc` | 天猫养车 |
| `com.coolapk.market` | 酷安 |

### 3.4 中国应用 — 出行/旅行

| 包名 | 显示名称 |
|------|---------|
| `com.autonavi.minimap` | 高德地图 |
| `com.baidu.BaiduMap` | 百度地图 |
| `com.sdu.didi.psnger` | 滴滴出行 |
| `ctrip.android.view` | 携程旅行 |
| `ctrip.english` | Trip.com |
| `com.taobao.trip` | 飞猪旅行 |
| `com.umetrip.android.msky.app` | 航旅纵横 |
| `com.rytong.ceair` | 东方航空 |
| `com.booking` | Booking.com缤客 |
| `com.zuzuChe` | 租租车 |
| `com.airbnb.android` | 爱彼迎 |
| `com.jingyao.easybike` | 哈啰 |
| `com.redteamobile.roaming` | 逍遥游 |
| `com.tmri.app.main` | 交管12123 |
| `com.smk` | 杭州市民卡 |
| `com.hanweb.android.zhejiang.activity` | 浙里办 |

### 3.5 中国应用 — 金融/银行

| 包名 | 显示名称 |
|------|---------|
| `com.eg.android.AlipayGphone` | 支付宝 |
| `com.unionpay` | 云闪付 |
| `com.unionpay.tsmservice` | 银联可信服务安全组件 |
| `com.icbc` | 中国工商银行 |
| `com.chinamworld.main` | 中国建设银行 |
| `com.chinamworld.bocmbci` | 中国银行 |
| `cmb.pb` | 招商银行 |
| `cn.com.cmbc.newmbank` | 民生银行 |
| `com.citiccard.mobilebank` | 动卡空间 (中信) |
| `com.zhongan.ibank` | ZA Bank |
| `com.bochk.app.aos` | BOCHK 中银香港 |
| `com.mybank.android.phone` | 网商银行 |
| `com.webank.wemoney` | 微众银行 |
| `com.hexin.plat.android` | 同花顺 |
| `com.eastmoney.android.berlin` | 东方财富 |
| `com.thfund.client` | 天弘基金 |
| `com.finshell.wallet` | 钱包 |

### 3.6 中国应用 — 娱乐/媒体

| 包名 | 显示名称 |
|------|---------|
| `com.ss.android.ugc.aweme` | 抖音 |
| `tv.danmaku.bili` | 哔哩哔哩 |
| `com.tencent.qqlive` | 腾讯视频 |
| `com.youku.phone` | 优酷视频 |
| `com.tencent.qqmusic` | QQ音乐 |
| `com.netease.cloudmusic` | 网易云音乐 |
| `com.miHoYo.hkrpg` | 崩坏：星穹铁道 |
| `com.papegames.lysk.cn` | 恋与深空 |
| `app.podcast.cosmos` | 小宇宙 |
| `com.douban.frodo` | 豆瓣 |
| `cn.damai` | 大麦 |
| `com.sinyee.babybus.story` | 小布咕 |
| `com.baidu.netdisk` | 百度网盘 |
| `com.videogo` | 萤石云视频 |
| `com.oray.sunlogin` | 向日葵远程控制 |

### 3.7 中国应用 — AI/工具/生活

| 包名 | 显示名称 |
|------|---------|
| `com.aliyun.tongyi` | 千问 |
| `com.deepseek.chat` | DeepSeek |
| `com.larus.nova` | 豆包 |
| `com.google.android.apps.labs.language.tailwind` | NotebookLM |
| `com.baidu.BaiduMap` | 百度地图 |
| `com.autonavi.minimap` | 高德地图 |
| `com.eusoft.eudic` | 欧路词典 |
| `cn.wps.moffice_eng` | WPS Office |
| `org.zotero.android` | Zotero |
| `com.mi.health` | 小米运动健康 |
| `com.xiaomi.smarthome` | 米家 |
| `com.xiaomi.mico` | 小米音箱 |
| `com.xiaomi.shop` | 小米商城 |
| `com.haier.uhome.uplus` | 海尔智家 |
| `com.petkit.android` | 小佩宠物 |
| `com.sgcc.wsgw.cn` | 网上国网 |
| `com.sgcc.evs.echarge` | e充电 |
| `com.ct.client` | 中国电信 |
| `com.greenpoint.android.mc10086.activity` | 中国移动 |
| `com.alibaba.aliyun` | 阿里云 |
| `com.ft07.serverchan.app3.server_app3` | Server酱 |
| `com.MobileTicket` | 铁路12306 |
| `com.cmi.jegotrip` | 无忧行 |
| `com.mcdonalds.gma.cn` | 麦当劳 |
| `com.nearme.instant.platform` | 快应用服务框架 |
| `com.opos.ads` | 智能推荐服务 |
| `com.android.mms` | 信息 |
| `com.android.email` | 邮件 |
| `com.absinthe.libchecker` | LibChecker |

---

## 四、应用解析机制

当 Agent 执行 `open_app` 操作时，`AndroidController` 通过以下逻辑解析包名：

```python
# 来自 mobile_world/runtime/controller.py
APP_LOWER_DICT = {app_name.lower(): package_name 
                  for package_name, app_name in COMMON_APP_MAPPER.items()}
APP_LOWER_DICT.update({k.lower(): v for k, v in APP_DICT.items()})
```

**优先级**: `APP_DICT` 的映射会覆盖 `COMMON_APP_MAPPER` 中同名的条目。

**启动方式**: 通过 `adb shell monkey -p <package_name>` 启动应用。

---

## 五、自托管后端服务

除了客户端应用，MobileWorld 还运行了以下自托管后端服务 (在 Docker 容器内)：

| 服务 | 用途 | 对应客户端应用 |
|------|------|---------------|
| **Mattermost** | 企业通信 | Mattermost (`com.mattermost.rnbeta`) |
| **Mastodon** | 社交媒体 | Mastodon (`org.joinmastodon.android.mastodon`) |
| **Mall4Uni (淘店)** | 电商平台 | Taodian (`com.testmall.app`) |
| **Mail Server** | 邮件服务 | Mail (`com.gmailclone`) |

---

## 六、查看模拟器当前已安装应用

可以通过 ADB 直接查看模拟器内所有已安装的应用：

```bash
# 通过 ADB 查看所有安装包
adb -s localhost:5556 shell pm list packages

# 查看第三方应用 (排除系统内置)
adb -s localhost:5556 shell pm list packages -3

# 查看特定应用
adb -s localhost:5556 shell pm list packages | grep chrome

# 查看应用的启动 Activity
adb -s localhost:5556 shell cmd package resolve-activity --brief com.android.chrome
```
