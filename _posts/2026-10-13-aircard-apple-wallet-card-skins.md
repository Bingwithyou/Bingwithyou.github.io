---
title: '银行卡也能换“壁纸”？这个开源工具，让 Apple 钱包变得有点意思'
date: 2026-10-13 15:09:52 +0800
description: '介绍 AirCard 及其 Windows 移植版：为 Apple 钱包中已有卡片更换卡面图片，附 USB 操作步骤、兼容性与恢复限制，以及 CardArt 卡图画廊。'
categories: [开源工具]
tags: [AirCard, Apple 钱包, Apple Pay, iOS, macOS, Windows, 开源]
---

手机壁纸、聊天背景、电脑桌面，大家都换过。

但你有没有想过，Apple 钱包里的银行卡，也能换成自己喜欢的图案？

最近受到关注的开源项目 **AirCard**，做的就是这件事：不用越狱，把 iPhone 钱包中已有卡片的默认图片，换成你准备好的新卡面。它有原作者维护的 Mac 版，也有社区移植的 Windows 版。[1][2]

先说清楚：**它不会帮你开卡，也不会把普通银行卡变成真正的“黑卡”。换的是外观，不是账户、额度和权益。**[3]

![AirCard Mac 版公开界面截图](/assets/img/aircard/aircard-mac.png)

*图 1｜AirCard Mac 版界面。图源：项目 GitHub 公开贡献讨论；为历史版本/贡献者界面截图，仅供参考，不代表当前发行版的完整功能。*

## 01｜它到底是怎么做到的？

可以把 AirCard 理解成一个“卡面图片替换器”，但它并不是通过苹果提供的常规换肤功能实现的。

它依赖 **AirLift**：利用 iOS 的 AirTraffic 媒体同步服务中的目录访问漏洞，在手机与电脑完成配对后，把自定义图片写入对应卡片的本地资源位置，再刷新钱包的显示缓存。[4][5]

把技术细节压缩成一句话，就是：

> **找到已有卡片 → 替换显示用的图片 → 让钱包重新加载。**

而 Apple Pay 使用的设备账号等支付凭证，存放在独立的安全元件（Secure Element）中，并不是卡面图片的一部分。因此，改图片不等于改支付凭证。[6]

不过，**“只换外观”不等于“整个操作绝对安全”**。它毕竟利用了系统漏洞，还需要你信任连接的电脑。开源意味着代码可以被检查，不代表任何下载包都自动可信。

## 02｜开始前，先确认这几件事

准备一部已经添加好钱包卡片的 iPhone、一台 Mac 或 Windows 电脑，以及一根支持数据传输的 USB 线。Windows 版要求 Windows 10 / 11（64 位），还需要 Apple 设备通信组件，普通用户可通过安装 64 位 iTunes 获取。[2][3]

更重要的是核对系统兼容性：**不要把项目名称里的“iOS 18+”，理解成后续所有系统都能用。**

截至 2026 年 10 月 9 日，项目兼容表列出支持 iOS 18.0—27.0.1、iOS 27.2 beta 1—2，并明确将 iOS 27.2 beta 3 及以后标记为已修复漏洞、无法刷入。操作前请再次核对项目的最新说明；不建议为了换卡面而刻意放弃安全更新。[1]

建议先备份重要数据，并从一张容易辨认的卡开始尝试，不要第一次就批量修改所有常用卡。

## 03｜怎么用？按这五步来

下面以 USB 连接的桌面版为例，Mac 和 Windows 的按钮名称一并标出。

### 第一步：从项目 Releases 下载程序

Mac 用户进入 `Mak5er/AirCard` 的 Releases 页面，下载 **AirCard.dmg**，打开后将应用拖入“应用程序”。Windows 用户进入 `Lumid-Off/AirCard-Windows` 的 Releases 页面，下载 **aircard.exe**。[2][3]

注意下载可运行的程序，不要误把 **Source code.zip** 当成安装包。截至本文整理时，Mac 最新发布版为 **v1.2.6**，Windows 为 **v1.2.4**；之后以各自 Releases 页面为准。[7][8]

Windows 遇到手机无法识别或刷入超时时，应先检查 Apple 驱动和 iTunes。项目中已有驱动冲突的反馈，别为了修一个问题反复叠装多种手机助手。[9]

### 第二步：连接手机，确认信任

用数据线连接 iPhone 和电脑，解锁手机。看到“要信任此电脑吗？”时，仅在你确认这台电脑可信的前提下选择“信任”，并在 **iPhone 上**完成密码确认。[10]

这里不是给程序提供银行卡密码，也不需要把锁屏密码交给别人。

### 第三步：开始扫描，再在手机上点选卡片

Mac 版点击 **Scan Cards**；Windows 版在 Wallet 页面点击 **Scan**。

然后拿起 iPhone，打开钱包，或双击侧边按钮唤出 Apple Pay，按提示完成身份验证，再点选准备更换卡面的那张卡。等待电脑识别到目标后，Windows 版可点击 **Stop** 停止扫描。[2][3]

**这一步最容易漏掉：点击“扫描”之后，还要在手机上实际点选卡片。**扫描不到时，可以切换到另一张卡，再切回来，不要只盯着电脑等待。[3]

### 第四步：导入图片，检查预览

Mac 版可以点击目标卡片选图，也可以把图片拖到卡片上；Windows 版点击 **Choose Image…（选择图片）**。[2][3]

