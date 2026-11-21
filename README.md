# marble GKI 5.10 内核云端编译

GitHub Actions 一键编译带 **Droidspaces 容器支持 + KernelSU** 的 GKI 5.10 内核刷机包（AnyKernel3）。

## 用法

1. 仓库 **Actions** 页 → 左侧 **构建 GKI 5.10 内核 (Droidspaces + KernelSU)** → **Run workflow**
2. 参数：
   - `droidspaces_slot`：SYSVIPC kABI 补丁槽位，默认 `678`（官方推荐）；刷入后 bootloop 则换 `123` 或 `345` 重试；选 `off` 则跳过全部 Droidspaces 补丁与容器配置，仅构建纯净 GKI + KernelSU
   - `kernel_branch`：GKI 内核分支，默认 `android12-5.10-lts`（持续收安全补丁），也可选固定季度分支
   - `ksu_channel`：KernelSU 版本渠道，默认 `latest`（最新正式版 tag，更稳定且易于匹配 Manager APK），可选 `main`（开发主线）
   - `create_release`：构建完成后自动发布 GitHub Release（默认开启，产物长期保留，附带内核版本、KernelSU 版本码与 SHA256）
3. 等待约 15-40 分钟（命中 ccache 后二次构建更快）
4. 运行完成后：
   - 仓库 **Releases** 页下载 `marble-gki-5.10-*-AnyKernel3.zip`（推荐，长期保留）
   - 或 workflow 页面 **Summary** 查看完整版本/SHA256 摘要并下载同名 artifact（保留 14 天）

## 产物说明

- **AnyKernel3 zip**：保留设备原有 ramdisk/bootconfig 的刷机包（推荐），在自定义 recovery（如 OFRP/TWRP）中刷入，或 `fastboot boot <recovery.img>` 临时引导后刷入
- 内核：AOSP GKI `android12-5.10` 主线（已去 `-dirty` 后缀）+ Droidspaces 官方 kABI 补丁与全套 kABI-safe 容器/网络配置（含 NAT66、Macvlan、UFW/Fail2ban、tmpfs xattr/ACL）+ 官方 KernelSU（内置 GKI 模式）
- 刷入后需安装对应版本的 KernelSU Manager 获取 root；Droidspaces 需求检查应全部通过

## 为什么基于 AOSP GKI 而非小米官方 `marble-s-oss`

- 小米官方 [`MiCode/Xiaomi_Kernel_OpenSource`](https://github.com/MiCode/Xiaomi_Kernel_OpenSource) 的 `marble-s-oss` 分支停更于 2023 年 6 月（Linux `5.10.117`），且缺失显示、相机、触控、data-kernel 等关键驱动仓库（半开源），无法完整编译原厂配套模块。
- 上游 AOSP `android12-5.10-lts` 持续维护至 `5.10.274+` 并内置 `android/abi_gki_aarch64_xiaomi` KMI 符号白名单，直接替换 GKI `Image` 即可在保持原厂 `vendor_boot`/`vendor_dlkm` 兼容的前提下获得最新安全补丁。

## 原理与来源

- 构建管线参考社区成熟实现（GKI KernelSU SUSFS 项目）的已验证机制
- 补丁与配置来源：[Droidspaces 官方仓库](https://github.com/ravindu644/Droidspaces-OSS) `Documentation/Kernel-Configuration.md` 及 `Documentation/resources/kernel-patches/GKI/`
- KernelSU 集成：[官方 setup.sh](https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh)
- AnyKernel3：WildKernels/AnyKernel3 `gki-2.0` 分支

## 注意事项

- 刷机有风险，操作前备份原厂 boot 镜像
- GKI 内核依赖设备 vendor 分区的原厂模块，KMI 不匹配可能导致相机/触控异常——刷入后立即验证核心功能
- 本仓库仅包含构建工作流，不含任何设备私有信息
