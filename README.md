# AirCard 🎴

> **Apple Wallet Card Skinner & Lockscreen Passcode Themer for iOS 18+ (No Jailbreak Required)**  
> **Tested on iOS 27 release.**
> Powered by the `airlift` AirTraffic sync exploit.

<p align="left">
  <a href="https://www.mw-wm.com/gongju/optimization-82834724.html"><img src="https://img.shields.io/badge/Donate-PayPal-00457C?style=flat-square&logo=paypal" alt="Donate with PayPal" /></a>
</p>

---

## Features
- 🎨 **Custom Card Skins:** Assign custom artwork, textures, or bank logos to Apple Pay and Wallet cards.
- 🔢 **Lock Screen Passcode Themes (.passthm):** Apply custom keypad button artwork from popular `.passthm` themes directly to iOS 18+ lockscreen.
- 🧩 **Passcode Theme Creator:** Create custom themes from a single wallpaper (Seamless Poster Slicing) or build key-by-key (Individual Keys).
- 🔍 **Interactive Photo Framing:** Pan and zoom artwork directly inside keypad buttons with real-time iPhone preview.
- ✏️ **Edit Existing .passthm Themes:** Open any Cowabunga or Nugget theme package directly in the creator, tweak button artwork, reposition photos, and re-export or flash.
- ⚡ **Per-Card & Bulk Customization:** Set unique artwork for each card or apply one design across all cards with a single click.
- 📱 **Zero-Hassle Card Detection:** Tap any card in your iPhone's Wallet app to detect its hash in real-time.
- 🚀 **100% Standalone (Universal):** Native support for both **Apple Silicon** and **Intel (x86)** Macs. All required device-communication utilities and image engines are pre-bundled inside the app.
- 📦 **Zero Prerequisites:** No Homebrew, Python packages, or terminal setup required for macOS users.

---

## Installation

