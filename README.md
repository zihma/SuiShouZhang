# 随手账

一个中文、离线、本机保存的个人记账应用。安卓安装版直接运行，不需要打开 Chrome，不需要账号或同步服务。

## 安装与使用

[下载安卓 APK v1.0.0](Suishou-Ledger-v1.0.0.apk?raw=true)。支持 Android 8.0（API 26）及以上，界面使用手机系统的 Android System WebView。

将 APK 下载或传到手机，点击安装。如手机询问是否允许当前文件管理器安装应用，请按自己的意愿选择允许。

- 记录收入与支出，填写分类、日期、备注。
- 编辑、删除历史记录，按月份查看明细和结余。
- 查看支出分类统计。
- 右上角“备份与设置”中可导出全部月份的 CSV（Excel 可打开）。
- 导出完整 JSON 备份，用于换手机或恢复；导入采用合并方式，相同记录编号保留当前版本。

**请定期保存完整备份。卸载应用或清除应用数据，会删除本机账本。CSV 用于查看，JSON 用于恢复。**

网页版本的 JSON 备份也可以导入安卓版本。

## 数据与权限

安卓应用没有声明联网权限，不包含广告、统计上报、账号或云同步。账目通过 Android `AtomicFile` 存放在应用私有目录。文件导出、导入使用系统文档选择器，不需要申请整个手机存储空间的访问权限。自动系统备份已关闭。

界面和脚本都随 APK 打包。应用使用系统 WebView 显示内置界面；网络访问、外部页面跳转和任意本地文件访问未开放。备注在显示前进行 HTML 转义；CSV 备注对公式开头进行处理。

## 验证状态与已知问题

- APK 编译、资源打包、ZIP 完整性、对齐、v2/v3 签名验证已通过。
- 自动检查覆盖本机保存接口、金额精度、收入支出汇总、编辑、保存失败、备份导入合并、重复记录跳过、非法备份拒绝和 CSV 导出。
- 使用者在 Redmi K70 Ultra 上报告：记录两笔账，退出后重新打开，记录正常保留。这是基础实机反馈，不代表完整兼容性测试。
- 尚未完成系统文件选择器导出/导入的全流程实机测试及长期使用测试。
- v1.0.0 已知边界问题：上一份导出尚未完成时快速再次导出，可能使暂存文件冲突；请等待“已导出”提示再进行下一次操作。
- 删除确认窗口打开时，手机返回键可能先关闭下层编辑窗口；当前可使用确认窗口的“取消”按钮。
- 较旧的系统 WebView 可能不支持界面使用的现代 JavaScript/CSS。若出现白屏，请检查系统 WebView 更新。

## 完整源码

[下载完整源码与构建脚本](Suishou-Ledger-GitHub.zip?raw=true)。解压后进入 suishou-ledger 文件夹，可查看和修改下面的项目目录。以下开发命令都在解压后的项目目录内执行。

## 源码结构

```text
android/            AndroidManifest.xml、Java 主界面、资源、内置网页
web/                原始浏览器/PWA 版本
scripts/build.py    不依赖 Gradle 的安卓打包脚本
tests/              记账和安卓桥接逻辑检查
releases/           已生成的签名 APK 与 SHA-256
```

## 本地开发与检查

准备 Python 3、Node.js、JDK 17 和 Android SDK。设置 `JAVA_HOME`、`ANDROID_HOME`（或 `ANDROID_SDK_ROOT`）。SDK 需要至少一个 API 29 或更高的平台以及 Build Tools 29.0.3 或更高版本。

```sh
node tests/android-bridge.cjs
python scripts/build.py
```

默认生成 `build/Suishou-Ledger-debug.apk`，使用本机新生成、被 Git 忽略的开发签名。这与仓库中的安装包签名不同，不能直接覆盖安装仓库里的 v1.0.0。

若持有正式签名材料，可设置：

```text
SUISHOU_KEYSTORE=正式签名文件的绝对路径
SUISHOU_PASSWORD_FILE=只包含签名密码的一行文本文件的绝对路径
SUISHOU_KEY_ALIAS=suishou
```

然后再次运行 `python scripts/build.py`，生成 `build/Suishou-Ledger-release.apk`。正式签名私钥与密码不在本仓库；后续覆盖安装更新必须保留相同包名、使用同一签名，并递增版本号。更换签名可能需要卸载旧应用，**先导出完整备份**。

浏览器版本可在本地运行：

```sh
python -m http.server 8765 --directory web
```

打开 `http://localhost:8765`。浏览器版与安卓应用分别保存自己的数据，可通过 JSON 备份迁移。

