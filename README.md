# marble GKI 5.10 内核云端编译

GitHub Actions 一键编译带 **Droidspaces 容器支持 + KernelSU** 的 GKI 5.10 内核刷机包（AnyKernel3）。

## 用法

1. 仓库 **Actions** 页 → 左侧 **构建 GKI 5.10 内核 (Droidspaces + KernelSU)** → **Run workflow**
2. 参数：
   - `droidspaces_slot`：SYSVIPC kABI 补丁槽位，默认 `678`（官方推荐）；刷入后 bootloop 则换 `123` 或 `345` 重试
   - `kernel_branch`：GKI 内核分支，默认 `android12-5.10-lts`（持续收安全补丁）
   - `create_release`：构建完成后自动发布 GitHub Release（默认开启，产物长期保留）
3. 等待约 30-60 分钟（含源码同步与编译）
4. 运行完成后：
   - 仓库 **Releases** 页下载 `marble-gki-5.10-*-AnyKernel3.zip`（推荐，长期保留）
   - 或 workflow 页面 **Summary** 底部下载同名 artifact（仅保留 14 天）

## 产物说明

- **AnyKernel3 zip**：保留设备原有 ramdisk/bootconfig 的刷机包（推荐），在自定义 recovery（如 OFRP/TWRP）中刷入，或 `fastboot boot <recovery.img>` 临时引导后刷入
- 内核：AOSP GKI `android12-5.10` 主线 + Droidspaces 官方 kABI 补丁 + 官方 KernelSU（内置 GKI 模式）
- 刷入后需安装 KernelSU Manager 获取 root；Droidspaces 需求检查应全部通过

## 原理与来源

- 构建管线参考社区成熟实现（GKI KernelSU SUSFS 项目）的已验证机制
- 补丁来源：[Droidspaces 官方仓库](https://github.com/ravindu644/Droidspaces-OSS) `Documentation/resources/kernel-patches/GKI/`
- KernelSU 集成：[官方 setup.sh](https://raw.githubusercontent.com/tiann/KernelSU/main/kernel/setup.sh)
- AnyKernel3：WildKernels/AnyKernel3 `gki-2.0` 分支

## 注意事项

- 刷机有风险，操作前备份原厂 boot 镜像
- GKI 内核依赖设备 vendor 分区的原厂模块，KMI 不匹配可能导致相机/触控异常——刷入后立即验证核心功能
- 本仓库仅包含构建工作流，不含任何设备私有信息
