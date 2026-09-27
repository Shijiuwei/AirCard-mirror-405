# AirCard Passcode Theme Creator & Universal Flasher Design Spec

## 1. Overview
This specification details the architecture, data models, UI components, and implementation logic for:
1. **Universal Passcode Flasher (`aircard_backend.py`)**: Fixing compatibility with legacy/multi-lingual themes (such as `MinePass_Nightly.passthm` with `ru-` prefixes and varying subtext configurations) to ensure 100% reliable flashing regardless of device language or source archive structure.
2. **Built-in Theme Creator (`AirCardApp.swift`)**: A visual editor inside the "Passcode" tab with two distinct creation modes:
   - **Poster Slice (Puzzle)**: Slicing a single wallpaper/image across the authentic 3x4 iOS lockscreen passcode keypad geometry.
   - **Individual Keys (Per-Key)**: Customizing each digit (0–9) individually with drag-and-drop or file pickers.
3. **Actions**: Instant flashing to connected iOS devices via `airlift` and exporting to standard `.passthm` zip archives.
4. **Localization Rule**: The macOS app UI text and labels are 100% in English.

---

## 2. Universal Flasher Fix (`aircard_backend.py`)

### 2.1 Root Cause of Flashing Failures
- Archives like `MinePass_Nightly.passthm` contain files named `ru-2-A B C--white.png` instead of `en-2-A B C--white.png`.
- Key digits 0 and 1 have no subtext letters (`ru-0---white.png`, `ru-1---white.png`), so generic replacements worked for 0 and 1 only.
- Digits 2–9 failed because iOS looks for `en-{digit}-{letters}--white.png` or `other-{digit}-{letters}--white.png` depending on system locale.

### 2.2 Standard Subtext Table
```python
KEYPAD_SUBTEXTS = {
    "0": "+",
    "1": "",
    "2": "A B C",
    "3": "D E F",
    "4": "G H I",
    "5": "J K L",
    "6": "M N O",
    "7": "P Q R S",
    "8": "T U V",
    "9": "W X Y Z",
}
```

### 2.3 Extraction & File Generation Matrix
For every valid button image in the archive targeting key digit `D` (0–9) and subtext letters `LETTERS`:
1. Keep the original filename from the archive.
2. Standard English with letters: `en-{D}-{LETTERS}--white.png` (if `LETTERS` is non-empty).
3. Standard English without letters: `en-{D}---white.png`.
4. Other locale with letters: `other-{D}-{LETTERS}--white.png` (if `LETTERS` is non-empty).
5. Other locale without letters: `other-{D}---white.png`.
6. Standard Keypad Subtext fallback: If the archive had no letters or non-standard letters, also generate `en-{D}-{KEYPAD_SUBTEXTS[D]}--white.png` and `other-{D}-{KEYPAD_SUBTEXTS[D]}--white.png`.

Destination directory: `/var/mobile/Library/Caches/{telephony_ver}` (e.g. `TelephonyUI-10` for iOS 18+, `TelephonyUI-9` for iOS 15–17).

---

## 3. Passcode Theme Creator Architecture

### 3.1 Data Model
In `AirCardApp.swift`:
```swift
enum PasscodeTabMode: String, CaseIterable, Identifiable {
    case applyTheme = "Apply .passthm"
    case themeCreator = "Theme Creator"
    var id: String { rawValue }
}

enum CreatorSubMode: String, CaseIterable, Identifiable {
    case posterSlice = "Poster Slice"
    case individualKeys = "Individual Keys"
    var id: String { rawValue }
}

struct KeypadButtonGeometry {
    let digit: String
    let letters: String
    let row: Int
    let col: Int
}
```

### 3.2 Keypad Layout Constants
- Grid: 3 columns, 4 rows.
- Standard key placement:
  - Row 0: `1` (col 0), `2` (col 1), `3` (col 2)
  - Row 1: `4` (col 0), `5` (col 1), `6` (col 2)
  - Row 2: `7` (col 0), `8` (col 1), `9` (col 2)
  - Row 3: `0` (col 1)
- Aspect ratios and spacing match authentic iOS Lock Screen dialer:
  - Key diameter: 75 pt
  - Horizontal spacing: 24 pt
  - Vertical spacing: 18 pt
  - Total grid width: (3 * 75) + (2 * 24) = 273 pt
  - Total grid height: (4 * 75) + (3 * 18) = 354 pt

