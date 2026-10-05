<div align="center">

# AC8 Override Loader

**适用于 ACE COMBAT 8 的通用 UE4SS IoStore 覆盖容器加载器**

版本 `0.2.0` · 游戏版本 `1.1.2.0` · Unreal Engine `5.4`

[简体中文](README.md) | [English](README_EN.md)

</div>

---

## 简介

AC8 Override Loader 会在游戏挂载官方 `pakchunk0-Windows.utoc` 后，临时允许加载未签名的自定义容器，并递归扫描 `AC8OverrideLoader\payloads` 下的所有 `.utoc` 文件。

Loader 不限制资源类型。有效的 UE5.4 覆盖容器可以包含数据表、蓝图、贴图、材质、模型、音频及其他 cooked 资源。

> [!IMPORTANT]
> 当前内部签名和 ABI 仅在 ACE COMBAT 8 `1.1.2.0` 上通过验证。游戏更新后，请等待兼容性确认后再使用。

## 安装

### 1. 准备环境

- 安装 **UE4SS 3.0.1 Beta #0**。
- 完全卸载或禁用旧的 `IoStoreLoaderMod`、AC8 PGM 专用 Loader，以及其他会挂载相同资源的加载器。

### 2. 安装 Loader

将本项目的压缩包解压到 `ACE COMBAT 8` 游戏根目录，并允许其中的 `Game` 目录合并。

随后在以下文件中添加或启用 Loader：

```text
Game\Binaries\Win64\ue4ss\Mods\mods.txt
```

```text
AC8OverrideLoader : 1
```

### 3. 放置模组容器

将每个模组的同名 `.utoc`、`.ucas` 和可选 `.pak` 文件放入独立子目录：

```text
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\
└── payloads\
    └── MyMod\
        ├── MyMod_P.utoc
        ├── MyMod_P.ucas
        └── MyMod_P.pak
```

| 文件 | 要求 |
| --- | --- |
| `.utoc` | 必须存在，Loader 以此文件发现容器 |
| `.ucas` | 必须存在，且必须与 `.utoc` 同名 |
| `.pak` | 可选；若成品集合包含此文件，请保持同名并放在同一目录 |

> [!NOTE]
> 只有单独 `.pak`、没有 `.utoc/.ucas` 的传统 Pak 模组不在此 Loader 的支持范围内。

## 加载顺序

Loader 按**不区分大小写的完整路径**排序，并从挂载序号 `1000` 开始依次挂载。需要明确覆盖顺序时，建议为子目录添加数字前缀：

```text
payloads\
├── 0010_BaseMod\...
└── 0020_OverrideMod\...
```

覆盖相同 `/Game/...` 路径的模组会产生冲突，较后挂载的模组通常具有更高优先级。

## 运行日志

日志文件位于：

```text
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\AC8OverrideLoader.log
```

每个成功挂载的容器都应显示：

```text
custom mount code=0
```

## 重要说明

- 本 Loader 绕过的是**未签名自定义 IoStore 容器的签名要求**，并不会解密游戏原始容器。
- 自定义容器应为正常、未加密的 UE5.4 IoStore 集合；提取原版加密资源仍需合法取得的 AES key。
- 仅限离线单人战役和开发测试。请勿用于多人模式、在线服务、排行榜或启用 EAC 的进程。
- Loader 不会修改游戏原始的 `pakchunk0-Windows.*` 文件。

## 卸载

1. 删除或移走所有依赖此 Loader 的 `payloads` 子目录。
2. 从 `mods.txt` 中删除或禁用 `AC8OverrideLoader`。
3. 删除整个 `AC8OverrideLoader` 文件夹。

## 相关工具

资源提取、修改、打包，以及 Legacy -> IoStore -> Legacy 回读验证，请使用独立的 `AC8OverrideToolkit`。如果已有可直接使用的成品容器，则不需要 Toolkit。

## 许可证与第三方组件

本项目采用 [MIT License](LICENSE)。原生 Loader 使用 MinHook，并基于 IoStoreLoaderMod 参考实现开发；详情请参阅 [第三方声明](THIRD_PARTY.md)。
