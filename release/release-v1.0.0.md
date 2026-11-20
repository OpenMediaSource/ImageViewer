# ImageViewer v1.0.0 发布

ImageViewer v1.0.0 正式发布。这是一个完全离线、绿色免安装的跨平台图像查看与处理工具，提供 Windows 与 Linux 的可执行发行包。

- 版本：`v1.0.0`
- 发布日期：2026-11-19
- 对应源码标签：`v1.0.0`
- 支持平台：Windows 10/11（x64 / ARM64）、Linux（x64 / ARM64）
- 授权方式：基础版免费；专业版通过离线激活码解锁
- 许可证：MIT

## 下载

请根据系统选择对应发行包：

| 平台 | 发行包 |
|------|--------|
| Windows x64 | `ImageViewer-win64-portable.zip` |
| Windows ARM64 | `ImageViewer-win-arm64-portable.zip` |
| Linux x64 | `ImageViewer-linux-x64-portable.tar.gz` |
| Linux ARM64 | `ImageViewer-linux-arm64-portable.tar.gz` |

发行包为绿色版，无需安装。Windows 解压后运行 `ImageViewApp.exe`；Linux 解压后运行 `./ImageViewApp`。程序不写注册表、不修改系统设置，删除程序目录即可卸载。

## 版本亮点

### 十种工作模式

- 图像查看（Viewer）
- 双文件对比（Compare）
- 批量格式转换（Batch Format Trans）
- 批量缩放（Batch Scale）
- 批量重命名（Batch Rename）
- 批量水印（Batch Watermark，专业版）
- Upscale to 4K（专业版）
- Restore 修复增强（专业版）
- SDR to HDR（专业版）
- HDR to SDR（专业版）

### 批量重命名

- 名称模板支持 `{name}` 和 `{index}`
- 可设置起始序号、补零宽度
- 支持仅处理图片或处理全部文件
- 冲突时可选择跳过或自动编号，已有文件不会被覆盖
- 提供重命名预览，执行前可确认结果

### 批量水印（专业版）

- 通过 GPU 为整个目录中的普通图片批量添加文字水印
- 可调整水印文本、字体像素高度、颜色、透明度、位置、高度比例和旋转角度
- 支持平铺水印及平铺间距
- 输出格式可选择“与源文件相同”或指定可写格式
- 输出文件命名为 `<原名>_watermarked.<扩展名>`
- 需要 OpenGL 3.3 及以上，暂不支持 CPU 回退

### 图像查看与检查

- 最多同时打开 32 个图像窗口，支持多选打开、拖入文件和拖入目录
- 异步加载，加载期间界面保持响应；保留最近 10 个文件
- 以鼠标位置为锚点缩放，左键拖动平移，双击复位
- 像素探针：`Shift` + 左键查看像素坐标和通道值
- 十六进制像素面板：查看解码后的像素缓冲区
- 支持 RGB、YUV、GRAY、BAYER 等原始像素格式，手动填写宽高和格式后查看
- 深色、浅色、经典三种主题；可调字号；中英文界面

### 双文件对比

- 左右并排显示两个文件的原始字节十六进制内容
- 自动计算 PSNR / MSE
- 尺寸不一致或无法解码时显示原因
- 支持同步滚动和多标签对比页

### 单图处理

- 格式转换：可选择可写格式，实时预览最终输出路径，拒绝覆盖源文件
- 去噪：Average、Median、Gaussian 三种算法
- 缩放：`2x`、`4x`、`1/2x`、`1/4x` 四档倍率
- 基础版使用 CPU；专业版可按条件使用 GPU 加速

### Anime4K 图像加速（专业版）

- **Upscale to 4K**：使用 Anime4K 预设将单张图等比放大到不超过 3840 × 2160
- **Restore**：同尺寸修复增强，提供多种 CNN / GAN 预设
- 仅处理带文件头的普通图片，不支持原始像素格式输入
- 需要 OpenGL 3.3 及以上；Restore 没有 CPU 回退

### HDR 工作流

- 可直接查看 Radiance `.hdr` 以及 10/12-bit PQ/HLG 的 HEIF/HEIC/AVIF 图片，并在 SDR 显示器上自动色调映射
- **SDR to HDR**：输出 10-bit HDR10（PQ10）AVIF
- **HDR to SDR**：支持 BT.2390、Reinhard、None 色调映射算子
- HDR 转换功能属于专业版，需要 OpenGL 3.3 及以上

