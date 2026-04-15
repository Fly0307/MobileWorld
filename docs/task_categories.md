# MobileWorld 任务分类与测试内容

> **总计**: 201 个任务 · 10 个类别 · 15+ 个应用 · 5 个 MCP 工具
>
> **语言分布**: ~150 个英文任务 (lang-en) · ~50 个中文任务 (lang-cn)
>
> **交互模式**: ~60 个 User Interaction 任务 (agent-user-interaction) · ~30 个 MCP 任务 (agent-mcp)

---

## 一、任务分类总览

| # | 类别 | 目录 | 任务数 | 核心应用 | 测试能力 |
|---|------|------|-------|----------|----------|
| 1 | **日历管理** | `calendar/` | 12 | Calendar, Messages, Clock | 日程创建/删除/查询、闹钟设置 |
| 2 | **网页浏览** | `chrome/` | 5 | Chrome, Mail | 网页搜索、信息提取、邮件联动 |
| 3 | **电子邮件** | `gmail/` | 20 | Mail, Calendar, Maps, Chrome | 邮件撰写/回复/转发、会议管理 |
| 4 | **电商购物** | `mall/` | 14 | Taodian (自研购物应用) | 商品搜索、购物车管理、结算支付 |
| 5 | **地图导航** | `map/` | 11 | Maps, MCP-Amap, Chrome | 距离查询、POI 搜索、路线规划 |
| 6 | **社交媒体** | `mastodon/` | 28 | Mastodon, Chrome, Calendar | 发帖、关注、列表、投票、书签 |
| 7 | **短信通信** | `messages/` | 26 | Messages, Maps, MCP 工具 | 短信发送、信息 relay、MCP 工具调用 |
| 8 | **原生应用** | `native/` | 36 | Files, Mail, Clock, Camera | 文件管理、文档阅读、自拍、闹钟 |
| 9 | **系统设置** | `settings/` | 10 | Settings, Gallery | 亮度、字体、壁纸、飞行模式 |
| 10 | **办公协作** | `work/` | 37 | Mattermost, Mail, arXiv, GitHub | 频道管理、消息传递、论文搜索 |

---

## 二、各类别详细测试内容

### 1. 日历管理 (Calendar) — 12 个任务

测试 Agent 对 Android Calendar 应用的操作能力和时间管理逻辑。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| AddEventWithCafeTask | 添加日程 (咖啡馆聚会) | Calendar | lang-en |
| AddBusinessTripWithCafeTask | 添加出差日程 | Calendar | lang-cn |
| DeleteEventTask | 删除指定日程 | Calendar | lang-en |
| DeleteEventAskUserTask | 询问用户后删除日程 | Calendar | agent-user-interaction, lang-en |
| CheckEventTask | 查询日程信息 | Calendar | lang-en |
| CheckEventAskUserTask | 查询并告知用户日程 | Calendar | agent-user-interaction, lang-en |
| SetAlarmFromCalendarTask | 基于日历设置闹钟 | Calendar, Clock | lang-en |
| SetAlarmTask | 设置闹钟 | Clock | lang-en |
| SetAlarmTaskAskUser1/2/3 | 询问用户时间后设置闹钟 | Clock | agent-user-interaction, lang-en |
| PlanTaxiRouteSmsTask | 规划打车路线并短信通知 | Messages, MCP-Amap | agent-mcp, lang-en |
| PlanCyclingRouteSmsTask | 规划骑行路线并短信通知 | Messages, MCP-Amap | agent-mcp, lang-en |
| PlanDrivingRouteSmsTask | 规划驾车路线并短信通知 | Messages, MCP-Amap | agent-mcp |

**测试能力**:
- ✅ 日程 CRUD 操作 (创建/读取/更新/删除)
- ✅ 时间解析与转换
- ✅ 多应用联动 (Calendar → Clock)
- ✅ 用户意图理解与消歧

---

### 2. 网页浏览 (Chrome) — 5 个任务

