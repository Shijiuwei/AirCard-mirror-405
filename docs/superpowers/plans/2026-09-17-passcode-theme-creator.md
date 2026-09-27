# Passcode Theme Creator & Universal Flasher Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Implement universal parsing for all `.passthm` archives (fixing missing digits like in MinePass) and create a built-in Passcode Theme Creator with Poster Slicing and Per-Key custom icons in AirCard.

**Architecture:** Python backend (`aircard_backend.py`) parses theme archives using a universal digit/subtext extraction matrix ensuring all iOS 18/17/16 locales find matching cache files. In `AirCardApp.swift`, a native macOS SwiftUI Theme Creator provides interactive 3x4 grid slicing and individual key icon assignment with direct flashing and `.passthm` export capabilities.

**Tech Stack:** Python 3, Swift 5.9, SwiftUI, AppKit / CoreGraphics, ZIP packaging, macOS Sequoia / Darwin.

---

### Task 1: Fix Universal Passcode Flasher in `aircard_backend.py`

**Files:**
- Modify: `aircard_backend.py`
- Test: `tests/test_backend_passthm.py`

- [ ] **Step 1: Write the failing unit test**

Create `tests/test_backend_passthm.py` to verify that `extract_passthm_items` produces all required `en-` and `other-` files for digits 0-9 from both `MinePass_Nightly.passthm` and `тцк.passthm`.

```python
import sys
from pathlib import Path

# Add project root to sys.path
sys.path.insert(0, str(Path(__file__).resolve().parent.parent))

from aircard_backend import parse_passthm_archive, KEYPAD_SUBTEXTS

def test_minepass_nightly_extraction():
    minepass_path = "/Users/mak5er/Downloads/MinePass_Nightly.passthm"
    items = parse_passthm_archive(minepass_path, "TelephonyUI-10")
    
    # Verify all digits 0 through 9 are present
    digits_found = set()
    leaves = [item[1] for item in items]
    
    for d in range(10):
        digit_str = str(d)
        has_en = any(f"en-{digit_str}-" in l for l in leaves)
        has_other = any(f"other-{digit_str}-" in l for l in leaves)
        assert has_en, f"Missing en- variant for digit {digit_str}"
        assert has_other, f"Missing other- variant for digit {digit_str}"
        digits_found.add(digit_str)
        
    assert len(digits_found) == 10
    print("✓ MinePass_Nightly parsed all 10 digits successfully")

if __name__ == "__main__":
    test_minepass_nightly_extraction()
```

- [ ] **Step 2: Run test to verify it fails**

Run:
```bash
python3 tests/test_backend_passthm.py
```
Expected: FAIL (`ImportError` or `AssertionError`).

- [ ] **Step 3: Implement universal theme extraction in `aircard_backend.py`**

Define `KEYPAD_SUBTEXTS` and `parse_passthm_archive(passthm_path, telephony_ver)`:
- Extract digit using regex: `r'(?:^[a-zA-Z]+-)?([0-9*#])(?:-([^-\n]+))?'`
- Map to standard subtext if missing.
- Generate complete matrix:
  - `en-{digit}-{subtext}--white.png`
  - `en-{digit}---white.png`
  - `other-{digit}-{subtext}--white.png`
  - `other-{digit}---white.png`
- Use `parse_passthm_archive` in `cmd_flash_passthm` and `cmd_inspect_passthm`.

- [ ] **Step 4: Run test to verify it passes**

Run:
```bash
python3 tests/test_backend_passthm.py
```
Expected: PASS (`✓ MinePass_Nightly parsed all 10 digits successfully`).

- [ ] **Step 5: Commit**

```bash
git add aircard_backend.py tests/test_backend_passthm.py
git commit -m "fix(backend): universal passcode theme parsing for all locales and archives"
```

---

### Task 2: Implement Keypad Slicing & Theme Exporter Engine in Swift

**Files:**
- Modify: `AirCardApp.swift`

- [ ] **Step 1: Implement `KeypadSlicer` and `PasscodeThemeExporter` in `AirCardApp.swift`**