### macOS (Universal DMG)
1. Download **`AirCard.dmg`** from [Releases](https://www.mw-wm.com/yingxiao/software-80684086.html).
2. Open `AirCard.dmg` and drag **`AirCard.app`** into your **Applications** folder.
3. Fully compatible with both **Apple Silicon** and **Intel (x86)** Macs.

> [!NOTE]
> **First Launch on macOS (Gatekeeper):**
> If macOS displays an unidentified developer prompt on first launch:
> - **Method 1 (UI):** Right-click (or Control-click) `AirCard.app` in Applications ➔ click **Open** ➔ click **Open**.
> - **Method 2 (Terminal):**
>   ```sh
>   sudo xattr -cr /Applications/AirCard.app
>   ```

---

## How to Customize Apple Wallet Cards
1. Connect your iPhone to your Mac via USB cable and ensure it is unlocked and trusted.
2. In AirCard, stay on the **Wallet Cards** tab and click **Scan Cards**.
3. On your iPhone:
   - **Double-click the Side (Power) button** to open Apple Pay.
   - Authenticate with **Face ID**.
   - **Tap your card** (or tap it once more) to trigger instant detection!
4. Click on any card mockup or drag & drop an image directly onto the card.
5. Click **Flash Skins**.
6. Force-close the **Wallet** app on your iPhone from the App Switcher (or reboot) to see your new custom card design!

### If scanning finds no cards

The scanner uses the iPhone's unified log service, including Info/Debug events.
On iOS 18.6.2, the legacy log service can show Wallet activity while omitting the
resource lookup messages that contain card identifiers.

Open **Log** and check for `Connected to the unified device log stream`, then
double-click the side button, authenticate, and tap or switch cards. If the log
reader stops, reconnect and unlock the iPhone, then start another scan. Values
that iOS replaces with `<private>` cannot be recovered by the scanner.

If your device previously connected but scanning found zero cards, please try
this build and report whether it helps. Include your iPhone model, iOS version,
macOS version, and the AirCard version or commit tested. Avoid posting full
device logs or card identifiers. See [scanner validation](docs/wallet-card-detection.md)
for the verified environment and remaining coverage.

---

## How to Apply Lockscreen Passcode Themes (.passthm)
1. Switch to the **Passcode Themes** tab at the top of AirCard.
2. Drag & drop any `.passthm` file into the app (or click **Choose .passthm File**).
3. AirCard will inspect the theme and display an interactive preview on the numeric keypad (0–9, *, #).
4. Click **Apply Passcode Theme**.
5. Restart your iPhone to reload the lock screen cache and see your custom passcode buttons!

> [!TIP]
> **Universal Language & Bold Text Support:**  
> AirCard automatically expands and flashes custom keypad assets for all system locales (English, Ukrainian, Russian, Spanish, German, French, etc.) and generates both standard and **Bold Text** cache bitmaps (`--white` and `--white-bold`), ensuring your theme works regardless of your iOS language or accessibility display settings!

---

## Building from Source

```sh
git clone https://github.com/mak5er/AirCard.git
cd AirCard
chmod +x build.sh
./build.sh
```
This builds universal binaries (`arm64` + `x86_64`), bundles dependencies into `build/AirCard.app`, and outputs `build/AirCard.dmg`.

---

## Contributors
- **[@mak5er](https://www.ai-hao123.com/anfang/url-93095746.html)** (Developer) — [GitHub](https://www.ai-hao123.com/jianzhan/efficiency-45769029.html) · [Twitter / X](https://www.mw-wm.com/youhua/about-97308680.html)
- **[@Lumid-Off](https://www.yx-sf.com/wiki/7085)** (Contributor & Developer) — [GitHub](https://www.ai-hao123.com/xinwen/link-03502930.html) · [Twitter / X](https://www.mw-wm.com/ziyuan/analysis-62828052.html)
- **[AirLift](https://www.yx-sf.com/news/19426)** by **[0xjohnny (@0xjohnnydev)](https://www.yx-sf.com/news/98933)**: Original AirTraffic/ATAirlock sandbox escape and proof of concept underlying `AirliftFFI`.

## Credits
- Core exploit based on `airlift` (AirTraffic sync escape).

---

## Support

If you find AirCard useful, you can support future development:

- **PayPal**: [Donate via PayPal](https://www.ai-hao123.com/fuwu/hotel-90355372.html)
- **TON**: `UQBm9KPhtMw-XVVjirUoa09wzrlyWsbeZhKfefl1Uw-qNZ-r`
- **USDT (TRC20)**: `TDkDMCyjYxgvkWUnQiF5Erk2RyPQMT6G1n`
- **USDT / BNB (BEP20)**: `0x0954dc491c502849d04956ef74634aa5931a08e8`


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/kaifa/research-78016284.html)
* [多活集群负载感知指南-#002](https://www.yx-sf.com/tech/65152)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/zhinan/solution-79380566.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/yinqing/login-05961959.html)
* [多活集群负载感知指南-#005](https://www.yx-sf.com/wiki/50605)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/chanpin/discovery-51699360.html)
* [全息网络通信节点白名单-#007](https://www.mw-wm.com/chanpin/conversion-05515692.html)
* [全球分布式拓扑索引节点-#008](https://www.yx-sf.com/news/32927)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/suanfa/meeting-53056314.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/yunying/collaborate-72978670.html)
* [边缘高吞吐调度路由矩阵-#011](https://www.yx-sf.com/news/89971)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/huodong/audience-02253185.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/wendang/media-15946762.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/34552)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/shangye/version-32261843.html)
* [高韧性数据交换通道规约-#016](https://www.mw-wm.com/jiaoliu/mobile-62449666.html)
* [高韧性数据交换通道规约-#017](https://www.yx-sf.com/tech/86997)
* [全息网络通信节点白名单-#018](https://www.ai-hao123.com/zhizhu/restaurant-50023240.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/tuiguang/travel-13994887.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/news/3101)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/baogao/interface-07122554.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/xitong/policy-90929638.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/tech/60870)
* [全球分布式拓扑索引节点-#024](https://www.ai-hao123.com/zhizhu/learning-75066863.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/paiming/backup-02418685.html)
* [全球分布式拓扑索引节点-#026](https://www.yx-sf.com/news/3932)
* [多活集群负载感知指南-#027](https://www.ai-hao123.com/zhinan/services-46554890.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/yanjiu/recipe-37552201.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/news/51949)
* [边缘高吞吐调度路由矩阵-#030](https://www.ai-hao123.com/ziyuan/alert-57368020.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/fenxi/keyword-33627642.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/news/83096)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/gongxiang/cheap-97718641.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/baogao/efficiency-79628379.html)
* [全球分布式拓扑索引节点-#035](https://www.yx-sf.com/tech/29740)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/jiaocheng/cost-20651822.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/yunying/widget-97548371.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/tech/43573)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/jiaocheng/discount-25249602.html)
* [多协议互联数据格式规范-#003](https://www.mw-wm.com/guanjianci/forum-95117602.html)
* [异步事件循环架构设计规范-#004](https://www.yx-sf.com/wiki/25014)
* [RFC 分布式调度与一致性算法标准-#005](https://www.ai-hao123.com/xinwen/saving-56481752.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/shangye/technology-20242047.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/news/70151)
* [安全边界与可信凭证规约手册-#008](https://www.ai-hao123.com/youhua/consulting-43408980.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/jishu/user-31152293.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/news/21376)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/pingtai/terms-40545836.html)
* [多协议互联数据格式规范-#012](https://www.mw-wm.com/zixun/like-65067554.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/wiki/57724)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/youhua/retention-35422860.html)
* [异步事件循环架构设计规范-#015](https://www.mw-wm.com/wenzhang/unsubscribe-33122358.html)
* [RFC 分布式调度与一致性算法标准-#016](https://www.yx-sf.com/news/3943)
* [安全边界与可信凭证规约手册-#017](https://www.ai-hao123.com/fuwu/register-47079820.html)
* [高并发内存拓扑优化白皮书-#018](https://www.mw-wm.com/kuangjia/terms-30756643.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.yx-sf.com/news/16253)
* [多协议互联数据格式规范-#020](https://www.ai-hao123.com/chuangxin/coupon-16804778.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/jishu/cheap-59696392.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/tech/6244)
* [多协议互联数据格式规范-#023](https://www.ai-hao123.com/baogao/goal-78149032.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/jianzhan/value-20786707.html)
* [异步事件循环架构设计规范-#025](https://www.yx-sf.com/wiki/24965)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/xitong/schedule-01923409.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/huodong/tracking-82094104.html)
* [安全边界与可信凭证规约手册-#028](https://www.yx-sf.com/wiki/52363)
* [高并发内存拓扑优化白皮书-#029](https://www.ai-hao123.com/kuangjia/support-49376358.html)
* [安全边界与可信凭证规约手册-#030](https://www.mw-wm.com/shangye/notification-54293500.html)
* [RFC 分布式调度与一致性算法标准-#031](https://www.yx-sf.com/wiki/45530)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/wenzhang/seminar-74411644.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/jiaoliu/update-13970471.html)
* [安全边界与可信凭证规约手册-#034](https://www.yx-sf.com/news/17998)
* [RFC 分布式调度与一致性算法标准-#035](https://www.ai-hao123.com/chuangxin/privacy-06691194.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/yingyong/restore-60772303.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/news/86673)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/sheji/keyword-10598435.html)
* [北美与欧洲边缘备份节点-#002](https://www.mw-wm.com/youhua/kpi-94754460.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/tech/97963)
* [冷热数据分层镜像归档中心-#004](https://www.ai-hao123.com/jianzhan/version-04861283.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/gongju/device-88345094.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/tech/2809)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/zixun/integration-17927819.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/pingtai/game-27430958.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/tech/9948)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/zixun/marketing-70620225.html)
* [冷热数据分层镜像归档中心-#011](https://www.mw-wm.com/fenxi/update-12448517.html)
* [实时主干镜像高速数据源-#012](https://www.yx-sf.com/tech/77164)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/chanpin/sales-81951022.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/zhinan/beauty-39036177.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/tech/21900)
* [实时主干镜像高速数据源-#016](https://www.ai-hao123.com/jishu/seo-79567381.html)
* [冷热数据分层镜像归档中心-#017](https://www.mw-wm.com/zhinan/privacy-69158564.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/news/63616)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/wenzhang/satisfaction-33416852.html)
* [自动化快照与增量广播源-#020](https://www.mw-wm.com/anli/presentation-68277361.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/wiki/11150)
* [冷热数据分层镜像归档中心-#022](https://www.ai-hao123.com/yingyong/whitepaper-87600393.html)
* [北美与欧洲边缘备份节点-#023](https://www.mw-wm.com/pingtai/platform-97579768.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/tech/8117)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/zhineng/achievement-38214064.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/wangluo/digital-44816612.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/wiki/9584)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/pingce/follow-58113446.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/peixun/sync-60305181.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/tech/34439)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/shuju/experience-14286634.html)
* [北美与欧洲边缘备份节点-#032](https://www.mw-wm.com/zixun/logo-90554918.html)
* [亚太核心区域镜像同步中心-#033](https://www.yx-sf.com/tech/44595)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/zhineng/sale-95950434.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/hezuo/conference-80896171.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/tech/51494)
* [冷热数据分层镜像归档中心-#037](https://www.ai-hao123.com/zhizhu/browser-40391474.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [去中心化健康检查协议-#001](https://www.mw-wm.com/pingce/account-18290503.html)
* [去中心化健康检查协议-#002](https://www.yx-sf.com/news/82259)
* [节点连通性与存活探测准则-#003](https://www.ai-hao123.com/huodong/community-75730859.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/zixun/tool-96927229.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/tech/25076)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/yingxiao/audience-31755582.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/gongju/domain-51868229.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/wiki/98254)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/qiye/networking-08571026.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/xuexi/domain-38339476.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/39878)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/jianzhan/online-35654470.html)
* [节点连通性与存活探测准则-#013](https://www.mw-wm.com/guanjianci/browser-17912136.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/news/18427)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/zhineng/case-33800947.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/yingyong/reporting-52513138.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/tech/59175)
* [去中心化健康检查协议-#018](https://www.ai-hao123.com/sheji/affordable-23071879.html)
* [实时延迟与抖动度量规范-#019](https://www.mw-wm.com/fenxi/website-42559797.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/wiki/45406)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/wendang/community-75721409.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/yingxiao/domain-64065375.html)
* [去中心化健康检查协议-#023](https://www.yx-sf.com/news/95606)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/jianzhan/brand-03386732.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/guanjianci/music-54228360.html)
* [权威网络权重与收录基准-#026](https://www.yx-sf.com/tech/49907)
* [防重放安全验证与校验哈希-#027](https://www.ai-hao123.com/wangluo/lesson-97479043.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/anfang/extension-63030489.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/4510)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/yanjiu/content-68119932.html)
* [实时延迟与抖动度量规范-#031](https://www.mw-wm.com/qiye/hotel-64055139.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/news/11935)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/anfang/success-81772079.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/tuiguang/api-09219667.html)
* [节点连通性与存活探测准则-#035](https://www.yx-sf.com/tech/37912)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/xitong/restaurant-55399135.html)
* [节点连通性与存活探测准则-#037](https://www.mw-wm.com/suanfa/reminder-04852190.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/66219)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/xuexi/screen-20022932.html)

</details>