测试 Agent 使用 Chrome 浏览器进行信息检索的能力。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| SearchWeatherTask | 搜索天气信息 | Chrome | lang-en |
| SearchWeatherAndSendEmailTask | 搜索天气并发送邮件 | Chrome, Mail | lang-en |
| SearchGithubStatsTask | 搜索 GitHub 项目信息 | Chrome | lang-en |
| SearchNewsTask | 搜索新闻 | Chrome | lang-en |
| SearchNewsAndSendEmailTask | 搜索新闻并邮件分享 | Chrome, Mail | lang-en |

**测试能力**:
- ✅ 浏览器导航与搜索
- ✅ 网页信息提取
- ✅ 跨应用工作流 (搜索 → 邮件)

---

### 3. 电子邮件 (Gmail) — 20 个任务

测试 Agent 处理电子邮件的综合能力，包括撰写、回复、转发、会议管理等。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| ReplyEmailTask | 回复邮件 | Mail | lang-en |
| ReplyEmailWithCalendarTask | 回复邮件并附加日历信息 | Mail, Calendar | lang-en |
| SendEmailTask | 发送邮件 | Mail | lang-en |
| SendEmailWithAttachmentTask | 发送带附件的邮件 | Mail, Files | lang-en |
| ForwardEmailTask | 转发邮件 | Mail | lang-en |
| CancelMeetingTask | 取消会议并通知 | Mail, Calendar | lang-en |
| CancelMeetingWithLocationTask | 取消会议并发送位置 | Mail, Calendar, Maps | lang-en |
| CheckConferenceAndSendSmsTask1/2/3/4 | 查询会议信息并短信通知 | Mail, Messages | lang-en |
| CheckRegistrationTask | 检查注册状态 | Mail, Chrome | lang-en |
| CheckDepartTimeTask | 检查出发时间 | Mail, Maps | lang-en |
| CheckConferenceLocationTask | 查询会议地点 | Mail, Maps | lang-en |
| FormReplyTask | 表单式邮件回复 | Mail | lang-en |
| SendFormTask | 发送表单邮件 | Mail | lang-en |
| PlanRouteWithMapTask | 规划路线并邮件发送 | Mail, Maps, MCP-Amap | agent-mcp, lang-en |

**测试能力**:
- ✅ 邮件 CRUD 操作
- ✅ 联系人解析与填写
- ✅ 附件处理
- ✅ 会议管理与日程联动
- ✅ 多步骤工作流

---

### 4. 电商购物 (Mall) — 14 个任务

测试 Agent 在自研购物应用 (Taodian) 中的电商操作能力。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| SearchItemAndCheckoutTask | 搜索商品并加入购物车 | Taodian | lang-cn |
| ItemCheckoutTask | 购物车结算 | Taodian | lang-cn |
| CartManagementTask | 购物车管理 (增删改) | Taodian | lang-cn |
| CartManagementAskUserTask | 询问用户后管理购物车 | Taodian | agent-user-interaction, lang-en |
| CalculateCartPricesByOwnerAskUserTask | 按归属计算购物车价格 | Taodian | agent-user-interaction, lang-en |
| CheckCartPriceTask | 检查购物车总价 | Taodian | lang-en |
| CheckPuchasedItem | 查看已购商品 | Taodian | lang-cn |
| DeleteItemsAskUserTask | 询问后删除购物车物品 | Taodian | agent-user-interaction, lang-en |
| CartInfoNotificationTask | 购物车信息短信通知 | Taodian, Messages | lang-en |
| RecentTotalExpenseTask | 查询近期总支出 | Taodian | lang-cn |
| BuyBlackTennisShoesBrotherAddressAskUserTask | 为哥哥购买黑色网球鞋并寄到指定地址 | Taodian | agent-user-interaction, lang-en |
| BuySugarFreeColaWorkAddressAskUserTask | 购买无糖可乐寄到工作地址 | Taodian | agent-user-interaction, lang-cn |
| SearchWhiteSneakersCheckoutAskUserTask | 搜索白色运动鞋并结算 (询问用户) | Taodian | agent-user-interaction, lang-cn |

**测试能力**:
- ✅ 商品搜索与筛选
- ✅ 购物车操作 (添加/删除/修改)
- ✅ 价格计算与比较
- ✅ 结算流程
- ✅ 地址管理与用户交互
- ✅ 消费记录查询

---

### 5. 地图导航 (Map) — 11 个任务

