# 音频裁剪（Beat Data Generator 插件）

在时间轴上框选一段音频，导出裁剪副本（WAV），或**就地裁剪并替换当前音频**——踩点按拍号
自动对齐，范围外的踩点清除，适合把长曲目裁成一段后继续打点。

插件 id：`dev.bdg.audiotrim`

## 安装

把本仓库整个文件夹放进插件扫描目录，重启编辑器：

- 用户目录：`<userData>/plugins/`（编辑器设置页可一键打开）；
- 开发模式：宿主工程根目录的 `plugins/`。

## 使用

- 时间轴：拖动选区两端的手柄即可调整裁剪范围，范围外区域变暗；
- 工程栏快捷行：点「裁剪选区」按钮可一键显示 / 隐藏选区；
- 「插件」菜单 →「音频裁剪」打开面板，或按快捷键 **Alt+T**。

面板内可：

- 用**拍号**或 `mm:ss.mmm` 设置起点 / 终点，或点 `⌖` 用当前播放头取值；
- 设置**对齐精度**（默认「整数拍（1 拍）」，另可选 1/2、1/4、1/8 拍或不对齐）；
- 查看选区时长与将被裁掉的踩点数量；
- **导出裁剪副本 (WAV)** 或 **裁剪并替换当前音频**。

## 行为说明

- 拖动选区时按拍网格对齐（默认整数拍），并吸附到 8px 内的已有踩点，便于精确落在拍点上。
- **裁剪并替换**：新选区成为整段音频（从其起点计为 0），并令 `offsetMs -= startMs`，
  使所有**拍号对应的音频内容保持不变**，踩点无需重排即锁在原音频片段上；范围外的踩点
  与循环子点会被清除，BPM 点保持不变。以上工程改动合并为**一次撤销**。
- ⚠️ **音频替换不进入撤销栈**：撤销只能恢复偏移与踩点，音频本身需要手动回退。
- 仅输出 **WAV**（16-bit PCM，无损）。

## 开发

```
bdg_plugin_audiotrim/
├─ manifest.json     # 元信息（必需）
├─ renderer.js       # 时间轴叠加层 + 面板 + 工程栏按钮（UI 与逻辑）
├─ plugin-api.d.ts   # 宿主 API 类型声明
└─ README.md
```

`renderer.js` 顶部引用 `plugin-api.d.ts` 可获得编辑器类型提示。接口用法见
宿主仓库的 [`docs/plugin-api.md`](https://github.com/BUGJI/beat_data_generator/blob/main/docs/plugin-api.md)：
本插件用到 `api.audio`（`getBuffer` / `encodeWav` / `replaceFromBytes`）、
`api.timeline`、`api.ui.registerTimelineOverlay` / `registerPanel` / `registerBarButton` /
`registerShortcut`、`api.system.writeBytes` 与 `api.storage`。

## 许可

- 插件版权归 BUGJI，采用与宿主一致的 **GNU GPL v3**。
- 宿主编辑器 **Beat Data Generator** 以 **GNU GPL v3** 发布（作者 BUGJI）。
  插件由宿主加载器装载运行，分发时建议注明与宿主的关联。
- 官方插件模板见 <https://github.com/beat-data-generator/bdg_plugin_template>。
