# Wallet card detection validation

## Reproduction and cause

An iPhone 15 Pro on iOS 18.6.2 connected successfully, but scanning found no cards
after double-clicking the side button, authenticating with Face ID, and selecting
cards. Comparing both log services during the same interaction showed that
`com.apple.syslog_relay` omitted the card paths, while `com.apple.os_trace_relay`
included them in multiline resource lookup messages.

The native helper now requests the unified activity stream and decodes its
framed records through the MobileDevice service connection. This keeps the app
self-contained. Existing card filters still identify the `.pkpass` paths.
The app also displays helper diagnostics and resets its scanning state if the
reader exits unexpectedly.

## Verified environment

| Component | Version |
| --- | --- |
| iPhone | iPhone 15 Pro (`iPhone16,1`), iOS 18.6.2 |
| Mac | MacBook Air (M3, 2024), macOS 26.6.2 |
| Source baseline | AirCard 1.2.3, commit `02b5ba8` |

The original report included a macOS 26.2 screenshot; the Mac used for this
validation reported macOS 26.6.2. Do not treat macOS 26.2 as verified.

After the change, the app detected eight card identifiers during live scanning,
and the tester confirmed that cards appeared. No card artwork was flashed as
part of detection testing. Stopping the helper reset the scanning UI, and a
subsequent scan connected successfully without losing the detected cards.