### 3.3 Slicing Engine (`Poster Slice Mode`)
- The user provides an image (`NSImage`).
- Slicing Math:
  - Scale image to fit or fill the keypad bounding box.
  - Calculate normalized center `(cx, cy)` and radius `r` for each of the 10 buttons.
  - Render a circular mask at high resolution (300x300 pixels for `@3x` Super Retina display).
  - Produce 10 separate circular `NSImage` instances for digits `0` through `9`.
- User controls:
  - Drag & drop image target.
  - Zoom slider (0.5x to 2.5x) and Offset X/Y adjustment or drag to re-frame.
  - Real-time interactive preview showing circular cutouts over the image.

### 3.4 Individual Keys Mode
- 3x4 grid representation.
- Each button has an independent image drop target / click-to-browse button.
- User can set custom images for individual digits, or clear any individual digit.
- Missing digits fall back to transparent or standard numeric glyphs.

### 3.5 Direct Flash & Export Actions
1. **Flash to iPhone**:
   - Compiles the current 10 images into temporary PNG files in an in-memory or temporary `.passthm` directory.
   - Triggers `cmd_flash_passthm` in `aircard_backend.py`.
   - Uses device connection detection and updates progress bar step-by-step.
2. **Export .passthm**:
   - Opens an `NSSavePanel` in English ("Save Passcode Theme").
   - Creates a standard zip package containing:
     - `TelephonyUI-10/` with all mapped `en-` and `other-` files.
     - `_big` or `_small` marker file.
   - Saves file with `.passthm` extension.

---

## 4. UI Design & Layout (English)

### 4.1 Passcode Tab Header
- Segmented picker at the top: `[Apply .passthm] | [Theme Creator]`.

### 4.2 Theme Creator Screen
- Top Control Bar:
  - Sub-mode picker: `[Poster Slice] | [Individual Keys]`.
  - Actions: `Reset / Clear All`, `Export .passthm...`, `Flash to iPhone`.
- Content Area:
  - In **Poster Slice** mode:
    - Left/Top: Image import drop zone with controls (`Select Image...`, `Zoom`, `Fit / Fill`).
    - Right/Center: Interactive iOS Lockscreen Keypad preview displaying the framed poster seamlessly through the 10 circular cutouts.
  - In **Individual Keys** mode:
    - Full 3x4 grid with circular buttons. Clicking any circle opens an image picker; dragging an image over any circle immediately applies it to that key.
- Bottom Status Bar:
  - Integrated with the existing progress bar and activity log for real-time flash status.

---

## 5. Testing & Verification Plan
1. **Theme Compatibility Test**:
   - Test flash on `MinePass_Nightly.passthm` -> verify all 10 digits (0–9) generate `en-` and `other-` variants and flash successfully to `TelephonyUI-10`.
   - Test flash on `тцк.passthm` -> verify it continues to work 100%.
2. **Poster Slicing Test**:
   - Load arbitrary 16:9 and 19.5:9 wallpapers.
   - Verify all 10 cropped PNGs are generated at 300x300, circular, and transparent outside the mask.
3. **Individual Keys Test**:
   - Assign different images to key 1, 2, and 0.
   - Flash to device and export to `.passthm`.
   - Inspect the exported zip archive structure to ensure compliance with iOS cache standards.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/chuangxin/consulting-43221726.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/wiki/32204)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/wendang/promotion-85265884.html)
