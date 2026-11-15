# ImageViewUtils 命令行工具使用说明

ImageViewUtils 是随 ImageViewer 提供的命令行工具集，用于在无 GUI 环境下完成图像格式转换、缩放、旋转和常见图片处理，适合脚本与自动化流水线。它不是交互式图像查看器。

支持格式取决于发行包包含的编解码组件。工具可处理 YUV、RGB、GRAY、BAYER 系列原始像素格式，以及 PNG、JPEG、BMP、TIFF 等常规图片格式；具体子命令可接受的输入以本文参数说明和各工具帮助为准。

---

## 目录

- [工具总览](#工具总览)
- [可执行文件位置](#可执行文件位置)
- [formattrans — 格式转换](#formattrans--格式转换)
- [scale — 图像缩放](#scale--图像缩放)
- [rotate — 图像旋转](#rotate--图像旋转)
- [imgproc — 一站式图片处理](#imgproc--一站式图片处理)
- [通用参数](#通用参数)
- [INI 配置（--cfg）](#ini-配置--cfg)
- [GPU 加速（--hw）](#gpu-加速--hw)
- [YUV 色彩矩阵（BT.601 / BT.709 / BT.2020）](#yuv-色彩矩阵bt601--bt709--bt2020)
- [注意事项](#注意事项)

---

## 工具总览

| 工具 | 功能 | 输入类型 | GPU 加速 |
|------|------|----------|----------|
| `formattrans` | 原始像素格式互转，可输出预览图 | 原始像素缓冲区，或 BMP/PPM/JPEG 文件 | `--hw` |
| `scale` | 原始像素图像缩放 | 原始像素缓冲区 | `--hw` |
| `rotate` | 原始像素图像旋转 | 原始像素缓冲区 | `--hw` |
| `imgproc` | 普通图片文件的格式转换、旋转、缩放 | PNG/JPEG/BMP/TIFF/WebP 等图片文件 | 无 |

`formattrans`、`scale`、`rotate` 面向**原始像素数据**，需要显式给出尺寸与格式（`--size`、`--if`）；`imgproc` 面向**普通图片文件**，自动识别输入格式并从输出扩展名推断目标格式。

---

## 可执行文件位置

命令行工具与图形界面位于同一目录：

```text
ImageViewer/
├── ImageViewApp.exe
├── formattrans.exe
├── scale.exe
├── rotate.exe
└── imgproc.exe
```

Windows 下为 `.exe`，Linux 下没有扩展名。在 Windows 上直接运行帮助命令：

```powershell
.\formattrans.exe --help
```

在 Linux 上打开终端，切换到程序目录后运行：

```bash
./formattrans --help
```

如果单独复制工具到其他目录，请同时保留其依赖的运行库。

---

## formattrans — 格式转换

在原始像素格式之间转换，支持 RGB↔YUV、YUV↔YUV、RGB↔RGB 等组合。

| 参数 | 说明 |
|------|------|
| `-i, --in <file>` | 输入文件 |
| `-o, --out <file>` | 输出文件 |
| `-s, --size WxH` | 输入图像尺寸（必填） |
| `--if <FORMAT>` | 输入格式，如 `NV12`、`RGB24`、`RGBA` |
| `--of <FORMAT>` | 输出格式，如 `NV12`、`RGB24`、`RGBA` |
| `--pic <file>` | 同时输出预览图（见下方规则） |
| `--hw` | 使用 OpenGL GPU 加速 |
| `--cfg <file>` | 从 INI 文件读取参数 |
| `--perf` | 结束后打印性能统计 |
| `--perf-trace <file>` | 记录 Perfetto/Chrome JSON trace |
| `-h, --help` | 显示帮助 |

```bash
# 原始像素：RGB24 → NV12
formattrans -i input.rgb -s 1920x1080 --if RGB24 --of NV12 -o output.yuv

# GPU 加速
formattrans -i input.rgb -s 1920x1080 --if RGB24 --of NV12 -o output.yuv --hw

# NV12 → BGRA，并生成 BMP 预览图
formattrans -i input.yuv -s 640x480 --if NV12 --of BGRA -o output.rgb --pic preview.bmp

# 普通图片文件输入（BMP/PPM/JPEG），仍需 --size 与 --if
formattrans -i input.bmp -s 1920x1080 --if BMP --of RGBA -o output.rgba
```

`--pic` 只对以下组合生效：

| 预览扩展名 | 要求的 `--of` |
|------------|---------------|
| `.bmp` | `RGBA` |
| `.ppm` | `RGB24` |
| `.jpg` / `.jpeg` | `RGB24`、`RGBA`、`RGBX` |

---

## scale — 图像缩放

| 参数 | 说明 |
|------|------|
| `-i, --in <file>` | 输入文件 |
| `-o, --out <file>` | 输出文件 |
| `-s, --size WxH` | 输入图像尺寸（必填） |
| `--if <FORMAT>` | 输入/输出格式（缩放不改变格式） |
| `--os WxH` | 输出图像尺寸（必填） |
| `--hw` | 使用 OpenGL GPU 加速 |
| `--cfg <file>` | 从 INI 文件读取参数 |
| `--perf` | 结束后打印性能统计 |
| `--perf-trace <file>` | 记录 Perfetto/Chrome JSON trace |
| `-h, --help` | 显示帮助 |

```bash
# CPU 缩放
scale -i input.rgb -s 1920x1080 --if RGB24 --os 640x480 -o output.rgb

# GPU 加速缩放
scale -i input.rgb -s 1920x1080 --if RGB24 --os 640x480 -o output.rgb --hw
```

---

## rotate — 图像旋转

| 参数 | 说明 |
|------|------|
| `-i, --in <file>` | 输入文件 |
| `-o, --out <file>` | 输出文件 |
| `-s, --size WxH` | 输入图像尺寸（必填） |
| `--if <FORMAT>` | 输入/输出格式（旋转不改变格式） |
| `-a, --angle <度>` | 旋转角度，正值为顺时针；支持 0/90/180/270 及任意角度 |
| `--hw` | 使用 OpenGL GPU 加速 |
| `--cfg <file>` | 从 INI 文件读取参数 |
| `--perf` | 结束后打印性能统计 |
| `--perf-trace <file>` | 记录 Perfetto/Chrome JSON trace |
| `-h, --help` | 显示帮助 |

90°/270° 旋转时输出宽高自动交换，无需手动指定输出尺寸。

```bash
# CPU 旋转 90°
rotate -i input.rgb -s 1920x1080 --if RGB24 --angle 90 -o output.rgb

# GPU 加速旋转
rotate -i input.rgb -s 1920x1080 --if RGB24 --angle 90 -o output.rgb --hw
```

---

## imgproc — 一站式图片处理

直接处理普通图片文件，可在一次调用中组合格式转换、旋转和缩放。注意它的参数风格与上面三个工具不同：`-h` 表示高度，帮助是 `-?` / `--help`。

| 参数 | 说明 |
|------|------|
| `-i, --in <file>` | 输入图片文件（自动识别格式） |
| `-o, --out <file>` | 输出文件（扩展名决定格式） |
| `-f, --format <name>` | 目标格式：`jpeg`、`png`、`bmp`、`tiff`、`ppm`、`gif`、`webp`（可选）、`heif`（可选） |
| `-r, --rotate <度>` | 旋转角度（0/90/180/270 或任意角度） |
| `-w, --width <px>` | 目标宽度；只给宽度时保持宽高比 |
| `-h, --height <px>` | 目标高度；只给高度时保持宽高比 |
| `-s, --scale <百分比>` | 缩放百分比（1-200，`50` 表示缩小一半） |
| `-v, --verbose` | 输出详细过程 |
| `-?, --help` | 显示帮助 |

```bash
# 格式转换
imgproc -i photo.jpg -o photo.webp

# 旋转 + 转换
imgproc -i photo.png -o photo.jpg --rotate 90

# 按宽度生成缩略图
imgproc -i photo.jpg -o thumb.jpg --width 320

# 组合操作
imgproc -i photo.jpg -o photo.webp --rotate 90 --width 800 --format webp
```

---

## 通用参数

以下参数适用于 `formattrans`、`scale`、`rotate`：

| 参数 | 说明 |
|------|------|
| `-i, --in <file>` | 输入文件路径 |
| `-o, --out <file>` | 输出文件路径 |
| `-s, --size WxH` | 输入图像尺寸，格式为 `<宽度>x<高度>` |
| `--if <FORMAT>` | 输入图像格式（如 `NV12`、`RGB24`、`RGBA`） |
| `--hw` | 启用 OpenGL GPU 硬件加速 |
| `--cfg <file>` | 从 INI 配置文件读取参数 |
| `--perf` | 结束后打印性能统计 |
| `--perf-trace <file>` | 记录 Perfetto/Chrome JSON trace |
| `-h, --help` | 显示帮助 |

性能统计也可通过环境变量启用：`PERF_STAT=1` 打开统计，`PERF_TRACE=<file>`（或 `1` 表示 `perf_trace.json`）记录 trace；`--perf` 与 `--perf-trace` 是单次运行的等价开关。

---

## INI 配置（--cfg）

`--cfg` 读取的是工具参数文件，与 YUV 色彩矩阵使用的 `imageConfig.txt` 是两个不同的文件。示例见 `ImageViewUtils/common/config.ini`。

各工具使用的 section 与键名：

```ini
[TRANSFER]
width=640
height=480
srcFormat=NV12
dstFormat=RGBA
infile=input.yuv
outfile=output.rgba

[SCALE]
width=640
height=480
dstWidth=320
dstHeight=240
format=RGB24
infile=input.rgb
outfile=output.rgb

[ROTATE]
width=640
height=480
format=RGB24
angle=90
infile=input.rgb
outfile=output.rgb
```

参数按出现顺序生效，后出现的项覆盖前面的项；因此建议把 `--cfg` 放在最前面，再用命令行参数覆盖个别项。

---

## GPU 加速（--hw）

- `formattrans`：源格式先由 GPU 转成 RGBA，再转成目标格式；GPU 初始化或转换失败时自动回退 CPU。
- `scale` / `rotate`：先由 GPU 把源格式转成 RGBA，再在 RGBA 上执行缩放/旋转，最后转回原格式；失败同样自动回退 CPU。
- GPU 路径使用无头 OpenGL 上下文，不需要可见窗口；在无 GPU/驱动不可用的环境会打印提示并回退。
- `imgproc` 没有 `--hw`，始终走 CPU。
- 版本限制：`--hw` 仅专业版生效。基础版会打印「GPU acceleration requires the Pro edition; falling back to CPU.」并自动回退 CPU，不会报错退出。详见 [基础版/专业版与离线授权说明](edition-guide.md)。

---

## YUV 色彩矩阵（BT.601 / BT.709 / BT.2020）

YUV 与 RGB 互转默认使用 BT.601（limited range）。矩阵只能通过环境变量或配置文件选择，工具本身不提供 `--matrix` 之类的命令行开关：

1. 环境变量 `IMAGE_YUV_COLOR_MATRIX`（优先级最高）
2. 进程工作目录下 `imageConfig.txt` 的 `yuvColorMatrix=...`
3. 默认 `bt601`

```bash
# 环境变量（Windows PowerShell）
$env:IMAGE_YUV_COLOR_MATRIX = "bt709"
formattrans -i in.nv12 -s 1920x1080 --if NV12 --of RGB24 -o out.rgb

# Linux / macOS
IMAGE_YUV_COLOR_MATRIX=bt2020 formattrans -i in.nv12 -s 1920x1080 --if NV12 --of RGB24 -o out.rgb
```

```ini
# imageConfig.txt（放在进程工作目录）
yuvColorMatrix=bt709
```

取值 `bt601`、`bt709`、`bt2020`，大小写不敏感，也接受 `0`/`1`/`2`；无效值回退到下一级或默认值。矩阵在进程内首次转换时解析一次，CPU 与 GPU 路径同时生效。

---

## 注意事项

- `formattrans`、`scale`、`rotate` 不会自动推断原始像素尺寸，`--size` 必须与文件实际尺寸一致。
- NV12/P010/YUY2 等 4:2:0 / 4:2:2 格式要求宽高为偶数，否则转换会失败。
- `formattrans` 的普通文件输入仅限 BMP/PPM/JPEG；其他图片格式请先用 `imgproc` 或 GUI 转换。
- `formattrans` 的 `--pic` 受目标格式限制（见上文表格），组合不匹配时不会生成预览图。
- 输出文件不会自动创建父目录，请确保目录已存在。
- 处理大图时 GPU 路径可能受显存限制，失败会自动回退 CPU，可用 `--perf` 观察耗时。