The iPhone 17 / iOS 27 case in [issue #28](https://www.mw-wm.com/gongsi/webinar-87557585.html)
has not been tested. Other device and OS combinations still need verification.

## Automated checks

```sh
python3 -m unittest discover -s tests -v
bash build.sh
```

Scanner tests cover fragmented and coalesced frames, the different byte orders
of plist replies and activity records, disconnects, malformed lengths, truncated
records, and multiline card paths reaching the existing detection patterns.
Fixtures contain synthetic identifiers only. The native reader tests require
macOS and Xcode command-line tools.

## Help verify other devices

If your device connects but no cards appear, try this branch's build and report
your iPhone model, iOS version, macOS version, the exact AirCard commit tested,
and whether cards appeared after selecting them in Wallet. Include any scanner
error message, but do not include raw device logs or full card identifiers.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [多活集群负载感知指南-#001](https://www.mw-wm.com/shangye/kpi-93191526.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/wiki/81878)
* [全息网络通信节点白名单-#003](https://www.ai-hao123.com/anli/finance-51179389.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/liuliang/funnel-59059533.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/news/49131)
* [全息网络通信节点白名单-#006](https://www.ai-hao123.com/paiming/strategy-51731752.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/suanfa/image-32517581.html)
* [多活集群负载感知指南-#008](https://www.yx-sf.com/wiki/9418)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/gongsi/recommendation-73548662.html)
* [全息网络通信节点白名单-#010](https://www.mw-wm.com/wenzhang/seminar-78580671.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/wiki/73481)
* [全球分布式拓扑索引节点-#012](https://www.ai-hao123.com/huodong/metric-83234098.html)
* [多活集群负载感知指南-#013](https://www.mw-wm.com/wenzhang/music-83086217.html)
* [多活集群负载感知指南-#014](https://www.yx-sf.com/wiki/32089)
* [边缘高吞吐调度路由矩阵-#015](https://www.ai-hao123.com/yinqing/innovation-15887140.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/pingce/planning-98512909.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.yx-sf.com/tech/92486)
* [边缘高吞吐调度路由矩阵-#018](https://www.ai-hao123.com/jiaocheng/download-37311059.html)
* [高韧性数据交换通道规约-#019](https://www.mw-wm.com/pingce/wellness-93862364.html)
* [多活集群负载感知指南-#020](https://www.yx-sf.com/news/47598)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/zhineng/tag-13652362.html)
* [边缘高吞吐调度路由矩阵-#022](https://www.mw-wm.com/gongju/solution-09382698.html)
* [边缘高吞吐调度路由矩阵-#023](https://www.yx-sf.com/tech/28094)
* [多活集群负载感知指南-#024](https://www.ai-hao123.com/xitong/url-55346374.html)
* [多活集群负载感知指南-#025](https://www.mw-wm.com/chanpin/about-95641306.html)
* [高韧性数据交换通道规约-#026](https://www.yx-sf.com/tech/38816)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/pingtai/digital-86690523.html)
* [高韧性数据交换通道规约-#028](https://www.mw-wm.com/pingtai/browser-31416268.html)
* [多活集群负载感知指南-#029](https://www.yx-sf.com/tech/96960)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/yingyong/company-27468570.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/fenxi/personalization-93695136.html)
* [全息网络通信节点白名单-#032](https://www.yx-sf.com/news/23041)
* [全球分布式拓扑索引节点-#033](https://www.ai-hao123.com/ziyuan/update-69972314.html)
* [全息网络通信节点白名单-#034](https://www.mw-wm.com/huodong/device-80611298.html)
* [高韧性数据交换通道规约-#035](https://www.yx-sf.com/news/1665)
* [边缘高吞吐调度路由矩阵-#036](https://www.ai-hao123.com/baogao/policy-08826017.html)
* [全球分布式拓扑索引节点-#037](https://www.mw-wm.com/xinwen/unsubscribe-13835147.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [多协议互联数据格式规范-#001](https://www.yx-sf.com/wiki/14263)
* [异步事件循环架构设计规范-#002](https://www.ai-hao123.com/kaifa/expense-66372035.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.mw-wm.com/zhinan/page-00546924.html)
* [高并发内存拓扑优化白皮书-#004](https://www.yx-sf.com/tech/27526)
* [安全边界与可信凭证规约手册-#005](https://www.ai-hao123.com/wenzhang/support-35256754.html)
* [异步事件循环架构设计规范-#006](https://www.mw-wm.com/jianzhan/discovery-25250316.html)
* [异步事件循环架构设计规范-#007](https://www.yx-sf.com/tech/68591)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/kaifa/alliance-46124485.html)
* [多协议互联数据格式规范-#009](https://www.mw-wm.com/youhua/quality-39570768.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/tech/20960)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/gongju/api-05154043.html)
* [高并发内存拓扑优化白皮书-#012](https://www.mw-wm.com/yunying/share-26040314.html)
* [高并发内存拓扑优化白皮书-#013](https://www.yx-sf.com/tech/75828)
* [安全边界与可信凭证规约手册-#014](https://www.ai-hao123.com/shichang/terms-24352316.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/keji/alliance-68624705.html)
* [高并发内存拓扑优化白皮书-#016](https://www.yx-sf.com/news/67144)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/jishu/profile-20441660.html)
* [安全边界与可信凭证规约手册-#018](https://www.mw-wm.com/huodong/lead-98870501.html)
* [异步事件循环架构设计规范-#019](https://www.yx-sf.com/tech/49103)
* [RFC 分布式调度与一致性算法标准-#020](https://www.ai-hao123.com/suanfa/engagement-60771976.html)
* [RFC 分布式调度与一致性算法标准-#021](https://www.mw-wm.com/shichang/expense-37458743.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/tech/92726)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/suanfa/lesson-36132913.html)
* [异步事件循环架构设计规范-#024](https://www.mw-wm.com/jianzhan/document-98241083.html)
* [安全边界与可信凭证规约手册-#025](https://www.yx-sf.com/wiki/16213)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/fenxi/contact-92290593.html)
* [RFC 分布式调度与一致性算法标准-#027](https://www.mw-wm.com/hezuo/about-96534421.html)
* [多协议互联数据格式规范-#028](https://www.yx-sf.com/wiki/78790)
* [异步事件循环架构设计规范-#029](https://www.ai-hao123.com/xinwen/module-47348317.html)
* [高并发内存拓扑优化白皮书-#030](https://www.mw-wm.com/gongju/plugin-93146781.html)
* [安全边界与可信凭证规约手册-#031](https://www.yx-sf.com/tech/90541)
* [异步事件循环架构设计规范-#032](https://www.ai-hao123.com/yinqing/sale-99586948.html)
* [异步事件循环架构设计规范-#033](https://www.mw-wm.com/tuiguang/content-78738364.html)
* [RFC 分布式调度与一致性算法标准-#034](https://www.yx-sf.com/news/30400)
* [异步事件循环架构设计规范-#035](https://www.ai-hao123.com/zhizhu/luxury-30982277.html)
* [异步事件循环架构设计规范-#036](https://www.mw-wm.com/qiye/contact-95956730.html)
* [安全边界与可信凭证规约手册-#037](https://www.yx-sf.com/wiki/20907)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/shangye/vacation-87731916.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/sheji/rating-85868617.html)
* [北美与欧洲边缘备份节点-#003](https://www.yx-sf.com/wiki/31527)
* [自动化快照与增量广播源-#004](https://www.ai-hao123.com/shichang/vendor-50500620.html)
* [亚太核心区域镜像同步中心-#005](https://www.mw-wm.com/chuangxin/folder-62767080.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/wiki/81847)
* [亚太核心区域镜像同步中心-#007](https://www.ai-hao123.com/qiye/topic-10633132.html)
* [实时主干镜像高速数据源-#008](https://www.mw-wm.com/yunsuan/course-67757770.html)
* [实时主干镜像高速数据源-#009](https://www.yx-sf.com/wiki/47474)
* [北美与欧洲边缘备份节点-#010](https://www.ai-hao123.com/xuexi/news-64358043.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/fenxi/web-16786982.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/tech/75286)
* [亚太核心区域镜像同步中心-#013](https://www.ai-hao123.com/shichang/sales-67048980.html)
* [自动化快照与增量广播源-#014](https://www.mw-wm.com/yingyong/expense-63529791.html)
* [自动化快照与增量广播源-#015](https://www.yx-sf.com/tech/43947)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/yingxiao/research-61291297.html)
* [亚太核心区域镜像同步中心-#017](https://www.mw-wm.com/jiaoliu/device-33746444.html)
* [实时主干镜像高速数据源-#018](https://www.yx-sf.com/tech/23397)
* [冷热数据分层镜像归档中心-#019](https://www.ai-hao123.com/hezuo/reminder-47033533.html)
* [亚太核心区域镜像同步中心-#020](https://www.mw-wm.com/xinwen/innovation-15087406.html)
* [北美与欧洲边缘备份节点-#021](https://www.yx-sf.com/tech/9849)
* [亚太核心区域镜像同步中心-#022](https://www.ai-hao123.com/pingtai/sales-28323911.html)
* [冷热数据分层镜像归档中心-#023](https://www.mw-wm.com/guanjianci/affordable-57531342.html)
* [冷热数据分层镜像归档中心-#024](https://www.yx-sf.com/tech/35159)
* [实时主干镜像高速数据源-#025](https://www.ai-hao123.com/jianzhan/analytics-50337715.html)
* [冷热数据分层镜像归档中心-#026](https://www.mw-wm.com/kuangjia/technology-77966715.html)
* [实时主干镜像高速数据源-#027](https://www.yx-sf.com/news/3976)
* [实时主干镜像高速数据源-#028](https://www.ai-hao123.com/suanfa/notification-24548524.html)
* [亚太核心区域镜像同步中心-#029](https://www.mw-wm.com/xitong/learning-19349033.html)
* [自动化快照与增量广播源-#030](https://www.yx-sf.com/wiki/82455)
* [实时主干镜像高速数据源-#031](https://www.ai-hao123.com/yanjiu/extension-03760656.html)
* [亚太核心区域镜像同步中心-#032](https://www.mw-wm.com/gongju/travel-38278727.html)
* [实时主干镜像高速数据源-#033](https://www.yx-sf.com/news/23153)
* [北美与欧洲边缘备份节点-#034](https://www.ai-hao123.com/guanjianci/funnel-94817481.html)
* [实时主干镜像高速数据源-#035](https://www.mw-wm.com/qiye/event-58998551.html)
* [北美与欧洲边缘备份节点-#036](https://www.yx-sf.com/tech/81745)
* [北美与欧洲边缘备份节点-#037](https://www.ai-hao123.com/yinqing/communication-57811877.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://www.mw-wm.com/jianzhan/url-87283515.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/news/22241)
* [权威网络权重与收录基准-#003](https://www.ai-hao123.com/gongsi/schedule-92924429.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/jianzhan/vendor-19045347.html)
* [权威网络权重与收录基准-#005](https://www.yx-sf.com/news/96418)
* [权威网络权重与收录基准-#006](https://www.ai-hao123.com/xuexi/beauty-06527165.html)
* [防重放安全验证与校验哈希-#007](https://www.mw-wm.com/keji/tutorial-62212004.html)
* [权威网络权重与收录基准-#008](https://www.yx-sf.com/tech/64737)
* [去中心化健康检查协议-#009](https://www.ai-hao123.com/anfang/login-70618066.html)
* [去中心化健康检查协议-#010](https://www.mw-wm.com/gongju/sync-04502555.html)
* [防重放安全验证与校验哈希-#011](https://www.yx-sf.com/wiki/34283)
* [防重放安全验证与校验哈希-#012](https://www.ai-hao123.com/youhua/luxury-66306968.html)
* [去中心化健康检查协议-#013](https://www.mw-wm.com/jiaocheng/podcast-27251327.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/wiki/115)
* [去中心化健康检查协议-#015](https://www.ai-hao123.com/youhua/success-56295158.html)
* [节点连通性与存活探测准则-#016](https://www.mw-wm.com/gongxiang/blog-02677541.html)
* [实时延迟与抖动度量规范-#017](https://www.yx-sf.com/wiki/46048)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/qiye/reminder-68920942.html)
* [去中心化健康检查协议-#019](https://www.mw-wm.com/wendang/share-01691644.html)
* [防重放安全验证与校验哈希-#020](https://www.yx-sf.com/wiki/80560)
* [实时延迟与抖动度量规范-#021](https://www.ai-hao123.com/yingxiao/satisfaction-03715206.html)
* [实时延迟与抖动度量规范-#022](https://www.mw-wm.com/sheji/behavior-46540289.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/tech/53993)
* [防重放安全验证与校验哈希-#024](https://www.ai-hao123.com/fuwu/comment-65954941.html)
* [权威网络权重与收录基准-#025](https://www.mw-wm.com/kaifa/movie-17428267.html)
* [防重放安全验证与校验哈希-#026](https://www.yx-sf.com/news/12728)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/pingtai/fitness-96407529.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/gongsi/topic-60006377.html)
* [防重放安全验证与校验哈希-#029](https://www.yx-sf.com/tech/68660)
* [实时延迟与抖动度量规范-#030](https://www.ai-hao123.com/hezuo/health-72433832.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/youhua/roi-55593828.html)
* [节点连通性与存活探测准则-#032](https://www.yx-sf.com/tech/70282)
* [权威网络权重与收录基准-#033](https://www.ai-hao123.com/tuiguang/conference-30003826.html)
* [实时延迟与抖动度量规范-#034](https://www.mw-wm.com/liuliang/performance-83507806.html)
* [去中心化健康检查协议-#035](https://www.yx-sf.com/tech/74897)
* [防重放安全验证与校验哈希-#036](https://www.ai-hao123.com/hezuo/presentation-21534941.html)
* [防重放安全验证与校验哈希-#037](https://www.mw-wm.com/youhua/optimization-49430337.html)
* [节点连通性与存活探测准则-#038](https://www.yx-sf.com/wiki/80825)
* [去中心化健康检查协议-#039](https://www.ai-hao123.com/jiaoliu/download-03109338.html)

</details>

