# APP 策划与交付 Skill

把 W.Y. 阅读器真实开发中修过的问题、优化方式及验收边界，整理成可以继续使用的工程方法。覆盖 **iOS、Android 手机、Android TV、macOS，以及共用 Reader v1 内容接口**。

技术名称保持 `wy-app-development`，以 W.Y. 经验为基础，负责 App 侧策划、实现、交互、缓存、跨平台验收与交付，包含客户端适配网站内容的经验。它与网站侧 `website-studio-delivery` 分开维护，后者负责官网内容、接口与网站发布。

这不是 App 源码发布，也不是凭印象补写的开发流水账。当前可核实资料以 2026 年 11 月 8–11 日的源码、版本交接、测试记录和网站 Git 历史为主；更早的完整需求与每次提交未全部保留在所检资料中。

## 直接阅读

- [Skill 主入口](wy-app-development/SKILL.md)：如何接手、诊断、修改、验证和交付。
- [真实修复与优化案例](wy-app-development/references/lessons.md)：问题 → 原因 → 做法 → 防回归。
- [内容契约与离线更新](wy-app-development/references/content-contract.md)。
- [iOS 手势、滚动、动效与音频](wy-app-development/references/ios-interaction.md)。
- [Android、TV 与 macOS 适配](wy-app-development/references/platforms.md)。
- [验证、安装与归档](wy-app-development/references/verification-release.md)。
- [证据索引与覆盖缺口](wy-app-development/references/evidence.md)。

## 最值得复用的经验

1. 稳定内容 ID、正文版本和界面语言分开管理，不能用换语言或换封面顺带重建读位与收藏。
2. 在线更新先校验，再原子提交；旧请求不仅不能刷新界面，也不能污染磁盘缓存。
3. 文章没有封面不是随便找图：区分明确封面、当前正文首图、已确认无图和网络失败。
4. 返回手势要取消同一次触摸的点击，避免返回后又把入口点开成空白页。
5. 自动收起工具栏要看真实输入，不把布局变化、跳转或惯性误当用户滚动。
6. 开场、音乐、朗读用可测试状态机，终止状态不能被迟到回调复活。
7. Android TV 断 Wi‑Fi 不等于断网；焦点也不能只靠触屏测试替代。
8. 构建通过、模拟器通过、覆盖安装成功、真机启动、服务器上线是五种不同证据。

## 使用

将仓库中的 `wy-app-development/` 文件夹放入需要使用它的 Codex skills 目录，或在项目中明确引用该文件夹。公开包保留完整 Skill 文件夹；安装前比较已有版本并保留自己的修改，不会自动安装或更改现有客户端。

示例：

> 使用 $wy-app-development，修复新文章没有封面的问题。先确认源码与安装版本，检查内容 revision、缓存和离线回退，保留已有收藏与读位，并按实际完成范围报告测试结果。

> 使用 $wy-app-development，把手机阅读工具栏收起行为适配到 Android TV。沿用内容契约，独立处理遥控器焦点、真正断网和返回路径，不照搬触屏验收。

## 编制时核验与本次公开审查

2026-11-11 从 iOS 0.6.10 / Build 56 归档源码重新编译并执行 5 组现有纯逻辑检查：封面 39 断言、刷新 8 断言、滚动几何 448 断言、开场状态 343 断言、音乐状态 426 断言，均通过。详见 [验证摘要](wy-app-development/references/validation.json)。这是原 Skill 编制时的记录，本次公开发布没有重新运行 App 测试。

这些检查不等于重新完成 UI、真机、耗电、音频共存或四端全量测试。历史验收与本次复跑在证据索引中分开列出。

仓库只包含脱敏工程经验、模板与核验摘要，不含 App 源码、安装包、证书、密钥、设备标识、用户偏好备份或原始操作日志。本仓库是经审查的独立公开方法快照，不包含原私有仓库的 Git 历史，也没有为已有代码或素材新增或推定开源许可。

## 公开范围

公开入口为 [wy-app-development/SKILL.md](wy-app-development/SKILL.md)，安装时保留该完整目录。必要参考、空白模板与脱敏验证摘要随包提供。归档别名与内部 QA 路径已移除；源码相对文件名及版本仅用于解释经验来源，原 App 源码、私有日志和设备数据不在本包。源码为中文，官网另有七语介绍。
