# 证据索引与覆盖范围

编制日期：2026-11-11。本公开索引保留版本、相对源码文件和证据等级，避免把历史报告、代码核对和实测混为一谈。原始归档、私有 QA 路径和原件不随包发布。下文“本次复跑”指原 Skill 编制阶段，并非本次公开发布重新执行。

## 证据等级

- **代码核对**：读取当前归档实现，可说明机制存在，不代表所有动态场景通过。
- **历史验收核对**：读取原 QA/日志，必须保留当时平台、版本、失败与限制。
- **本次复跑**：仅下面列出的 5 组 iOS 纯逻辑；没有重新安装或操作任何 App。
- **建议**：基于已识别限制的下一步，不能列为既有修复。

## 原始资料定位

### E1：iOS 0.6.10 / Build 56

资料范围：对应版本的受检源码与内部验收记录；原件不随公开包提供。

- `源码/WYReader/ReaderFigureCovers.swift`：请求 identity/源站、正文 hash、顺序块、首图/无图、版本缓存与合并请求。
- `源码/WYReader/Library.swift`：215–268 行目录/发现刷新代次，309–323 行读位键，486–534 行目录封面/缓存，556–598 行离线搜索 revision，785–819 行离线包校验和配额。
- `源码/WYReader/StartupSequenceState.swift`：终态、generation、单调超时。
- `源码/WYReader/ProfileScrollGeometry.swift`、`MusicNoteRotationState.swift`：几何与旋转状态。
- `源码/WYReader/WYReaderApp.swift`：原生及网页连续返回手势；`InAppWebView.swift`：网页历史与容器关闭。
- `源码/WYReaderUITests/`：ReviewFlow、MusicMiniPlayer、EdgeReturnMatrix、WebEdgeReturn、ProfileExperience、StartupIntro、NavigationSync。

历史结果：15 个不同 UI 方法有有效最终通过记录；最初 final-ui-regression 运行存在 2 个失败，后续分别复测，不能把原运行改称全通过。覆盖安装、读回版本和偏好一致性有记录，启动因锁屏未完成。没有新增真机 UI、Duo 内屏、断网 UI 验收。

### E2：iOS 连续版本交接

资料范围：对应版本的受检源码与内部验收记录；原件不随公开包提供。


0.6.1 说明中官网接口待发布属于当时状态；后续归档存在 live navigation 验收，不能把早期“未上线”永远套用当前版本。当前上线情况仍应现场核查。

### E3：早期体验资料

iOS 0.3.4 至 0.5.3 的内部视觉验收与交接说明。多个早期目录主要是截图，足以定位界面演进，不能单靠截图证明修复根因、真机通过或完整性能收益。没有为每一个早期版本补造 changelog。

### E4：网站 Reader v1 契约和 Git 历史

网站源码工程的：

- `docs/content/native-reader-api.md`、`docs/releases/reader-step3-20261110.md`、`docs/verification/reader-step3-20261110.json`。
- `src/lib/native-reader-catalog.mjs`、`native-reader-bodies.mjs`、`native-reader-images.mjs`、`native-reader-navigation.mjs`。
- `reader_contract.py`、`reader_rich_contract.py` 及对应 tests。

私有源码记录核对（公开读者无法直接读取原仓库）：`5a59f90c` 共用目录、`17602655` 结构化正文/章节、`93fbf46f` Discover/独立离线包、`78d36975` 正文版本/完整性（#253）、`3742f542` 博客富文本（#255）、`489bd90a` 多尺寸图片、`f7b4f931` 导航接口校验。

这些是源码记录定位，提交存在不等于上线；网站当次测试数与 App 客户端测试数不合并。richText 的网站能力不证明四端已显示新格式。

### E5：Android 手机 0.4.1 / Build 12

资料范围：对应版本的受检源码与内部验收记录；原件不随公开包提供。

源码 `Android/app/src/main/java/com/yutengfei/reading/`：

- `ReaderChrome.kt:43–107` 输入过滤、显隐、视觉与点击区。
- `OfflineCache.kt:35–46,220–259` URL 范围与分段下载。
- `OfflineBooks.kt:39–95` staging、并发与发布版本一致性；91 行附近旧目标删除后 rename 是回滚局限。
- `PortableOffline.kt:151–165,194–204` 校验和 AtomicFile。
- `NativeLinks.kt:214–265` 浏览器范围及释放。
- `Android/app/build.gradle.kts`：统计构建开关；未读取或复制 Firebase 配置内容。

历史验收材料仅供维护者内部核对，不随公开包发布；下文保留已知结果与限制。

### E6：Android TV 0.1.4 / Build 5

资料范围：对应版本的受检源码与内部验收记录；原件不随公开包提供。

源码 `AndroidTV项目/app/src/main/java/com/yutengfei/reading/tv/MainActivity.kt:798–917`：阅读控件、原始触摸、焦点、回顶与图像边界。

历史验收材料仅供维护者内部核对，不随公开包发布；下文保留已知结果与限制。

### E7：macOS 1.1.1 / Build 112

资料范围：对应版本的受检源码与内部验收记录；原件不随公开包提供。

- `macOS项目/Sources/ArticleView.swift:78–132,182–309,374–391`：WebKit 隔离、pageKey、资源路由、输入显隐、token 绑定。
- `macOS项目/Sources/Library.swift:620–644`：数据库 staging、验证和提交前版本检查。

边界：本地预览未完成 Developer ID/公证；半身页面窗口复核待完成；正文 WebKit 焦点下 Escape 退出有已记录缺口。没有测得量化性能提升。

## 编制时的独立复跑摘要

编制方法包时从 E1 实际源文件编译，而不是复制算法。5 个 runner 均 exit 0，本次公开发布没有重新运行：

| 检查 | 场景 | 断言 |
| --- | ---: | ---: |
| 封面解析/缓存/目录映射 | 16 | 39 |
| 发现刷新/缓存乱序 | 不单独统计场景 | 8 |
| 滚动几何 | 19 | 448 |
| 启动状态 | 13 | 343 |
| 音乐旋转状态 | 9 | 426 |

编制时所检源码及 harness 的 hash、状态摘要见[验证摘要](validation.json)。仅包含相对源码路径和校验值，不含 App 源码。旧 runner 内版本/机器标签未作为本次环境证据。没有启动模拟器、真机、播放器或 UI 测试；不宣称四端全量通过。

## 仍未覆盖

最初立项与全部早期提交、iPad 独立验收、实体 Android TV、物理折叠铰链、全端富文本接入、真实读屏焦点、旧系统音频共存、帧率/耗电基准、商店审核、GA4 后台入库，均没有在本任务取得足够的完整验证证据。
