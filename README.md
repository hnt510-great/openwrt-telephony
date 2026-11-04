# OpenWrt 语音通信 feed

**简体中文** | [English](README.en.md)

本仓库是 [hnt510-great/openwrt](https://github.com/hnt510-great/openwrt) 定制主树使用的私有源码 feed。
项目面向 x86_64/glibc、APK、原生 systemd 和 GNOME 桌面；软件选择以主树
保存的完整配置为准。

| 项目 | 值 |
| --- | --- |
| Feed 名称 | `telephony` |
| 本仓库分支 | `master` |
| 原始上游 | [https://github.com/openwrt/telephony](https://github.com/openwrt/telephony)（`master`） |

本 feed 基于 OpenWrt telephony，保存语音通信软件、协议库及内核模块配方，
并保留本项目的本地提交和上游合并历史。

| 目录 | 主要内容 |
| --- | --- |
| `libs/` | PJPROJECT、libsrtp、sofia-sip、spandsp、DAHDI/OSLEC 等库或底层组件 |
| `net/` | Asterisk、FreeSWITCH、Kamailio、baresip、coturn、rtpengine 及诊断工具 |

## 在本项目中的职责

本 feed 与主树的 glibc/APK、原生 systemd 和 mainline 内核配置一起维护。
历史中包含 rtpengine 新内核构建适配等修改；这类修改应保留在本 feed，
不能仅从官方 upstream 重建并假定已包含本地集成。

内核相关配方必须针对当前主树配置和对应的内核生成批次构建。新的固件不能
搭配旧 ABI 的模块或开发头文件；完整构建应使用主树的内核批次保留入口。

主树 docs 尚未给出 SIP 账号、PBX、RTP 通话或物理电话设备的专属验收流程。
完整镜像的路由烟雾测试不代表这些业务已经验证，服务选项与启用状态以当前
`.config` 和设备配置为准。

## 获取与构建

先在构建机登录有权访问全部私有仓库的 GitHub 账号，再获取完整主树：

```sh
gh auth login --hostname github.com --git-protocol https --web
gh auth setup-git
git clone https://github.com/hnt510-great/openwrt.git
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
```

主树 `feeds.conf.default` 已包含：

```text
src-git telephony https://github.com/hnt510-great/openwrt-telephony.git;master
```

在现有主树中只更新本 feed，可执行：

```sh
./scripts/feeds update telephony
./scripts/feeds install -a -p telephony
```

先在主树配置中选择所需包并运行 `make defconfig`。单包构建示例：

```sh
make package/feeds/telephony/rtpengine/compile V=s
```

以上命令均从主树根目录运行。保留已有 `.config`；新 checkout 的配置恢复
步骤见 [本地构建指南](https://github.com/hnt510-great/openwrt/blob/main/docs/local-build.md)。`install` 安装的是构建配方链接，
不会在设备上安装软件。完整 `update -a` 还会检查并可能修改主树的 custom 配方。

## 维护与验证

`origin` 指向本私有仓库。新 clone 需要合并原始上游时，先添加一次 `upstream`：

```sh
git -C feeds/telephony remote add upstream https://github.com/openwrt/telephony.git
git -C feeds/telephony fetch upstream
git -C feeds/telephony merge upstream/master
# 审阅、解决冲突并完成相关验证后推送
git -C feeds/telephony push origin master
```

已有 `upstream` 时跳过添加命令。`scripts/feeds update` 拉取本私有分支，
不会自动合并原始上游。本地集成修改与主树的提交分别管理，保留现有提交、
stash 和未提交文件。主树的 `patches/feeds/` 和功能清单是历史备份，
不要向已经包含修改的 feed 重复应用补丁。

涉及已发布 APK 内容的修改必须产生新包版本，不能覆盖同一包身份。源码
检查、包构建、VM 与物理设备验证应分别记录；本文汇总已有设计和验证记录，
不新增构建或设备验证结论。私有源码托管说明见
[GitHub 源码托管](https://github.com/hnt510-great/openwrt/blob/main/docs/github-source-hosting.md)。

## 设计与验证文档

- [主线内核维护](https://github.com/hnt510-great/openwrt/blob/main/docs/mainline-kernel.md)
- [内核包与升级机制](https://github.com/hnt510-great/openwrt/blob/main/docs/kernel-upgrade.md)
- [原生服务监督](https://github.com/hnt510-great/openwrt/blob/main/docs/systemd-native-supervision.md)
- [源码和配置重建](https://github.com/hnt510-great/openwrt/blob/main/docs/local-build.md)

## 许可与上游

保留上游版权和许可。各软件包源码的许可按其配方 `PKG_LICENSE` 及源码声明执行。