测试 Agent 使用地图和位置服务的能力。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| CheckDistanceMcpTask | 查询两地距离 (MCP) | Chrome, MCP-Amap | agent-mcp, lang-cn |
| CheckDistanceMcpTask2 | 查询距离并比较 (MCP) | Taodian, MCP-Amap | agent-mcp, lang-cn |
| CheckDistanceAskUserTask1/2 | 询问用户后查询距离 | Maps | agent-user-interaction, lang-en |
| GoogleMapsAlibabaPhoneContactTask | 查找阿里巴巴南园电话联系信息 | Maps, Contacts | lang-en |
| GoogleMapsAlibabaPhoneContactAskUserTask | 查找电话联系信息 (询问用户) | Maps, Contacts | agent-user-interaction, lang-en |
| GoogleMapsAlibabaSouthNeighborTask | 查找阿里巴巴南园邻居信息 | Maps | lang-en |
| GoogleMapsAlibabaSouthNeighborAskUserTask | 查找邻居信息 (询问用户) | Maps | agent-user-interaction, lang-en |
| DownloadPoiImagesMcpTask | 下载 POI 图片 (MCP) | Chrome, MCP-Amap | agent-mcp, lang-cn |
| TextArrivalTimeTask | 查询到达时间并短信通知 | Messages, Maps | lang-en |
| CompareApartmentsDistanceSmsTask | 比较公寓距离并短信通知 | Messages, MCP-Amap | agent-mcp, lang-cn |

**测试能力**:
- ✅ 地图搜索与导航
- ✅ 距离/时间计算
- ✅ POI (兴趣点) 信息查询
- ✅ MCP 工具调用 (Amap)
- ✅ 联系人信息联动

---

### 6. 社交媒体 (Mastodon) — 28 个任务

测试 Agent 在 Mastodon 社交平台上的操作能力。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| MastodonCreateTootTask | 发布新帖 (Toot) | Mastodon | lang-en |
| MastodonCreateTootWithImageTask | 发布带图片的 Toot | Mastodon, Chrome | lang-en |
| MastodonReplyTootTask | 回复 Toot | Mastodon | lang-en |
| MastodonRetootTask | 转发 Toot (Reblog) | Mastodon | lang-en |
| MastodonFollowUserTask | 关注用户 | Mastodon | lang-en |
| MastodonUnfollowUserTask | 取消关注 | Mastodon | lang-en |
| MastodonCreateListTask | 创建用户列表 | Mastodon | lang-en |
| MastodonAddUserToListTask | 添加用户到列表 | Mastodon | lang-en |
| MastodonRemoveUserFromListTask | 从列表移除用户 | Mastodon | lang-en |
| MastodonCreatePollTask | 创建投票 | Mastodon | lang-en |
| MastodonVotePollTask | 参与投票 | Mastodon | lang-en |
| MastodonBookmarkTootTask | 收藏 Toot | Mastodon | lang-en |
| MastodonRemoveBookmarkTask | 取消收藏 | Mastodon | lang-en |
| MastodonAddHashtagTask | 添加话题标签 | Mastodon | lang-en |
| MastodonRemoveHashtagTask | 移除话题标签 | Mastodon | lang-en |
| MastodonFilterTask | 使用过滤器 | Mastodon | lang-en |
| MastodonSearchTootTask | 搜索 Toot | Mastodon | lang-en |
| MastodonEditTootTask | 编辑 Toot | Mastodon | lang-en |
| MastodonDeleteTootTask | 删除 Toot | Mastodon | lang-en |
| MastodonPinTootTask | 置顶 Toot | Mastodon | lang-en |
| MastodonUnpinTootTask | 取消置顶 | Mastodon | lang-en |
| MastodonMuteUserTask | 静音用户 | Mastodon | lang-en |
| MastodonBlockUserTask | 屏蔽用户 | Mastodon | lang-en |
| MastodonReportUserTask | 举报用户 | Mastodon | lang-en |
| MastodonCheckNotificationsTask | 查看通知 | Mastodon | lang-en |
| MastodonCheckProfileTask | 查看个人资料 | Mastodon | lang-en |
| MastodonCheckTimelineTask | 查看时间线 | Mastodon | lang-en |