### 图像格式

支持 PNG、JPEG、BMP、GIF、TIFF、TGA、PSD、WebP、PPM/PGM/PBM、XBM/XPM、QOI、ICO/CUR、ICNS、DDS、KTX、SVG、Radiance HDR，以及 HEIF/AVIF 和 Camera RAW。

同时支持 50 余种原始像素格式，包括 RGB、YUV、GRAY 和 BAYER 系列。实际可读/可写能力取决于发行包包含的编解码器：

- HEIC 仅支持读取
- PSD 仅解码合成图，不保留图层
- Camera RAW 仅支持读取

### 命令行工具

发行包内提供四个命令行工具：

| 工具 | 用途 |
|------|------|
| `formattrans` | 原始像素格式互转，可输出预览图 |
| `scale` | 原始像素图像缩放 |
| `rotate` | 原始像素图像旋转 |
| `imgproc` | 普通图片文件的格式转换、旋转、缩放 |

`formattrans`、`scale`、`rotate` 支持 `--hw`；基础版会提示并回退 CPU，专业版使用 GPU。`imgproc` 始终使用 CPU。

## 基础版与专业版

ImageViewer 使用同一个程序包，通过离线激活码在运行时切换版本，无需联网、无需重启。

| 功能 | 基础版 | 专业版 |
|------|:------:|:------:|
| 图像查看、对比 | 支持 | 支持 |
| 单图格式转换 / 去噪 / 缩放 | 支持（CPU） | 支持（CPU + GPU） |
| 批量格式转换 | 支持（CPU） | 支持（CPU + GPU 可选） |
| 批量缩放 | 支持（CPU） | 支持（CPU） |
| 批量重命名 | 支持 | 支持 |
| 批量水印 | 不支持 | 支持 |
| Upscale to 4K / Restore | 不支持 | 支持 |
| SDR to HDR / HDR to SDR | 不支持 | 支持 |
| 命令行 `--hw` | 回退 CPU 并提示 | 生效 |

激活入口：`Help > License / Activate Pro`。许可证保存在程序目录的 `license.lic`，删除后回到基础版。

## 系统要求

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10/11（x64 / ARM64）；Linux（x64 / ARM64） |
| 网络 | 无需网络，程序完全离线运行 |
| GPU（可选） | 显卡与驱动支持 OpenGL 3.3 及以上 |

基础版不需要 GPU。专业版的批量水印、Anime4K 图像加速和 HDR 转换需要可用的 OpenGL 3.3 环境。

## 已知限制

- 水印文字仅支持 printable ASCII，最多 128 个字符；小写字母会以大写形式渲染
- 批量水印需要专业版和 OpenGL 3.3 及以上，没有 CPU 回退
- Restore、SDR to HDR、HDR to SDR 需要 OpenGL 3.3 及以上；Restore 和 HDR 转换没有 CPU 回退
- 原始像素文件没有文件头，必须手动提供正确的宽、高和格式
- 批量重命名和批量水印只扫描所选目录第一层，不递归子目录
- 批量格式转换不会自动添加后缀；在同一目录输出相同格式时可能覆盖源文件，建议使用独立输出目录
- HEIC 仅支持读取；PSD 仅解码合成图，不保留图层

## 文档

- [用户手册](https://gitee.com/open-release/image-viewer/blob/master/doc/imageviewer-user-manual.md)
- [特色功能](https://gitee.com/open-release/image-viewer/blob/master/doc/imageviewer-features.md)
- [基础版 / 专业版与离线授权](https://gitee.com/open-release/image-viewer/blob/master/doc/edition-guide.md)
- [命令行工具说明](https://gitee.com/open-release/image-viewer/blob/master/doc/imageviewutils-guide.md)
- [隐私策略](https://gitee.com/open-release/image-viewer/blob/master/doc/privacy-policy.md)

## 反馈

如遇到图像无法显示、格式识别错误、批处理失败或激活问题，请通过以下方式反馈：

- Gitee Issues：<https://gitee.com/open-release/image-viewer/issues>
- 邮箱：<starsgreen@sina.com>

反馈时请附上操作系统版本、图片格式与尺寸、复现步骤，以及相关日志或错误信息。
