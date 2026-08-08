# Blender-on-Android CI（阶段0：复现 v0.0.3 构建）

## 这是什么

把 epai 的 Blender Android 移植（基于 Blender 3.6-release）在 GitHub Actions 上自动化构建：
- 公共仓库 → Actions 免费无限分钟
- 两个 job 接力：先 NDK 交叉编译全部 Blender 静态库（bf_*.a），再链接打包 APK
- 用 **qemu-user + binfmt** 在 x86_64 runner 上直接运行 arm64 的 DNA/RNA 生成器，
  绕开作者在 Windows 上"手机跑生成器"的 hack，官方 CMake 流程全自动

## 怎么用

1. fork 本仓库（公共仓库免费额度生效）
2. Actions 页 → 手动触发 `Blender Android Build`（或 push main 自动触发）
3. 跑完后在 Artifacts 下载：
   - `blender-on-android-ci` → 签名 APK（debug key，可安装）
   - `blender-libs` → 全部 libbf_*.a（后续升级 4.2 可复用）

## 已知风险（按概率排序）

| 风险 | 应对 |
|---|---|
| Job1 编译超 6h（2核 runner） | 拆两个 job 分模块组并行，或接受重跑 |
| 14GB 磁盘不够（NDK 3.5G + 源码 1.5G + 产物） | 浅克隆 + 缓存 NDK + 清理中间 .o |
| 作者 CMakeLists 有未发现的 Windows 依赖 | 看日志迭代修 |
| qemu 跑生成器失败（依赖特殊 syscall） | 退化为 host 原生编译生成器 |
| jcenter 依赖残留 | 已 sed 替换 mavenCentral |

## 后续路线

- 阶段0 ✅ 复现 v0.0.3（本 workflow）
- 阶段1：工具链升级（NDK 25 / SDK 35 / 16KB 对齐）
- 阶段2：Blender 内核 3.6 → 4.2 LTS（同步 fork + 适配 GHOST/渲染）
- 阶段3：真机回归 → 发布

## 仓库来源

- APP: https://github.com/dshawshank/APP-android_arm64 (fork: KzxTang/APP-android_arm64)
- Blender 源码: https://projects.blender.org/epai/Blender-android_arm64 (blender-v3.6-release)
- 预编译三方库: https://projects.blender.org/epai/lib-android_arm64
- 工具: https://projects.blender.org/epai/Utilities-android_arm64

GPL-2.0