**测试能力**:
- ✅ 社交 CRUD (发帖/回复/转发)
- ✅ 用户关系管理 (关注/取关/静音/屏蔽)
- ✅ 列表管理
- ✅ 投票功能
- ✅ 收藏与书签
- ✅ 话题标签操作
- ✅ 内容搜索与过滤

---

### 7. 短信通信 (Messages) — 26 个任务

测试 Agent 通过短信进行通信的能力，常结合 MCP 工具使用。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| SendWeatherSmsTask | 查询天气并短信发送 | Messages, MCP-Amap | agent-mcp, lang-cn |
| SendStockEsgRatingSmsTask | 查询股票 ESG 评级并短信 | Messages, MCP-stockstar | agent-mcp, lang-cn |
| SendHighDividendStocksSmsTask | 查询高股息股票并短信 | Messages, MCP-stockstar | agent-mcp, lang-cn |
| SearchBenchmarkTasksSmsTask | 搜索基准任务并短信 | Messages, MCP-Github | agent-mcp, lang-cn |
| SearchGithubIssuesSmsTask | 搜索 GitHub Issues 并短信 | Messages, MCP-Github | agent-mcp, lang-cn |
| SearchPharmacyTask | 搜索药店位置并短信 | Messages, MCP-Amap | agent-mcp, lang-cn |
| SendLocationAddressSmsTask | 发送位置地址短信 | Messages, MCP-Amap | agent-mcp, lang-cn |
| SendWebpageAudioCountSmsTask | 统计网页音频数并短信 | Messages, MCP-jina | agent-mcp, lang-en |
| FactCheckLemonWaterSmsTask | 查证柠檬水事实并短信 | Messages, MCP-jina | agent-mcp, lang-cn |
| SayHelloRoommatesAskUserTask1 | 向室友问好 (询问信息) | Messages, Contacts | agent-user-interaction, lang-en |
| SendInterviewInvitationAskUserTask | 发送面试邀请 (询问用户) | Messages | agent-user-interaction, lang-en |
| DeleteMessagesAskUserTask | 询问后删除短信 | Messages | agent-user-interaction, lang-en |
| DeleteAllMessagesAskUserTask | 询问后删除所有短信 | Messages | agent-user-interaction, lang-en |
| CheckCandidateAskUserTask2 | 查询候选人信息 | Messages, Files | agent-user-interaction, lang-en |
| SMSManagement | 短信综合管理 | Messages, Mail | lang-en |
| PlanRouteAndSmsTask | 规划路线并短信通知 | Messages, MCP-Amap | agent-mcp |

**测试能力**:
- ✅ 短信发送与接收
- ✅ 联系人查找与使用
- ✅ MCP 工具调用 (Amap/GitHub/jina/stockstar)
- ✅ 信息提取与转发
- ✅ 短信删除与管理
- ✅ 多步骤工作流

---

### 8. 原生应用 (Native) — 36 个任务

测试 Agent 操作 Android 原生应用的能力，覆盖文件管理、文档处理、相机、闹钟等。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| CountFileLinesTask | 统计文件行数 | Files | lang-en |
| SumFileLinesTask | 汇总多文件行数 | Files | lang-en |
| DeleteUselessFilesAskUserTask | 询问后删除无用文件 | Files | agent-user-interaction, lang-en |
| BidFileRenameAskUserTask | 询问后重命名投标文件 | Files | agent-user-interaction, lang-cn |
| BidFileRenameTask | 重命名投标文件 | Files | lang-cn |
| SendZipFilesAskUserTask1/2 | 压缩并发送文件 (询问用户) | Files, Mail | agent-user-interaction, lang-en |
| ReadQwen3PaperTask1-5 | 阅读 Qwen3 论文并回答 | Docreader, Files | lang-en |
| CheckInvoiceTask1-4 | 查看发票信息 | Docreader, Files | lang-en |
| InvoiceReceiptCopyTask | 复制发票收据 | Files | lang-cn |
| InvoiceReceiptCopyAskUserTask | 复制发票 (询问用户) | Files | lang-cn |
| SendInvoiceWithInfoTask | 发送含信息的发票 | Docreader, Files, Chrome, Mail | agent-user-interaction, lang-en |
| CVEmailTask | 发送简历邮件 | Files, Mail | lang-cn |
| CVEmailAskUserTask | 发送简历 (询问用户) | Files, Mail | agent-user-interaction, lang-cn |
| ReviewPaperEmailTask | 发送论文审阅邮件 | Files, Mail | lang-cn |
| ProjectPaperEmailAskUserTask | 发送项目论文 (询问用户) | Files, Mail | agent-user-interaction, lang-cn |
| TakeSelfieTask | 自拍 | Camera | lang-en |
| TakeSelfieAskUserTask1/2 | 自拍 (询问用户/场景) | Gallery, Camera, Mail | agent-user-interaction, lang-en |
| SharePhotosTask | 分享照片 | Gallery, Mail | lang-en |
| SharePhotosAskUserTask | 分享照片 (询问用户) | Gallery, Mail | agent-user-interaction, lang-en |
| SetAlarmTask | 设置闹钟 | Clock | lang-en |
| SetAlarmTaskAskUser1/2/3 | 设置闹钟 (询问用户) | Clock | agent-user-interaction, lang-en |