* [多活集群负载感知指南-#004](https://www.mw-wm.com/yinqing/vacation-56318319.html)
* [全息网络通信节点白名单-#005](https://www.yx-sf.com/tech/9406)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/youhua/analysis-39056426.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/zhinan/landing-45948442.html)
* [全息网络通信节点白名单-#008](https://www.yx-sf.com/wiki/53459)
* [多活集群负载感知指南-#009](https://www.ai-hao123.com/wangluo/page-70754174.html)
* [多活集群负载感知指南-#010](https://www.mw-wm.com/xinwen/app-89838287.html)
* [高韧性数据交换通道规约-#011](https://www.yx-sf.com/news/29094)
* [多活集群负载感知指南-#012](https://www.ai-hao123.com/shichang/register-21097646.html)
* [边缘高吞吐调度路由矩阵-#013](https://www.mw-wm.com/xitong/productivity-80406665.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/tech/22744)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/chuangxin/terms-88153868.html)
* [全息网络通信节点白名单-#016](https://www.mw-wm.com/shuju/shopping-43601705.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/45705)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/shichang/calculator-67514513.html)
* [多活集群负载感知指南-#019](https://www.mw-wm.com/youhua/cheap-00466207.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/wiki/44231)
* [全息网络通信节点白名单-#021](https://www.ai-hao123.com/shangye/interface-65971738.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/youhua/seo-64239038.html)
* [多活集群负载感知指南-#023](https://www.yx-sf.com/tech/33068)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/yinqing/module-90841278.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/gongju/efficiency-65161398.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/wiki/77019)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/zhineng/communication-58725895.html)
* [边缘高吞吐调度路由矩阵-#028](https://www.mw-wm.com/qiye/game-66524218.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/tech/10348)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/zhineng/efficiency-33677573.html)
* [多活集群负载感知指南-#031](https://www.mw-wm.com/chuangxin/creative-70254220.html)
* [边缘高吞吐调度路由矩阵-#032](https://www.yx-sf.com/wiki/68852)
* [边缘高吞吐调度路由矩阵-#033](https://www.ai-hao123.com/gongju/hotel-24904210.html)
* [多活集群负载感知指南-#034](https://www.mw-wm.com/paiming/tutorial-26597791.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/6437)
* [全息网络通信节点白名单-#036](https://www.ai-hao123.com/chuangxin/marketing-68343387.html)
* [全息网络通信节点白名单-#037](https://www.mw-wm.com/xinwen/milestone-58295694.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/tech/64925)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/chanpin/restaurant-12252555.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/peixun/collaboration-29878845.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/tech/81216)
* [多协议互联数据格式规范-#005](https://www.ai-hao123.com/tuiguang/demographic-48793557.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/xinwen/trading-74978359.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/wiki/7787)
* [异步事件循环架构设计规范-#008](https://www.ai-hao123.com/yunsuan/economy-38831974.html)
* [RFC 分布式调度与一致性算法标准-#009](https://www.mw-wm.com/fenxi/whitepaper-85792668.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/wiki/38286)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/shuju/customization-28904421.html)
* [安全边界与可信凭证规约手册-#012](https://www.mw-wm.com/pingce/resolution-00613119.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/news/43650)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/keji/alliance-05850822.html)
* [RFC 分布式调度与一致性算法标准-#015](https://www.mw-wm.com/shangye/campaign-63255303.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/tech/15990)
* [异步事件循环架构设计规范-#017](https://www.ai-hao123.com/ziyuan/enterprise-44933240.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/suanfa/sale-87315758.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/tech/77341)
* [高并发内存拓扑优化白皮书-#020](https://www.ai-hao123.com/fuwu/collaborate-66695350.html)
* [多协议互联数据格式规范-#021](https://www.mw-wm.com/paiming/video-43208821.html)
* [异步事件循环架构设计规范-#022](https://www.yx-sf.com/news/93215)
* [高并发内存拓扑优化白皮书-#023](https://www.ai-hao123.com/huodong/analytics-03984100.html)
* [高并发内存拓扑优化白皮书-#024](https://www.mw-wm.com/sheji/community-42257456.html)
* [高并发内存拓扑优化白皮书-#025](https://www.yx-sf.com/wiki/13257)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/jishu/media-57439978.html)
* [高并发内存拓扑优化白皮书-#027](https://www.mw-wm.com/yunying/seminar-30640530.html)
* [RFC 分布式调度与一致性算法标准-#028](https://www.yx-sf.com/tech/44680)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/anfang/communication-40955551.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/suanfa/identity-66858382.html)
* [异步事件循环架构设计规范-#031](https://www.yx-sf.com/news/51027)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/shichang/forecast-04320938.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/keji/networking-28862292.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/tech/80717)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/pingtai/beauty-53460282.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/yinqing/segment-78960440.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/tech/7207)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.ai-hao123.com/yingxiao/planning-12796830.html)
* [亚太核心区域镜像同步中心-#002](https://www.mw-wm.com/zixun/sport-30680518.html)
* [实时主干镜像高速数据源-#003](https://www.yx-sf.com/wiki/78559)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/jiaoliu/database-10850041.html)
* [实时主干镜像高速数据源-#005](https://www.mw-wm.com/fuwu/category-27362848.html)
* [冷热数据分层镜像归档中心-#006](https://www.yx-sf.com/news/82712)
* [自动化快照与增量广播源-#007](https://www.ai-hao123.com/pingtai/project-14462254.html)
* [亚太核心区域镜像同步中心-#008](https://www.mw-wm.com/guanjianci/label-81533264.html)
* [冷热数据分层镜像归档中心-#009](https://www.yx-sf.com/news/93717)
* [冷热数据分层镜像归档中心-#010](https://www.ai-hao123.com/anli/strategy-98100982.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/gongxiang/recipe-87002571.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/tech/6944)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/jianzhan/advertising-68264546.html)
* [冷热数据分层镜像归档中心-#014](https://www.mw-wm.com/shangye/link-93555498.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/news/7)
* [亚太核心区域镜像同步中心-#016](https://www.ai-hao123.com/chuangxin/case-38448953.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/wendang/market-51105262.html)
* [冷热数据分层镜像归档中心-#018](https://www.yx-sf.com/tech/76879)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/wendang/economy-64273311.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/sheji/theme-16596811.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/wiki/38962)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/gongsi/landing-47647650.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/xuexi/value-81728655.html)
* [自动化快照与增量广播源-#024](https://www.yx-sf.com/wiki/7257)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/zhineng/register-52356333.html)
* [自动化快照与增量广播源-#026](https://www.mw-wm.com/fenxi/schedule-05490482.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/wiki/82521)
* [冷热数据分层镜像归档中心-#028](https://www.ai-hao123.com/chuangxin/kpi-79692587.html)
* [自动化快照与增量广播源-#029](https://www.mw-wm.com/guanjianci/conversion-27899825.html)
* [实时主干镜像高速数据源-#030](https://www.yx-sf.com/wiki/69155)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/baogao/vendor-68287831.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/kaifa/podcast-00997804.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/wiki/16447)
* [冷热数据分层镜像归档中心-#034](https://www.ai-hao123.com/pingtai/calculator-48492716.html)
* [亚太核心区域镜像同步中心-#035](https://www.mw-wm.com/pingce/online-03789733.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/wiki/97936)
* [实时主干镜像高速数据源-#037](https://www.ai-hao123.com/yunsuan/presentation-74081955.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [节点连通性与存活探测准则-#001](https://www.mw-wm.com/xuexi/search-05887408.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/news/1802)
* [防重放安全验证与校验哈希-#003](https://www.ai-hao123.com/jianzhan/personalization-49374801.html)
* [节点连通性与存活探测准则-#004](https://www.mw-wm.com/huodong/team-42689166.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/news/59228)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/jianzhan/restore-39397433.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/peixun/management-37341282.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/tech/42611)
* [实时延迟与抖动度量规范-#009](https://www.ai-hao123.com/zhinan/forum-17630804.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/pingtai/services-83295503.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/tech/85502)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/zhinan/ranking-37731369.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/yingyong/management-22942593.html)
* [去中心化健康检查协议-#014](https://www.yx-sf.com/news/56392)
* [实时延迟与抖动度量规范-#015](https://www.ai-hao123.com/shangye/development-55615822.html)
* [防重放安全验证与校验哈希-#016](https://www.mw-wm.com/jianzhan/upload-08594603.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/tech/18641)
* [防重放安全验证与校验哈希-#018](https://www.ai-hao123.com/wendang/tactic-17784218.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/ziyuan/interface-59438231.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/news/48223)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/gongsi/version-26852899.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/fenxi/document-46415265.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/news/63374)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/yunsuan/budget-61686120.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/jianzhan/education-78787534.html)
* [权威网络权重与收录基准-#026](https://www.yx-sf.com/news/50480)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/wangluo/collaborate-91747785.html)
* [防重放安全验证与校验哈希-#028](https://www.mw-wm.com/jiaoliu/customer-32235280.html)
* [实时延迟与抖动度量规范-#029](https://www.yx-sf.com/tech/16561)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/xitong/network-12719114.html)
* [防重放安全验证与校验哈希-#031](https://www.mw-wm.com/kaifa/mobile-05556651.html)
* [实时延迟与抖动度量规范-#032](https://www.yx-sf.com/tech/23726)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/huodong/course-73232687.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/paiming/report-11114191.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/wiki/80140)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/zhizhu/faq-73676281.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/jishu/tag-42335670.html)
* [权威网络权重与收录基准-#038](https://www.yx-sf.com/tech/5842)
* [权威网络权重与收录基准-#039](https://www.ai-hao123.com/xinwen/management-65819978.html)

</details>