建议提前准备 **1536 × 969 像素的横向 PNG**。其他比例的图片可能被裁切，重要图案不要贴着边缘。尤其钱包里有多张卡时，建议给设计保留明显区别，避免付款时选错。[2][3]

![AirCard Windows 版选图与应用卡面界面](/assets/img/aircard/aircard-windows.png)

*图 2｜AirCard Windows 版界面，可见 Choose Image、Apply Card Skin 和日志区域。图源：项目 GitHub 公开反馈；仅作界面参考，不作为操作成功的证明。*

### 第五步：应用卡面，重新打开钱包

确认目标卡片和图片都正确后，Mac 版点击 **Flash Skins**，Windows 版点击 **Apply Card Skin（应用卡片皮肤）**。等待处理完成，过程中保持手机解锁，不要拔掉数据线。[2][3]

随后在 iPhone 的多任务界面划掉“钱包”App，再重新打开。没有立即更新时，可按项目说明尝试重启手机。[3]

## 04｜动手之前，还要知道两个边界

**第一，别把“信任此电脑”当成一个无关紧要的按钮。**

苹果的说明明确指出，受信任的电脑可以同步并访问设备上的照片、视频、通讯录等内容。因此，只在自己的可信电脑上操作，不要为换一张卡面，随便把手机交给陌生人远程处理，也不要公开上传完整设备日志或配对文件。[10]

**第二，不要默认一定有“一键撤销”。**

Windows 版提供 **Restore Original（恢复原卡面）**，但前提是确实备份到了原始图片。项目贡献者明确说明，原图备份是尽力尝试，并不是每张卡都能成功；不能因为界面上有这个功能，就认为一定能恢复。[11]

需要移除卡片后重新添加时，先确认发卡行的重新验证要求；涉及交通卡，还要核对余额、票卡和迁移规则，不要为了恢复图片直接删掉常用卡。系统更新或缓存刷新，也可能让自定义卡面恢复原样。[12]

## 05｜最后推荐一个卡图网站：CardArt

工具准备好了，下一步往往不是“怎么换”，而是“换成什么”。

这里推荐 **CardArt（cardart.cc）**，一个为 Apple 钱包卡面准备的社区画廊。[13]

它和 AirCard 不是一回事：**AirCard 负责替换卡面，CardArt 负责提供和制作图片。**

![CardArt 社区卡图画廊](/assets/img/aircard/cardart-gallery.png)

*图 3｜CardArt 画廊的公开演示截图，图源：Korben；页面样式及收录数量以当前网站为准，卡图版权归各自作者。*

在网站上挑选喜欢的设计，下载 **1536 × 969 的 PNG 原图**，再回到 AirCard 导入即可。不要只截一张网页缩略图，既容易损失清晰度，也可能带上不需要的页面元素。[13]

网站还提供在线卡面制作入口，可以自己做图，再导出使用。社区作品适合寻找灵感，但能下载不等于可以随意商用；涉及品牌、角色或其他作者的设计时，注意相应权利。[13]

## 06｜项目及素材入口

Mac 版：[AirCard Releases](https://github.com/Mak5er/AirCard/releases)

Windows 版：[AirCard-Windows Releases](https://github.com/Lumid-Off/AirCard-Windows/releases)

卡图网站：[CardArt](https://cardart.cc/)

不是每个开源项目都要提高生产力。有时候，让每天都会看到的小东西更合自己的心意，也是一种乐趣。

**换的是卡面，不是卡本身。知道它能做什么，也知道它不能做什么，再去折腾，才更有意思。**

---

### 资料来源

1. [AirCard 项目与兼容性](https://github.com/Mak5er/AirCard)
2. [AirCard-Windows 项目说明](https://github.com/Lumid-Off/AirCard-Windows)
3. [AirCard 简体中文使用指南](https://github.com/Mak5er/AirCard/blob/main/docs/guides/README.zh-CN.md)
4. [AirLift 原始研究](https://github.com/0xjohnnydev/airlift)
5. [AirCard 图片写入实现](https://github.com/Mak5er/AirCard/blob/main/apply_card_skin.py)
6. [Apple：隐私与 Apple Pay](https://www.apple.com/privacy/features/)
7. [AirCard 发布版本](https://github.com/Mak5er/AirCard/releases)
8. [AirCard-Windows 发布版本](https://github.com/Lumid-Off/AirCard-Windows/releases)
9. [Windows 设备驱动问题反馈](https://github.com/Lumid-Off/AirCard-Windows/issues/8)
10. [Apple：“要信任此电脑吗”说明](https://support.apple.com/zh-cn/109054)
11. [原卡面备份与恢复的实现说明](https://github.com/Lumid-Off/AirCard-Windows/pull/10)
12. [CardArt：恢复原来的卡面](https://cardart.cc/guide/restore?lang=zh)
13. [CardArt 社区及在线编辑器](https://cardart.cc/)

### 配图来源

1. [图 1：AirCard 公开贡献讨论](https://github.com/Mak5er/AirCard/pull/23)
2. [图 2：AirCard-Windows 公开反馈](https://github.com/Lumid-Off/AirCard-Windows/pull/10)
3. [图 3：Korben 的公开演示](https://korben.info/en/aircard-customize-bank-cards-apple-pay.html)