**测试能力**:
- ✅ 文件浏览与管理
- ✅ 文档阅读与信息提取
- ✅ 文件压缩与发送
- ✅ 相机操作
- ✅ 照片分享
- ✅ 闹钟设置
- ✅ 用户交互 (询问偏好/信息)

---

### 9. 系统设置 (Settings) — 10 个任务

测试 Agent 修改 Android 系统设置的能力。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| ChangeBrightnessTask | 修改屏幕亮度 | Settings | lang-en |
| ChangeFontSizeTask | 修改字体大小 | Settings | lang-en |
| ChangeIconSizeTask | 修改图标大小 | Settings | lang-en |
| ChangeWallpaperTask | 更换壁纸 | Settings, Gallery | lang-en |
| EnableAirplaneModeTask | 开启飞行模式 | Settings | lang-en |
| DisableAirplaneModeTask | 关闭飞行模式 | Settings | lang-en |
| EnableWifiTask | 开启 WiFi | Settings | lang-en |
| DisableWifiTask | 关闭 WiFi | Settings | lang-en |
| EnableBluetoothTask | 开启蓝牙 | Settings | lang-en |
| DisableBluetoothTask | 关闭蓝牙 | Settings | lang-en |

**测试能力**:
- ✅ 显示设置 (亮度/字体/图标)
- ✅ 网络设置 (WiFi/蓝牙/飞行模式)
- ✅ 个性化 (壁纸)
- ✅ 设置项导航

---

### 10. 办公协作 (Work) — 37 个任务

测试 Agent 在企业办公场景中的综合操作能力。

| 任务名称 | 测试内容 | 涉及应用 | 标签 |
|----------|----------|----------|------|
| MattermostCreateChannelTask | 创建 Mattermost 频道 | Mattermost | lang-en |
| MattermostSendMessageTask | 发送 Mattermost 消息 | Mattermost | lang-en |
| MattermostCreatePrivateChannelTask | 创建私有频道 | Mattermost | lang-en |
| MattermostReplyMessageTask | 回复 Mattermost 消息 | Mattermost | lang-en |
| MattermostCreateDirectMessageTask | 创建直接消息 | Mattermost | lang-en |
| MattermostPinMessageTask | 置顶消息 | Mattermost | lang-en |
| MattermostUnpinMessageTask | 取消置顶消息 | Mattermost | lang-en |
| MattermostReactMessageTask | 表情回应消息 | Mattermost | lang-en |
| SearchArxivPaperTask | 搜索 arXiv 论文 | MCP-arXiv, Mail | agent-mcp, lang-en |
| SearchArxivPaperAndSendEmailTask | 搜索论文并邮件发送 | MCP-arXiv, Mail | agent-mcp, lang-en |
| SearchGithubRepoTask | 搜索 GitHub 仓库 | MCP-Github, Chrome | agent-mcp, lang-cn |
| SearchGithubRepoAndSendEmailTask | 搜索仓库并邮件通知 | MCP-Github, Mail | agent-mcp, lang-cn |
| CheckGithubStarsTask | 查看 GitHub Stars | MCP-Github, Chrome | agent-mcp, lang-cn |
| CheckGithubStarsAndSendEmailTask | 查看 Stars 并邮件通知 | MCP-Github, Mail | agent-mcp, lang-cn |
| SearchCodeInGithubTask | GitHub 代码搜索 | MCP-Github, Chrome | agent-mcp, lang-cn |
| CheckGithubIssuesTask | 查看 GitHub Issues | MCP-Github, Chrome | agent-mcp, lang-cn |