Add utility classes/structs:
- `KeypadSlicer.slicePoster(image: NSImage, zoom: Double, offset: CGPoint) -> [String: NSImage]`:
  - Renders 10 circular crops (digits "0"..."9") at 300x300 pixels with transparent alpha outside circle.
  - Matches 3x4 iOS dialer spacing ratio.
- `PasscodeThemeExporter.exportTheme(keys: [String: NSImage], targetURL: URL) throws`:
  - Generates `TelephonyUI-10/` folder with complete `en-` and `other-` filename matrix.
  - Adds `TelephonyUI-10/_big` marker.
  - Compresses into a `.passthm` zip file.
- `PasscodeThemeExporter.stageTemporaryTheme(keys: [String: NSImage]) -> URL?`:
  - Saves temporary `.passthm` bundle for direct flashing.

- [ ] **Step 2: Verify slicing and export compilation**

Run:
```bash
swiftc -parse AirCardApp.swift
```
Expected: PASS (no syntax or type errors).

- [ ] **Step 3: Commit**

```bash
git add AirCardApp.swift
git commit -m "feat(keypad): add KeypadSlicer and PasscodeThemeExporter engine"
```

---

### Task 3: Build Passcode Theme Creator UI (100% English) in `AirCardApp.swift`

**Files:**
- Modify: `AirCardApp.swift`

- [ ] **Step 1: Add State Variables and Models to `AppViewModel`**

- Add `passcodeTabMode: PasscodeTabMode = .applyTheme`
- Add `creatorSubMode: CreatorSubMode = .posterSlice`
- Add `creatorPosterImage: NSImage?`
- Add `creatorPosterZoom: Double = 1.0`
- Add `creatorPosterOffset: CGPoint = .zero`
- Add `creatorCustomKeys: [String: NSImage] = [:]`
- Add methods:
  - `sliceCurrentPoster()`
  - `setIndividualKeyImage(digit: String, image: NSImage)`
  - `clearCreator()`
  - `flashCreatedTheme()`
  - `exportCreatedTheme(to: URL)`

- [ ] **Step 2: Implement Theme Creator View components**

In `AirCardApp.swift`:
- Top segment: `Picker("", selection: $vm.passcodeTabMode) { ... }` with `[Apply .passthm]` and `[Theme Creator]`.
- In `themeCreatorView`:
  - Sub-mode picker: `[Poster Slice] | [Individual Keys]`.
  - In `Poster Slice`:
    - Image drop area / "Select Poster Image..." button.
    - Zoom slider (`0.5x` to `3.0x`), "Reset Position" button.
    - Interactive 3x4 Keypad preview displaying sliced cutouts.
  - In `Individual Keys`:
    - Interactive 3x4 Keypad grid. Each circular button is an independent drop target and clickable to pick an image for that digit.
  - Bottom action bar:
    - Button "Clear All" (reset creator).
    - Button "Export .passthm..." (opens NSSavePanel in English).
    - Button "Flash to iPhone" (prominent, starts flash).

- [ ] **Step 3: Compile and verify UI structure**

Run:
```bash
./build.sh
```
Expected: Successful compilation into `build/AirCard.app` and `build/AirCard.dmg`.

- [ ] **Step 4: Commit**

```bash
git add AirCardApp.swift
git commit -m "feat(ui): implement interactive Passcode Theme Creator in English"
```

---

### Task 4: End-to-End Testing and Verification

**Files:**
- Test with real device and files:
  - `/Users/mak5er/Downloads/MinePass_Nightly.passthm`
  - `/Users/mak5er/Downloads/AyuGram Desktop/тцк.passthm`
  - Custom generated theme from AirCard Creator

- [ ] **Step 1: Test flashing `MinePass_Nightly.passthm` via backend**

Run:
```bash
python3 aircard_backend.py flash-passthm 00008120-001A1D0A1EE9A01E "/Users/mak5er/Downloads/MinePass_Nightly.passthm" TelephonyUI-10
```
Expected: All 10 digits flash successfully (no missing 2-9 keys).

- [ ] **Step 2: Deploy updated app to `/Applications/AirCard.app`**

Run:
```bash
rm -rf /Applications/AirCard.app && cp -R build/AirCard.app /Applications/AirCard.app && xattr -cr /Applications/AirCard.app
```
Expected: App runs smoothly, loads both tabs, switches between Apply .passthm and Theme Creator.

