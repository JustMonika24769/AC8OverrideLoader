# AC8 Override Loader 0.2.0

面向 ACE COMBAT 8 `1.1.2.0` 的通用 UE4SS IoStore 覆盖容器加载器。

它在游戏挂载官方 `pakchunk0-Windows.utoc` 后，临时允许未签名的自定义容器，再递归扫描
`AC8OverrideLoader\payloads` 下的所有 `.utoc`。Loader 不检查资源类型；有效的 UE5.4 覆盖容器可以包含数据表、蓝图、贴图、材质、模型、音频或其他
cooked 资源。

## 安装

1. 安装 UE4SS 3.0.1 Beta #0。
2. 完全卸载或禁用旧的 `IoStoreLoaderMod`、AC8 PGM 专用 Loader，以及其他会挂载同一资源的加载器。
3. 将本压缩包解压到 `ACE COMBAT 8` 游戏根目录，使 `Game` 目录合并。
4. 在 `Game\Binaries\Win64\ue4ss\Mods\mods.txt` 中添加或启用：

```text
AC8OverrideLoader : 1
```

5. 将每个模组的同名 `.utoc/.ucas/.pak` 放在独立子目录，例如：

```text
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\payloads\MyMod\MyMod_P.utoc
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\payloads\MyMod\MyMod_P.ucas
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\payloads\MyMod\MyMod_P.pak
```

`.utoc` 和同名 `.ucas` 必须存在；成品集合带有 `.pak` 时保持同名并放在同一目录。
只有单独 `.pak`、没有 `.utoc/.ucas` 的传统 pak 模组不属于此 Loader 的支持范围。

## 加载顺序

Loader 按不区分大小写的完整路径排序，从 order `1000` 开始挂载。需要明确顺序时，建议使用：

```text
payloads\0010_BaseMod\...
payloads\0020_OverrideMod\...
```

覆盖相同 `/Game/...` 路径的模组会冲突，较后挂载者通常取得优先权。

## 重要说明

- 这里绕过的是未签名自定义 IoStore 容器的签名要求，不是解密原版游戏容器。
- 自定义容器应为正常、未加密的 UE5.4 IoStore 集合；提取原版加密资源仍需合法取得的 AES key。
- 仅限离线单人战役和开发测试。不要用于多人、在线服务、排行榜或启用 EAC 的进程。
- Loader 内部签名和 ABI 仅验证于游戏版本 `1.1.2.0`，游戏更新后必须重新验证。
- Loader 不修改游戏原始 `pakchunk0-Windows.*`。

日志位于：

```text
Game\Binaries\Win64\ue4ss\Mods\AC8OverrideLoader\AC8OverrideLoader.log
```

每个成功容器应显示 `custom mount code=0`。

## 卸载

先删除或移走依赖此 Loader 的 `payloads` 子目录，再从 `mods.txt` 删除或禁用
`AC8OverrideLoader`，最后删除整个 `AC8OverrideLoader` 文件夹。

资源提取、修改、打包和 Legacy -> IoStore -> Legacy 回读验证请使用独立的
`AC8OverrideToolkit`。已有成品容器时不需要 Toolkit。
