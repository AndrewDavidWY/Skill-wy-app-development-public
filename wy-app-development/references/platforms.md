# Android 手机、Android TV 与 macOS

以下规则由实际源文件和原始 QA 交叉核对，本次没有启动这些应用重新验收。来源 E5–E7。

## 真实输入、页面位置与持久化分开

三个平台都遇到阅读工具显隐与滚动相互影响的问题，但不能共用同一事件判断：

- Android Compose：`ReaderChrome.kt` 只处理 `NestedScrollSource.UserInput`，排除横向主导输入，同向累计、反向重置，到顶恢复。历史阈值 24dp。
- TV：`MainActivity.kt` 从触摸 rawY 累积方向位移，不依赖视口变化的 scroll 回调。历史阈值 28dp。
- macOS WebKit：`ArticleView.swift` 的 wheel 过滤非可信、缩放和横向输入，统一 deltaMode；180ms 分段，收起/恢复使用不同阈值。普通 scroll 单独延迟上报进度。

复用的是拆分意图的方式，不是复制数值。验收慢反向、短文、顶部、底部、程序回顶、恢复读位和隐藏工具后视口重排。分别量小按钮视觉、点击区域和正文边界；手机历史采用 32dp 图形/48dp 点击区，TV 保留更大的控件区域。

## TV：每次 UI 变化都要定义焦点去向

- 隐藏当前聚焦工具前，把焦点交给正文。
- 定义方向键上下翻页、左键恢复工具、右键进入回顶的回路。
- 图片上一张/下一张按钮在边界禁用时，不能让焦点留在禁用控件；移到图片。
- 关闭图片或正文后，回原控件/列表位置。

用可达控件、方向出口和返回目标描述每个状态。触摸滑动通过不代表 D‑pad、Back、OK 或实体遥控器通过。

真实断网需要系统无有效默认网络的证据。E6 的日志记录 `Active default network: none`，不是单凭关闭 Wi‑Fi。它使用 Android 15 通用系统镜像配置的电视尺寸，**不是实体 Android TV**。离线文章、书章、图库和遍历记录有价值，但不可升级为物理设备验收。

原主 QA 日志在八组通过后有 AssertionError，原因是脚本认为一次返回已退出搜索，实际仍在输入框；后续 tail 日志补测复制、外观重启、七语、慢反向。应报告“失败与续跑范围”，不是“一次全绿”。

## Android：下载提交和整书快照

`OfflineCache.kt` 对下载源限制 HTTPS、允许 host、凭据/端口/fragment，缓存按完整 URL hash 命名。下载不跟随重定向，检查响应和大小，流式写 `.part`，循环检查取消；核对长度后再提交，失败清残片。

`OfflineBooks.kt` 在 staging 校验章节身份唯一合法、非空正文，分组并发章节与配图；下载前后 releaseId 必须一致，防止混合发布。`PortableOffline.kt` 用 AtomicFile 写包，校验 schema/id/locale，文件丢失时清除内存缓存。

**已知边界**：所查 `OfflineBooks.kt` 替换路径有先删除旧目标再 rename 的步骤。它不能作为“所有替换失败都可完整回滚”的范例。当前 UI 阻止重复下载已存在书，也不证明底层任意调用安全。后续改善可采用可恢复备份/目录切换，但这是建议，尚未在本次实现或验证。

Android 正文主要为原生组件。`NativeLinks.kt` 的 WebView 是 InAppBrowser，不要将其写成“正文 WebView 修复”。它禁 file/content access、限制导航目标，仅主框架错误影响主页面失败状态，释放时停止加载并销毁。

## macOS：内容库更新与用户数据独立

`Library.swift` 更新库时：限制大小 → 随机 staging → 实际打开并验证数据库 → 比较 builtAt → 有远端描述时核对 releaseID/commit/date → 提交前再次检查当前安装版本 → rename。后置检查阻止较旧并发任务覆盖新版本。

收藏与阅读进度不随库替换重置。历史核心测试包含旧版用户记录解码兼容，但未来 schema 变化仍需重测迁移。

## macOS：WebKit 路由和性能

`ArticleView.swift` 的 pageKey 使用内容 ID、资料库 releaseID、界面语言。只有 key 变更才重新 load；字号和主题经 applyControls 更新，避免每次外观变化重建正文。这里界面语言仍参与 macOS pageKey，切语言可能触发重载；不能直接套用 iOS 保留当前正文实例的实现结论。若要求切语言保位，需独立验证重载后的内容身份、语言与进度恢复。

页面/token/查找请求 ID 绑定异步结果；进度消息要求当前 token 和主 frame，防快速换文后旧页写入新文读位。内容进程终止时重载当前页。

内部 `ytf` 方案只响应当前 reader 页面或数据库 asset；页面脚本默认禁用，应用自有脚本和消息通道隔离。只允许当前阅读路径留在正文 WebView，用户激活的链接再路由给宿主。

图片资源 ID 要有合法格式。只改写真正图片 viewer 的锚点，不能把所有 img 外层链接变为看图，否则图文入口会失去文章导航。离线验收应点缩略图、原图、图注 viewer 链接，不能只看缩略图已出现。

这些是减少重载/过期回调的机制证据，没有量化耗时或帧率基准，不能声称提升百分比。

## 复制、签名与统计的独立证据

复制文章/章节链接要实际粘贴核对 canonical URL。菜单、点击和 Toast 都不足以证明复制正确；不能复制内部资源地址或旧语言地址。

历史手机资料记录 14 项 UI、Lint 0 error、相同签名覆盖与 16KB 对齐等结果；TV 留有 exact APK 覆盖、firstInstallTime 保持等记录。没有在本任务重跑，不写成新的四端动态验证。

macOS 包是本地预览签名，不是 Developer ID/公证发布。保留两个 UI 缺口：半身页面宽窄/明暗的原生窗口复核待完成；正文 WebKit 有焦点时 Escape 未触发 SwiftUI 退出处理。

Android 构建默认关闭 wyAnalytics；启用要求现有 Firebase 配置及相应插件/依赖。配置一致性不证明事件入库，本次没有 DebugView/后台接收直接证据。看板读取公开统计 JSON，也不证明本 App 已成功上报。