**测试能力**:
- ✅ Mattermost 团队协作 (频道/消息/反应)
- ✅ MCP 工具集成 (arXiv/GitHub)
- ✅ 学术信息检索
- ✅ 开发者工具使用
- ✅ 邮件联动

---

## 三、按标签交叉分类

### 按语言分布

| 语言 | 任务数 | 主要类别 |
|------|-------|----------|
| **English** (lang-en) | ~150 | 全部 10 个类别 |
| **Chinese** (lang-cn) | ~50 | Mall, Messages, Native, Map, Work |

### 按交互模式

| 模式 | 标签 | 任务数 | 说明 |
|------|------|-------|------|
| **GUI-Only** | 无特殊标签 | ~110 | Agent 直接操作 GUI 完成 |
| **User Interaction** | agent-user-interaction | ~60 | 需要询问模拟用户获取信息/确认 |
| **MCP Tool** | agent-mcp | ~30 | 需要调用 MCP 工具 (Amap/GitHub/arXiv/jina/stockstar) |

### 按应用依赖

| 应用 | 依赖该应用的任务数 | 任务类别 |
|------|------------------|----------|
| **Messages** | ~35 | 短信通信、地图导航、日历 |
| **Mail** | ~30 | 电子邮件、办公协作、原生应用 |
| **Files** | ~25 | 原生应用、办公协作 |
| **Taodian** | 14 | 电商购物 |
| **Mastodon** | 28 | 社交媒体 |
| **Mattermost** | ~15 | 办公协作 |
| **Maps** | ~12 | 地图导航、电子邮件 |
| **Calendar** | ~10 | 日历管理 |
| **Clock** | ~8 | 日历管理、原生应用 |
| **Chrome** | ~15 | 网页浏览、办公协作 |
| **Camera** | 3 | 原生应用 |
| **Settings** | 10 | 系统设置 |
| **MCP-Amap** | ~15 | MCP 工具 |
| **MCP-Github** | ~5 | MCP 工具 |
| **MCP-arXiv** | ~3 | MCP 工具 |
| **MCP-jina** | ~3 | MCP 工具 |
| **MCP-stockstar** | ~3 | MCP 工具 |

---

## 四、任务难度分级

| 难度 | 特征 | 示例任务 |
|------|------|----------|
| **简单** | 单应用、单步骤操作 | `SendEmailTask`, `SetAlarmTask`, `ChangeBrightnessTask` |
| **中等** | 2-3 个应用联动、条件判断 | `SendEmailWithAttachmentTask`, `SearchWeatherAndSendEmailTask` |
| **复杂** | 多步骤工作流、MCP 工具调用、用户交互 | `PlanRouteWithMapTask`, `CalculateCartPricesByOwnerAskUserTask`, `CheckInvoiceTask4AskUser` |
| **极复杂** | 4+ 应用、多条件评估、后台服务依赖 | `SendInvoiceWithInfoTask` (Docreader+Files+Chrome+Mail), Mattermost + arXiv 组合任务 |

---

## 五、评估方式

| 评估类型 | 说明 | 示例 |
|----------|------|------|
| **状态检查** | 检查 UI 状态、文件是否存在 | `is_successful()` 读取 UI Hierarchy |
| **内容匹配** | 检查文本、数值是否匹配 | 邮件主题/收件人/正文验证 |
| **异步评估** | 等待后台服务响应后评估 | `is_successful_async()` 调用 MCP 工具验证 |
| **回调评估** | 通过回调接口获取结果 | Mall 任务使用 `get_recent_callback_content()` |
| **部分得分** | 返回 `(score, reason)` 元组 | 部分完成的任务可获得 0.5 分 |
