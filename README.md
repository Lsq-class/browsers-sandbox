# Chrome Sandbox Manager

> 多版本浏览器沙箱管理器 —— 在 macOS / Windows 上下载、管理历史版本 Chrome / Chromium 内核，并为每个客户环境创建完全隔离的调试沙箱。

一个面向前端开发者的桌面工具：当客户反馈"在他们的浏览器里有问题"时，用它快速复现客户所用内核版本下的兼容性问题，而不用污染自己日常使用的浏览器。

## ✨ 功能特性

### 🔖 内核版本库

- **Chrome for Testing（113+）**：内置官方完整版本索引（2500+ 版本），输入即搜，选中即装
- **Chromium 历史版本（96–112）**：按里程碑自动匹配该时期的真实快照构建（基于公开发布日历锚点 + 快照镜像日期对齐），下拉直接选择
- **Chromium revision 直装**：支持输入任意 revision；自动校验其在快照库中是否存在，不存在时**自动对齐到最近的可用构建**，不再出现 404
- **双源下载**：优先 npmmirror 国内镜像，失败自动回退官方 `storage.googleapis.com`，全程实时进度条 + 日志
- **零依赖**：内置 Rust 原生下载器，**无需安装 Node.js / npx**
- **本地导入**：直接把 `.zip` / `.app` 拖进窗口或通过文件选择器导入，自动识别 arm64 / x86_64 架构、修复 Gatekeeper 隔离属性，纳入统一管理
- **自定义下载目录**：内核库位置可改（支持迁移已有内核，跨卷自动回退复制）

### 🧪 客户环境沙箱

- 每个沙箱绑定一个具体内核版本，使用完全隔离的 `--user-data-dir`，互不干扰
- 一键注入调试开关：
  - `--disable-web-security`（禁用同源策略）
  - `--ignore-certificate-errors`（忽略证书错误）
  - `--auto-open-devtools-for-tabs`（自动打开 DevTools）
  - `--proxy-server`（代理）
  - 任意自定义启动参数（每行一条）
- 进程守护：外部关闭浏览器后状态灯实时熄灭；强制停止（Windows 走 `taskkill` 进程树终止，macOS 走 SIGTERM/SIGKILL）
- 一键清空缓存：恢复首次访问状态，用于复现"新用户"场景

### 🖥 界面

<img width="2510" height="1360" alt="Clipboard_Screenshot_1793154054" src="https://github.com/user-attachments/assets/230cf11d-e62e-4b6e-bb15-201157874f58" />
## 📖 常见问题

**Q：为什么 Chromium 旧版本不能直接输版本号？**
Chromium 快照库（`chromium-browser-snapshots`）按 commit **revision** 编号存储，不接受版本号。应用已内置「版本号 → revision」解析（113+）与里程碑匹配（96–112），正常情况下直接搜版本号即可。

**Q：下载的浏览器提示"已损坏"？**
这是 macOS Gatekeeper 的隔离属性导致（老内核尤其常见）。应用在每次安装/导入完成后会自动执行 `xattr -cr` 清除；若仍遇到，可手动执行：
`xattr -cr "/path/to/Chromium.app"`

**Q：强制 x86_64 是什么意思？**
在 Apple Silicon 上安装 Intel 构建以模拟 Rosetta 场景；主版本低于 105 的 Chromium 无 ARM 原生包，会自动回退 Intel 构建。

**Q：删除沙箱会删除浏览器内核吗？**
不会。沙箱只是配置 + 独立用户数据目录；内核在「内核版本库」中单独管理，被沙箱占用时禁止卸载。

## 🗺 Roadmap

- [ ] 签名与公证（macOS notarization / Windows 代码签名）
- [ ] 内核下载断点续传
- [ ] 沙箱配置导入导出（团队共享客户环境）
- [ ] CDP 远程调试端口支持

## License

暂未设置开源协议。