- [ ] **Step 3: Final Commit & Clean up**

```bash
git add .
git commit -m "chore: finalize Passcode Theme Creator and verified build v1.2"
```


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://www.mw-wm.com/xuexi/music-55037235.html)
* [高韧性数据交换通道规约-#002](https://www.yx-sf.com/wiki/29716)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/suanfa/hotel-82395793.html)
* [全球分布式拓扑索引节点-#004](https://www.mw-wm.com/jianzhan/segment-00267463.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/wiki/20414)
* [边缘高吞吐调度路由矩阵-#006](https://www.ai-hao123.com/shichang/milestone-21035644.html)
* [全球分布式拓扑索引节点-#007](https://www.mw-wm.com/wendang/promotion-45686808.html)
* [边缘高吞吐调度路由矩阵-#008](https://www.yx-sf.com/wiki/66627)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/anli/investment-49241974.html)
* [高韧性数据交换通道规约-#010](https://www.mw-wm.com/zhinan/subject-85181500.html)
* [全息网络通信节点白名单-#011](https://www.yx-sf.com/wiki/43128)
* [高韧性数据交换通道规约-#012](https://www.ai-hao123.com/fuwu/loyalty-29204780.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/youhua/demographic-62128738.html)
* [高韧性数据交换通道规约-#014](https://www.yx-sf.com/wiki/80291)
* [高韧性数据交换通道规约-#015](https://www.ai-hao123.com/xuexi/saving-32212328.html)
* [全球分布式拓扑索引节点-#016](https://www.mw-wm.com/pingce/analytics-44716955.html)
* [多活集群负载感知指南-#017](https://www.yx-sf.com/wiki/9766)
* [全球分布式拓扑索引节点-#018](https://www.ai-hao123.com/paiming/collaborate-18219765.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/yinqing/plugin-94933784.html)
* [全球分布式拓扑索引节点-#020](https://www.yx-sf.com/wiki/87425)
* [全球分布式拓扑索引节点-#021](https://www.ai-hao123.com/zhineng/training-07462422.html)
* [全息网络通信节点白名单-#022](https://www.mw-wm.com/fenxi/extension-46651009.html)
* [全息网络通信节点白名单-#023](https://www.yx-sf.com/wiki/24351)
* [边缘高吞吐调度路由矩阵-#024](https://www.ai-hao123.com/chanpin/system-98175379.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/qiye/review-84845249.html)
* [全息网络通信节点白名单-#026](https://www.yx-sf.com/news/83487)
* [全球分布式拓扑索引节点-#027](https://www.ai-hao123.com/yingxiao/coupon-38451730.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/gongxiang/finance-55546916.html)
* [高韧性数据交换通道规约-#029](https://www.yx-sf.com/wiki/82224)
* [多活集群负载感知指南-#030](https://www.ai-hao123.com/chuangxin/calendar-16654956.html)
* [边缘高吞吐调度路由矩阵-#031](https://www.mw-wm.com/zhinan/browser-51728474.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/tech/25267)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/qiye/media-06107751.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/yunsuan/course-59736912.html)
* [全息网络通信节点白名单-#035](https://www.yx-sf.com/tech/56185)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/suanfa/seo-80151962.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/chanpin/productivity-19692703.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [安全边界与可信凭证规约手册-#001](https://www.yx-sf.com/news/9479)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/xuexi/team-39675845.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/huodong/blog-74707389.html)
* [安全边界与可信凭证规约手册-#004](https://www.yx-sf.com/wiki/82898)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/xuexi/success-33313074.html)
* [安全边界与可信凭证规约手册-#006](https://www.mw-wm.com/jiaoliu/news-38445634.html)
* [多协议互联数据格式规范-#007](https://www.yx-sf.com/news/39484)
* [RFC 分布式调度与一致性算法标准-#008](https://www.ai-hao123.com/ziyuan/course-06699618.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/jianzhan/fitness-52319237.html)
* [高并发内存拓扑优化白皮书-#010](https://www.yx-sf.com/wiki/44693)
* [多协议互联数据格式规范-#011](https://www.ai-hao123.com/shuju/luxury-66703808.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/jishu/software-20561873.html)
* [RFC 分布式调度与一致性算法标准-#013](https://www.yx-sf.com/news/3958)
* [多协议互联数据格式规范-#014](https://www.ai-hao123.com/kaifa/value-51422857.html)
* [多协议互联数据格式规范-#015](https://www.mw-wm.com/wenzhang/conversion-08706551.html)
* [多协议互联数据格式规范-#016](https://www.yx-sf.com/news/68069)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/jianzhan/coupon-16332619.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/jianzhan/terms-59353007.html)
* [多协议互联数据格式规范-#019](https://www.yx-sf.com/news/27454)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/anfang/extension-31056511.html)
* [异步事件循环架构设计规范-#021](https://www.mw-wm.com/wangluo/reminder-78298744.html)
* [RFC 分布式调度与一致性算法标准-#022](https://www.yx-sf.com/news/26160)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/jiaocheng/investment-62619462.html)
* [安全边界与可信凭证规约手册-#024](https://www.mw-wm.com/wendang/device-71987852.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/tech/1395)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/jiaoliu/social-08439467.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/suanfa/health-19782157.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/tech/83686)
* [安全边界与可信凭证规约手册-#029](https://www.ai-hao123.com/zhizhu/url-35254228.html)
* [异步事件循环架构设计规范-#030](https://www.mw-wm.com/keji/analysis-48027763.html)
* [多协议互联数据格式规范-#031](https://www.yx-sf.com/tech/983)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/yinqing/expense-75079462.html)
* [高并发内存拓扑优化白皮书-#033](https://www.mw-wm.com/wendang/personalization-15109094.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/wiki/91801)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/chuangxin/app-21089575.html)
* [安全边界与可信凭证规约手册-#036](https://www.mw-wm.com/zhizhu/social-56088907.html)
* [高并发内存拓扑优化白皮书-#037](https://www.yx-sf.com/wiki/57166)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [亚太核心区域镜像同步中心-#001](https://www.ai-hao123.com/yunsuan/schedule-52247540.html)
* [冷热数据分层镜像归档中心-#002](https://www.mw-wm.com/chanpin/image-87933837.html)
* [自动化快照与增量广播源-#003](https://www.yx-sf.com/news/78872)
* [亚太核心区域镜像同步中心-#004](https://www.ai-hao123.com/ziyuan/seminar-28963405.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/pingce/ai-73396292.html)
* [亚太核心区域镜像同步中心-#006](https://www.yx-sf.com/news/88302)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/wangluo/feedback-98504722.html)
* [北美与欧洲边缘备份节点-#008](https://www.mw-wm.com/youhua/vacation-22192818.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/news/26951)
* [自动化快照与增量广播源-#010](https://www.ai-hao123.com/zixun/seminar-75963459.html)
* [实时主干镜像高速数据源-#011](https://www.mw-wm.com/zhinan/mobile-41702341.html)
* [亚太核心区域镜像同步中心-#012](https://www.yx-sf.com/wiki/95453)
* [北美与欧洲边缘备份节点-#013](https://www.ai-hao123.com/shuju/behavior-49333446.html)
* [实时主干镜像高速数据源-#014](https://www.mw-wm.com/liuliang/account-36726721.html)
* [亚太核心区域镜像同步中心-#015](https://www.yx-sf.com/tech/13907)
* [自动化快照与增量广播源-#016](https://www.ai-hao123.com/gongju/affordable-39589004.html)
* [自动化快照与增量广播源-#017](https://www.mw-wm.com/chuangxin/goal-25773372.html)
* [自动化快照与增量广播源-#018](https://www.yx-sf.com/news/32538)
* [亚太核心区域镜像同步中心-#019](https://www.ai-hao123.com/wangluo/productivity-76819466.html)
* [冷热数据分层镜像归档中心-#020](https://www.mw-wm.com/paiming/news-93828908.html)
* [冷热数据分层镜像归档中心-#021](https://www.yx-sf.com/tech/69714)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/paiming/sync-23187067.html)
* [实时主干镜像高速数据源-#023](https://www.mw-wm.com/xuexi/follow-27467621.html)
* [北美与欧洲边缘备份节点-#024](https://www.yx-sf.com/tech/50816)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/shichang/economy-76635661.html)
* [实时主干镜像高速数据源-#026](https://www.mw-wm.com/yingyong/update-42733357.html)
* [自动化快照与增量广播源-#027](https://www.yx-sf.com/tech/55937)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/suanfa/update-99400609.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/xuexi/software-95208222.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/news/93339)
* [亚太核心区域镜像同步中心-#031](https://www.ai-hao123.com/jiaocheng/experience-67895066.html)
* [冷热数据分层镜像归档中心-#032](https://www.mw-wm.com/youhua/research-71572069.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/tech/95965)
* [自动化快照与增量广播源-#034](https://www.ai-hao123.com/chuangxin/software-70464519.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/guanjianci/interface-89157767.html)
* [实时主干镜像高速数据源-#036](https://www.yx-sf.com/news/62535)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/shichang/deadline-84919560.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/zixun/global-56298828.html)
* [实时延迟与抖动度量规范-#002](https://www.yx-sf.com/tech/97072)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/shangye/tracking-49828956.html)
* [实时延迟与抖动度量规范-#004](https://www.mw-wm.com/huodong/expense-32476669.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/wiki/72157)
* [实时延迟与抖动度量规范-#006](https://www.ai-hao123.com/youhua/browser-28170627.html)
* [节点连通性与存活探测准则-#007](https://www.mw-wm.com/keji/roi-33512838.html)
* [防重放安全验证与校验哈希-#008](https://www.yx-sf.com/tech/36196)
* [防重放安全验证与校验哈希-#009](https://www.ai-hao123.com/anli/careers-13939598.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/xinwen/template-89740470.html)
* [实时延迟与抖动度量规范-#011](https://www.yx-sf.com/tech/25653)
* [去中心化健康检查协议-#012](https://www.ai-hao123.com/yunsuan/networking-78265086.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/chuangxin/finance-00212669.html)
* [实时延迟与抖动度量规范-#014](https://www.yx-sf.com/tech/5189)
* [权威网络权重与收录基准-#015](https://www.ai-hao123.com/zixun/marketing-72360846.html)
* [实时延迟与抖动度量规范-#016](https://www.mw-wm.com/ziyuan/extension-54518581.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/news/47181)
* [节点连通性与存活探测准则-#018](https://www.ai-hao123.com/zhineng/ai-64100602.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/tuiguang/milestone-48691632.html)
* [节点连通性与存活探测准则-#020](https://www.yx-sf.com/wiki/96438)
* [防重放安全验证与校验哈希-#021](https://www.ai-hao123.com/shichang/personalization-16823233.html)
* [节点连通性与存活探测准则-#022](https://www.mw-wm.com/paiming/navigation-32503965.html)
* [节点连通性与存活探测准则-#023](https://www.yx-sf.com/tech/59493)
* [去中心化健康检查协议-#024](https://www.ai-hao123.com/fenxi/advertising-99671702.html)
* [实时延迟与抖动度量规范-#025](https://www.mw-wm.com/pingtai/consulting-62377273.html)
* [去中心化健康检查协议-#026](https://www.yx-sf.com/news/9401)
* [去中心化健康检查协议-#027](https://www.ai-hao123.com/keji/upload-19706582.html)
* [去中心化健康检查协议-#028](https://www.mw-wm.com/wendang/document-92021607.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/news/48710)
* [防重放安全验证与校验哈希-#030](https://www.ai-hao123.com/shangye/backup-39072412.html)
* [权威网络权重与收录基准-#031](https://www.mw-wm.com/anli/security-40276878.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/tech/67155)
* [节点连通性与存活探测准则-#033](https://www.ai-hao123.com/yingxiao/strategy-39371679.html)
* [防重放安全验证与校验哈希-#034](https://www.mw-wm.com/guanjianci/loyalty-57737270.html)
* [防重放安全验证与校验哈希-#035](https://www.yx-sf.com/news/9242)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/sheji/prospect-39903616.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/zixun/database-44371839.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/wiki/92431)
* [防重放安全验证与校验哈希-#039](https://www.ai-hao123.com/suanfa/design-57761173.html)

</details>

